
# OpenJDK HotSpot Deep Dive

Welcome to the OpenJDK HotSpot Deep Dive repository. This README serves as a guide and an index to various resources, including articles, books, blogs, videos, and code samples, each focusing on different aspects of the OpenJDK HotSpot JVM.

## Table of Contents
1. [High Level Architecture](#chapter-1-high-level-architecture)
2. [Runtime System](#chapter-2-runtime-system)
3. [JIT Compilation](#chapter-3-jit-compilation)
4. [Garbage Collection](#chapter-4-garbage-collection)
5. [Performance Tuning](#chapter-5-performance-tuning)
6. [Advanced Topics](#chapter-6-advanced-topics)

---

## Chapter 1: High Level Architecture
In this chapter, we explore the basics of OpenJDK HotSpot, its history, and its role in the Java ecosystem.

- **Articles**
  - [Introduction-to-HotSpot](https://www.infoq.com/articles/Introduction-to-HotSpot/)
  - [JVMInternals](https://blog.jamesdbloom.com/JVMInternals.html)
- **Books**
  - [Java Performance](https://www.amazon.com/Java-Performance-Charlie-Hunt/dp/0137142528) by Charlie Hunt
- **Blogs**
  - [HotSpot Internals for OpenJDK](https://openjdk.java.net/groups/hotspot/docs/HotSpotGlossary.html)
- **Videos**
  - [A JVM does that?](https://www.youtube.com/watch?v=-vizTDSz8NU) by Dr Cliff Click
- **Code**
  - [HotSpot Code](https://github.com/openjdk/jdk/tree/master/src/hotspot)

---

## Chapter 2: Runtime System
In this chapter, we explore the HotSpot VM Runtime environment.

- **Articles**
  ## Constant pool
  - [Understanding the constant pool inside a Java class file](https://blogs.oracle.com/javamagazine/post/java-class-file-constant-pool) by Andrew Binstock
  ## Class Metadata
  - [Java Objects Inside Out](https://shipilev.net/jvm/objects-inside-out/) by Aleksey Shipilёv
  ## Bytecode Interpreter
  - [A Matter of Interpretation: From Bytecodes to Machine Code in the JVM](https://www.azul.com/blog/a-matter-of-interpretation-from-bytecodes-to-machine-code-in-the-jvm/) by  Simon Ritter
  ## Method Invocation
  - [The Black Magic of (Java) Method Dispatch](https://shipilev.net/blog/2015/black-magic-method-dispatch/) by Aleksey Shipilёv
  - [JVM Anatomy Quark #16: Megamorphic Virtual Calls](https://shipilev.net/jvm/anatomy-quarks/16-megamorphic-virtual-calls/) by Aleksey Shipilёv
  - [Mastering the mechanics of Java method invocation](https://blogs.oracle.com/javamagazine/post/mastering-the-mechanics-of-java-method-invocation) by Ben Evans
  - [Overview of CompiledIC and CompiledStaticCall](https://wiki.openjdk.org/display/HotSpot/Overview+of+CompiledIC+and+CompiledStaticCall) OpenJDK Wiki
  - [Inline_caching](https://en.wikipedia.org/wiki/Inline_caching) Wikipedia
  ## Synchronization
  - [Java Memory Model Pragmatics](https://shipilev.net/blog/2014/jmm-pragmatics/) by Aleksey Shipilёv
  - [Close Encounters of The Java Memory Model Kind](https://shipilev.net/blog/2016/close-encounters-of-jmm-kind/) by Aleksey Shipilёv
  - [On The Fence With Dependencies](https://shipilev.net/blog/2014/on-the-fence-with-dependencies/) by Aleksey Shipilёv
  - [All Fields Are Final](https://shipilev.net/blog/2014/all-fields-are-final/) by Aleksey Shipilёv
  ## Thread Management
  - [Stack Overflow handling in HotSpot JVM](https://pangin.pro/posts/stack-overflow-handling) by Andrei Pangin
- **Books**
  - [Java Performance](https://www.amazon.com/Java-Performance-Charlie-Hunt/dp/0137142528) by Charlie Hunt
- **Blogs**
- **Videos**
- **Code**

---

## Chapter 3: JIT Compilation
This chapter delves into the Just-In-Time (JIT) compilation process of HotSpot, including how Java code is compiled into machine code at runtime.

- **Articles**
  - [How Tiered Compilation works in OpenJDK](https://devblogs.microsoft.com/java/how-tiered-compilation-works-in-openjdk/) by Cesar Soares
  - [How the JIT compiler boosts Java performance in OpenJDK](https://developers.redhat.com/articles/2021/06/23/how-jit-compiler-boosts-java-performance-openjdk) by Roland Westrelin
  - [What the JIT!? Anatomy of the OpenJDK HotSpot VM](https://www.infoq.com/articles/OpenJDK-HotSpot-What-the-JIT/) by Monica Beckwith
  - [Understanding Java JIT Compilation with JITWatch](https://www.oracle.com/technical-resources/articles/java/architect-evans-pt1.html) by Ben Evans
- **Books**
  - [Java Performance](https://www.amazon.com/Java-Performance-Charlie-Hunt/dp/0137142528) by Charlie Hunt
- **Blogs**
- **Videos**
  - [The Sea of Nodes and the HotSpot JIT](https://www.youtube.com/watch?v=98lt45Aj8mo) by Dr. Cliff Click
- **Code**
  - [JitWatch](https://github.com/AdoptOpenJDK/jitwatch/) 

---

## Chapter 4: Garbage Collection
Explore the garbage collection mechanisms in HotSpot, including different GC algorithms and their impact on application performance.

- **Articles**
  - [Tracing garbage collection](https://en.wikipedia.org/wiki/Tracing_garbage_collection) Wikipedia
  - [Java garbage collection: The 10-release evolution from JDK 8 to JDK 18](https://blogs.oracle.com/javamagazine/post/java-garbage-collectors-evolution) by Thomas Schatzl
  - [Understanding Garbage Collectors](https://blogs.oracle.com/javamagazine/post/understanding-garbage-collectors) by Christine H. Flood
  - [Do It Yourself (OpenJDK) Garbage Collector](https://shipilev.net/jvm/diy-gc/) by Aleksey Shipilёv 
  - [Stages and levels of Java garbage collection](https://developers.redhat.com/articles/2021/08/20/stages-and-levels-java-garbage-collection) RedHat
  - [How the JVM uses and allocates memory ](https://developers.redhat.com/articles/2021/09/09/how-jvm-uses-and-allocates-memory) RedHat
  - [How to choose the best Java garbage collector](https://developers.redhat.com/articles/2021/11/02/how-choose-best-java-garbage-collector) RedHat
  - [The Inner Workings of Safepoints](https://mostlynerdless.de/blog/2023/07/31/the-inner-workings-of-safepoints/) by Johannes Bechberger
  - [Where is my safepoint?](https://psy-lob-saw.blogspot.com/2014/03/where-is-my-safepoint.html) by Nitsan Wakart
  - [Safepoints: Meaning, Side Effects and Overheads](https://psy-lob-saw.blogspot.com/2015/12/safepoints.html) by Nitsan Wakart
  - [JVM Anatomy Quark #22: Safepoint Polls](https://shipilev.net/jvm/anatomy-quarks/22-safepoint-polls/) by Aleksey Shipilёv
  - [The JVM Write Barrier - Card Marking](https://psy-lob-saw.blogspot.com/2014/10/the-jvm-write-barrier-card-marking.html) by Nitsan Wakart
  - [Card Table Card Size Shenanigans](https://tschatzl.github.io/2022/02/15/card-table-card-size.html) by Thomas Schatzl
  - [JVM Pauses - It's more than GC](https://blanco.io/blog/jvm-safepoint-pauses/) by Zac Blanco
- **Books**
  - [The Garbage Collection Handbook. The Art of Automatic Memory Management (2023, CRC Press)](https://www.amazon.com/Garbage-Collection-Handbook-International-Perspectives/dp/1032218037) by Richard Jones, Antony Hosking, Eliot Moss 
- **Blogs**
- **Videos**
- **Code**
  - [HotSpot GC code](https://github.com/openjdk/jdk/tree/master/src/hotspot/share/gc)

---
