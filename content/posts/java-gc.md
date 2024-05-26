+++
title = 'Java Garbage Collection'
date = 2024-05-26T10:45:40+01:00
draft = false
+++

Java’s Garbage Collector is one of the key features in the Java environment. When you develop in Java, the objects you instantiate are allocated in the heap of the Java Virtual Machine. In Java You don't have to think about how to allocate and déallocate objects in memory.


For an object that is no longer used, the Garbage Collector finds them and removes them from the Heap memory space.  Periodically, the GC scans the heap memory space to detect which ones are no longer used.

The process of garbage collection involve several steps :
- Marking: Garbage collection starts by scanning all the heap space and mark all object references used by the JVM. All not marked objects are candidates for garbage collection.
- Sweeping: Once the marking phase is done, the garbage collector sweeps the heap space and reclaims the memory that is used by the objects, the object is deallocated and the memory it used is returned to free memory space.
- Compacting: depending on which GC you use, the sweeping phase can be followed by a compaction process. Where the objects reference are grouped together to minimize the memory fragmentation.


Without garbage collection, the Heap would eventually run out of memory, leading to a runtime `OutOfMemoryError`.

There are two main types of Garbage Collection algorithms: Full Garbage collection and Incremental Garbage collection

**Full Garbage collection** is when the garbage collector _pauses_ the program’s execution and performs a search for garbage objects. 
All the heap space memory is scanned, the garbage collector marks and searches for garbage objects not used anymore in the app. 
During this phase application performance slowdown, caused by the GC pause. 
Full GC is usually triggered when there is not enough memory available or when the application’s memory consumption reaches a certain threshold.

**Incremental Garbage Collection**, avoid to _pause_ the program’s execution to perform the marking process. 
The garbage collector divides the scanning process into small, manageable pieces called "increments". 
During each increment, the garbage collector scans a portion of the program's memory, identifying any objects that are not needed and marking them as available for reuse.

I won't go into the details and describe all the CG events that exist. I'll save it for another time but we can still describe some interesting points on how the heap space is separated and what is and what’s implies `GC Stop the world (STW)` and `GC Pause`.

Memory management is a critical aspect in the JVM. The heap space is separated into different regions to optimize the garbage collection.

The memory space is separated in 3 parts, `Young generation`, `Old Generation` and `Permanent Generation`.

![JVM heap space representation](/jvm-heap-space.png)

### Young Generation

Young generation is the initial destination for newly created objects, especially those placed in the `eden` space. When `eden` becomes full, a minor garbage collection starts and marked objects are moved to the survivor space (`S0` and `S1`) and those no longer in use (`unmarked`) will be removed.

This pattern never stops, every time `eden` is full a minor garbage collection starts all over again and objects already located in S0 are moved to S1 survivor space. Objects located in S1 and have survived long enough (threshold depend on the GC algorithm) move to the old generation space.


_Remember_: Minor GC pause the application execution

### Old Generation

The old generation, also known as the tenured generation, stores long-lived objects.
Objects located in the young generation and survived (depending on the GC algorithm) are moved to the old generation space.

When an object is garbage collected in the old generation, it triggers a major garbage collection event. A major GC typically causes a pause in the application, the pause can be noticeable and may impact application performance, especially if the Old Generation has accumulated a significant amount of live data.

_Information_: Major GC only targets the old generation heap memory space.

### Permanent generation

Permanent generation is not populated by objects from the old generation. It doesn’t work like objects moved from the young generation to the old generation.

The permanent generation is populated by the JVM to store metadata about classes and methods (class metadata, interned strings and statics variables).

In `OpenJDK 17`, the permanent generation space has been replaced by a new metaspace, which is designed to be more efficient and flexible. `Metaspace` is not a contiguous heap area but is allocated in the native memory (to allow it to grow dynamically) and reduce `OutOfMemoryError` risks and improve performances, class loading/unloading can be more efficiently managed for applications that dynamically load many classes.


### The GC Stop the world event !

A GC stop-the-world is when the JVM halts the execution of all application threads to perform a GC operation, during a STW event no application code runs and all processing is dedicated to garbage collection. A SWT event happens during : minor & major GC and certain phases of concurrent GCs (initial mark / remark phases in CMS).

Any interruption of application execution due to garbage collection, implies GC pause. STW causes GC pause.

In most GC algorithms, a "Stop-the-World" (STW) is typically involved in a Full GC event.
Full GC reclaim memory across the entire heap space memory, including both young and old generations and update references and move objects. To ensure correctness and synchronization, the JVM must halt the code execution.

_Information_: A full GC occurs mainly when `old generation space` is exhausted or for an `allocation / promotion failure`.


I hope  you enjoyed this short introduction about how GC works.
I plan to go further into the internal operation of the JVM and share in detail about the JVM synchronization, safepoint, and concurrent mark sweep.


If I've made a mistake, please let me know :)  
