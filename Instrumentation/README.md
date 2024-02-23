# Instrumentation in OpenJDK

**Every section assumes `x86` architecture, if you are working on something else, the functions are similar but be careful with the registers you decide to use**

## Table of Contents
1. [Method entry/exit](#method-entryexit)
2. [Field accesses (interpreter)](#field-accesses-interpreter)
3. [Field accesses (ZGC)](#field-accesses-zgc)
4. [Locks](#chapter-3-jit-compilation)

---

## Method entry/exit 

- `src/hotspot/cpu/x86/interp_masm_x86.cpp`
```c++
void InterpreterMacroAssembler::notify_method_entry() {
  // Whenever JVMTI is interp_only_mode, method entry/exit events are sent to
   ...
    NOT_LP64(get_thread(rthread);)
    get_method(rarg);
    call_VM_leaf(CAST_FROM_FN_PTR(address, SharedRuntime::_method_entry),rthread, rarg); // can be any method here, but has to be a leaf
}
```
```c++
void InterpreterMacroAssembler::notify_method_exit(
    TosState state, NotifyMethodExitMode mode) {
  // Whenever JVMTI is interp_only_mode, method entry/exit events are sent to
  ...
	  push(state);
    NOT_LP64(get_thread(rthread);)
    get_method(rarg);
    call_VM_leaf(CAST_FROM_FN_PTR(address,             SharedRuntime::_method_exit),rthread, rarg);
}
```
The `SharedRuntime::_method_entry` and `SharedRuntime::_method_exit` must have the following signature:
```c++
void SharedRuntime::method_entry(JavaThread *thread, Method *method);
```

---

## Field accesses (interpreter)

- `src/hotspot/cpu/x86/templateTable_x86.cpp`
```c++
void TemplateTable::getfield_or_static(int byte_no, bool is_static, RewriteControl rc) {
  transition(vtos, vtos);

  const Register obj   = LP64_ONLY(c_rarg3) NOT_LP64(rcx);
  const Register cache = rcx;
  const Register index = rdx;
  const Register off   = rbx;
  const Register tos_state   = rax;
  const Register flags = rdx;
  const Register bc    = LP64_ONLY(c_rarg3) NOT_LP64(rcx); // uses same reg as obj, so don't mix them

  resolve_cache_and_index_for_field(byte_no, cache, index);
  jvmti_post_field_access(cache, index, is_static, false);
  load_resolved_field_entry(obj, cache, tos_state, off, flags, is_static);

  if (!is_static) pop_and_check_object(obj);

  const Address field(obj, off, Address::times_1, 0*wordSize);

  /*
   If you want to check field flags, copy flags register into another general purpose register.
   During testing, I found out that it gets corrupted by some calls later in the code.
   */

  // rdx is safe to use, as it is not used later
    __ movl(rdx, flags); 
  ...

  /*
    The following example tracks oops and ignores volatile oops
  */

   // atos
  do_oop_load(_masm, field, rax);
  __ push(atos);

  // you may not pass the method to the VM, but it is useful for debugging

  // both c_rarg0 and c_rarg1 are used to pass arguments to the VM
  // and are both general purpose registers
  __ leaq(c_rarg0, field); // load the field into c_rarg0 (register arg 0)
  __ get_method(c_rarg1); // load the method into c_rarg1 (register arg 1)

  // volatile check
  // you may also check for other flags, such as final, private, etc. by just ORing the rest of the flags.
  int32_t is_volatile = 1 << ConstantPoolCacheEntry::is_volatile_shift;

  __ testl(rdx, is_volatile);
  __ jcc(Assembler::notZero, volatile_field);

  __ call_VM_leaf(CAST_FROM_FN_PTR(address, SharedRuntime::field_load), c_rarg0, c_rarg1, rbcp);

  __ bind(volatile_field); // ignore volatile fields

  // Different accesses, according to the type of the field
  // See TosState for that
}
```

The `SharedRuntime::field_load` must have the following signature:
```c++
/* bcp can be used to get the exact bytecode index
through the index, you may get the exact line of code that caused the field access.
See Method::bci_from(bcp) and Method::line_number_from_bci(bci)
*/
void SharedRuntime::field_load(void *obj, Method *method, address bcp);
```

The same principle applies to stores.
```c++
void TemplateTable::putfield_or_static(int byte_no, bool is_static, RewriteControl rc);
```

- `src/hotspot/cpu/x86/templateTable_x86.cpp`

**Array accesses**
```c++
 // for 4 byte int loads from arrays
void TemplateTable::iaload();
// for 8 byte int (long) loads from arrays
void TemplateTable::laload(); 
// for object loads from arrays - for compressed oops the load is 4 bytes
void TemplateTable::aaload(); 
// for byte loads from arrays
void TemplateTable::baload(); 
// for char loads from arrays
void TemplateTable::caload();
// for short loads from arrays
void TemplateTable::saload(); 
// for 4 byte int stores to arrays
void TemplateTable::iastore();
// for 8 byte int (long) stores to arrays
void TemplateTable::lastore();
// for object stores to arrays - for compressed oops the store is 4 bytes
void TemplateTable::aastore();
// for byte stores to arrays
void TemplateTable::bastore();
// for char stores to arrays
void TemplateTable::castore();
// for short stores to arrays
void TemplateTable::sastore();
```
---

## Field accesses (ZGC)

- `ZGC` provides a higher level of abstraction for field accesses.
- It uses `barriers` to ensure that the `GC` can track the changes in the heap.
- Initially, `ZGC` used only **load barriers** and since `JDK 21`, it also uses **store barriers**. This happened because `ZGC` became generational.

### Warning
- `ZGC` **does not use barriers on all field accesses!**
- `ZGC` only uses barriers on `oop`'s (pointers) and **skips all primitive types**.
- `ZGC` **does not use barriers on every load/store**, **only on safepoints**. During testing I found that out of **20** field accesses, only **6** had barriers, but this is not necessarily the case for all applications. Furthe testing is required.

### Load barriers
- `src/hotspot/share/gc/z/zBarrier.inline.hpp`
```c++
inline zaddress ZBarrier::load_barrier_on_oop_field(volatile zpointer* p) {
  const zpointer o = load_atomic(p);

  const zaddress healed_addr = load_barrier_on_oop_field_preloaded(p, o);
  // it is important to use the healed address, after the barrier has been applied
  oop obj = to_oop(healed_addr);

  // sanity check for the object
  if (obj && Universe::heap()->is_oop(boj)) {
    // handle load
  }

  return healed_addr;
}
```

### Store barriers
- `src/hotspot/share/gc/z/zBarrier.inline.hpp`
```c++
inline void ZBarrier::store_barrier_on_heap_oop_field(volatile zpointer* p, bool heal) {
  const zpointer prev = load_atomic(p);

  auto slow_path = [=](zaddress addr) -> zaddress {
    return ZBarrier::heap_store_slow_path(p, addr, prev, heal);
  };

  zaddress healed_addr;

  if (heal) {
    healed_addr = barrier(is_store_good_fast_path, slow_path, color_store_good, p, prev);
  } else {
    healed_addr = barrier(is_store_good_or_null_fast_path, slow_path, color_store_good, nullptr, prev);
  }

  oop obj = to_oop(healed_addr);

  // sanity check for the object
  if (obj && Universe::heap()->is_oop(boj)) {
    // handle store
  }
}
```

```c++
inline void ZBarrier::store_barrier_on_native_oop_field(volatile zpointer* p, bool heal) {
  const zpointer prev = load_atomic(p);

  zaddress healed_addr;

  if (heal) {
    healed_addr = barrier(is_store_good_fast_path, native_store_slow_path, color_store_good, p, prev);
  } else {
    healed_addr = barrier(is_store_good_or_null_fast_path, native_store_slow_path, color_store_good, nullptr, prev);
  }

  // same logic as above

}
```