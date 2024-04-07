+++
title = 'Foreign Function and Memory API (FFM)'
date = 2024-04-07T14:10:40+01:00
draft = false
+++

With the JDK 22 and the JEP 424, I discovered a new feature  : Foreign Function and Memory API (FFM).

Before diving into, let me introduce the motivations and goals behind this. Java Platform offers a rich foundation of tools, libraries and drivers (JDBC, HTTP, NIO, sockets), but sometimes developers have to deal with resources outside the JVM.
FFM was created to interoperate with code and data outside of the Java runtime. By efficiently invoking foreign functions, and by safely accessing foreign memory, the API enables Java programs to call native libraries and process native data without the brittleness and danger of JNI.

Basically, the FFM API resides in the `java.lang.foreign` package of the `java.base` module.


Developer, allocate access and manage foreign memory and communicate outside the JVM. 

`MemorySegment` represents a contiguous region of memory, while `MemoryAddress` provides a pointer to a specific memory location within a segment. 
`SegmentAllocator` interface allows to allocate memory segments with specific characteristics, such as size and alignment. 

To call a foreign function developer have `Linker, FunctionDescriptor, and SymbolLookup`


## How to use it

### **Instantiate the Linker**

The Linker plays a vital role in enabling Java programs to communicate outside the JVM. Most of the time the developer will use the nativeLinker(), an optimized version for OS and Processor architecture. This ensures compatibility and efficiency when interacting with native libraries and functions.
However, if your project has specific requirements or you're exploring alternative options, you may consider third-party native linkers. Be sure to evaluate them based on factors such as platform compatibility, performance, ease of use, community support, and reliability.

### **Find symbols from native libraries** 

Symbols are functions or variables defined in external libraries that you want to access from your Java code.

To do this there are 3 steps :
- get the address of a symbol in one or more libraries.
- specify the signature and calling convention of the function you want to invoke. `FunctionDescriptor` interface defines the parameters and return type of the function, as well as other characteristics such as calling conventions
- Once you have obtained the address of the symbol and defined its descriptor, you can transform it into a `MethodHandle` using the `MethodHandles API`

Example for a function that takes two integers as parameters and returns a double and his transformation to `MethodHandle` allows to invoke the function in java code

```java 
// Define the parameter types and return type
MemoryLayout intLayout = MemoryLayouts.JAVA_INT; // Represents an int
MemoryLayout doubleLayout = MemoryLayouts.DOUBLE; // Represents a double

// Define the FunctionDescriptor
FunctionDescriptor descriptor = FunctionDescriptor.of(CLinker.C_DOUBLE, intLayout, intLayout);

MethodHandle handle = lookup.lookupFunction("myFunction", descriptor); 
```

### Allocate Memory Area
To interact with external libraries that require manual memory management FFM allows developers to allocate and manage memory outside the Java Heap.
Without this, parameters may not be readable for the underlying function, as the necessary memory space does not exist within the Java runtime. 

In reality, developers need to store pointers to data, rather than the data itself. 
This can be cumbersome, especially when dealing with complex data structures like lists, as each item in the list must be individually allocated.

### Invoke the function 

Invoke the function and pass the Memory Area containing the parameters.
Thanks to the `MethodHandle` you can now invoke it.

Suppose you have a native function `myFunction` defined in an external library that takes two integers as parameters and returns a double. 

```java 
double result = (double) handle.invoke(arg1, arg2);
```

> Note :There is no guarantee that the asType call is actually made. If the JVM can predict the results of making the call, it may perform adaptations directly on the caller's arguments, and call the target method handle according to its own exact type. see : https://wiki.openjdk.org/display/HotSpot/Deconstructing+MethodHandles

### (IF needed !) read the result in of heap memory Area
If, as with the `radixsort` function, there are no returns and the inserted data is modified directly, you should re-read the data in the (same :)) Area memory.
(as is in the example below)

You will find here the example shared in the JEP, hope this will let you more the concepts : 
```java 
// 1. Find foreign function on the C library path
Linker linker = Linker.nativeLinker();
SymbolLookup stdlib = linker.defaultLookup();
MethodHandle radixSort = linker.downcallHandle(
        stdlib.lookup("radixsort"), ...);
// 2. Allocate on-heap memory to store four strings
String[] javaStrings   = { "mouse", "cat", "dog", "car" };
// 3. Allocate off-heap memory to store four pointers
SegmentAllocator allocator = SegmentAllocator.implicitAllocator();
MemorySegment offHeap  = allocator.allocateArray(ValueLayout.ADDRESS, javaStrings.length);
// 4. Copy the strings from on-heap to off-heap
for (int i = 0; i < javaStrings.length; i++) {
// Allocate a string off-heap, then store a pointer to it
MemorySegment cString = allocator.allocateUtf8String(javaStrings[i]);
    offHeap.setAtIndex(ValueLayout.ADDRESS, i, cString);
}
// 5. Sort the off-heap data by calling the foreign function
        radixSort.invoke(offHeap, javaStrings.length, MemoryAddress.NULL, '\0');
// 6. Copy the (reordered) strings from off-heap to on-heap
for (int i = 0; i < javaStrings.length; i++) {
MemoryAddress cStringPtr = offHeap.getAtIndex(ValueLayout.ADDRESS, i);
javaStrings[i] = cStringPtr.getUtf8String(0);
}
        assert Arrays.equals(javaStrings, new String[] {"car", "cat", "dog", "mouse"});  // true
```

You can go further in Memory management (sessions, segment, (un)safety) and Looking up foreign functions with openGL lookup example here : https://openjdk.org/jeps/424

As we can see here, the process is simpler than with JNI integration with less boilerplate code, Simplified API, lightweight memory management and  type safety.

I personally hope this will be generalized more and used by most java developers, who today tend to over-eng their projects with dependencies that aren't always necessary, reducing the overall safety of their services. 


I'd like to take this opportunity to share some feedback from the RocksDb teams who tested FFM : https://rocksdb.org/blog/2024/02/20/foreign-function-interface.html

Overall, Panama/FFI offers benefits such as improved performance, reduced boilerplate code, and enhanced interoperability, making it a promising technology for enhancing the RocksDB Java API and enabling new opportunities in the future.

Hope you enjoyed this short introduction about FFM with some prémise of Panama’s Foreign-Memory Access API concepts. If I made a mistake let me know and contact me, nobody is perfect. Soon I'd like to share some discoveries about JRE and Memory Areas, see ya :) 
