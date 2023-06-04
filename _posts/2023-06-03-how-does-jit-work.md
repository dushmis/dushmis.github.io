---
layout: post
title: "Understanding Java JIT and Compilation"
categories: java
author:
- Dushyant 
meta: "java"
---


Java, a stalwart language of enterprise software development, has a unique design philosophy centered around write once, run anywhere. To achieve this, Java uses the concept of bytecode and a virtual machine to run the compiled code. This post will delve into the Java Just-In-Time (JIT) compilation process, its advantages, and how to debug and observe JIT in action.

## What is Bytecode?

In Java, when you compile your source code, it does not get translated directly into machine code, as is the case with languages like C or C++. Instead, Java compiles your source code into an intermediate form called bytecode, which is platform-independent. The bytecode files are commonly seen with the `.class` extension, which is run by the Java Virtual Machine (JVM) on your machine.

## The Java Virtual Machine (JVM)

The JVM is a key component in Java's write once, run anywhere philosophy. It is responsible for taking the platform-independent bytecode and translating it to the machine code that the underlying hardware understands. The JVM comes in different flavors for different operating systems, but all of them know how to execute Java bytecode.

## The JIT Compiler

The JVM initially interprets the bytecode, which is relatively slower. However, to improve performance, modern JVMs employ a Just-In-Time (JIT) compiler. JIT compilers compile bytecode into machine code just in time for execution. This is done dynamically at runtime, hence the name.

The JVM identifies the frequently executed parts of the bytecode, also known as hotspots. It then compiles these parts into machine code, optimizing the program's performance. The machine code is directly executable by the system's CPU, which makes it much faster than interpreted bytecode.

## The JIT Compilation Process

Here's a basic Java program for demonstration:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

When you compile this code using `javac HelloWorld.java`, you get **HelloWorld.class** containing the bytecode. You can use `javap -c HelloWorld` to disassemble and see the bytecode:

```java
Compiled from "HelloWorld.java"
public class HelloWorld {
  public HelloWorld();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #3                  // String Hello, World!
       5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

This bytecode is then interpreted or compiled to machine code by JVM during execution.

## The JIT Compilation Process

To see JIT compilation at runtime, you can enable debugging options provided by JVM. Here's how to do it:

Run the java program with the following options:

```java
java -XX:+UnlockDiagnosticVMOptions -XX:+PrintCompilation -XX:+LogCompilation HelloWorld
```

The **-XX:+PrintCompilation** flag will print the methods that the JVM compiles during execution. **-XX:+LogCompilation** logs more detailed information about the compiled methods, including the optimizations that the JIT compiler applies.

Here's an example of what you might see with **-XX:+PrintCompilation**:

```java
251  1       3       java.lang.String::hashCode (64 bytes)
320  2       3       java.lang.String::charAt (33 bytes)
455  3       3       HelloWorld::main (9 bytes)
```

This shows the order of method compilations (the first column), the compilation level (the second column), the method compiled, and the size of the method in bytes.

## Understanding On-Stack Replacement (OSR)

OSR, as applied in Java's JIT compilation process, enables the JVM to optimize code during execution, rather than waiting for a method to finish executing before applying optimizations. By identifying hotspots within loops or long-running code segments, OSR allows the JIT compiler to suspend the method's execution, perform additional optimizations, recompile the code, and replace it on the call stack. This on-the-fly optimization adapts to the runtime behavior of the code, potentially unlocking significant performance improvements.

Let's illustrate the process with a simple example. Consider the following Java code snippet:

```java
public int calculateSum() {
    int sum = 0;
    for (int i = 0; i < 100000; i++) {
        sum += i;
    }
    return sum;
}
```

In this scenario, the calculateSum() method calculates the sum of numbers from 0 to 99999. As the loop executes repeatedly, the JVM can recognize the hotspot and trigger OSR. At this point, the JIT compiler suspends the execution, applies further optimizations such as loop unrolling or inlining, recompiles the code, and replaces it on the call stack. Consequently, subsequent iterations of the loop execute with the optimized version, potentially leading to improved performance.

Examining PrintCompilation and Its Flags:
To gain insights into JIT optimizations, including OSR events, the JVM provides a useful diagnostic flag called **PrintCompilation**. When enabled, it prints information about JIT compilation activities and the methods being compiled. Additionally, it reveals various flags denoting the specific optimizations applied during the compilation process.

Here are some of the flags printed by **PrintCompilation**:

- **!:** Indicates that the method is interpreted, not compiled.
- **s:** Indicates that the method has been compiled in a tiered compilation setup.
- **c:** Denotes a method compiled without tiered compilation.
- **b:** Marks a method compiled with full optimization.
- **m:** Signifies that the method was compiled with OSR support.
- **o:** Indicates a method compiled with on-stack replacement (OSR) compilation.

By observing these flags, developers can gather valuable information about the JIT compilation process, including whether OSR optimizations were applied to specific methods.


Apart from On-Stack Replacement (OSR), Just-in-Time (JIT) compilers employ several other optimization techniques to improve the performance of Java code. Here are a few notable optimizations commonly applied by JIT compilers:

### Method Inlining:
Inlining is a powerful optimization technique where a method call is replaced with the actual code of the called method. By eliminating the overhead of method invocation, inlining reduces the cost of method calls and enables further optimizations. Inlining is particularly beneficial for small, frequently called methods, as it reduces the overhead of method dispatch and parameter passing.

### Loop Unrolling:
Loop unrolling is a technique where the compiler replicates the loop body multiple times, reducing the number of loop iterations. By eliminating loop control overhead, such as loop counters and condition checks, loop unrolling can improve performance. It also allows the compiler to apply other optimizations, such as instruction scheduling and register allocation, more effectively.

### Common Subexpression Elimination (CSE):
CSE is an optimization that identifies and eliminates redundant computations. It recognizes when the same expression is computed multiple times within a method and replaces subsequent occurrences with a reference to the previously computed result. This optimization reduces the number of computations and improves code efficiency.

### Dead Code Elimination:
Dead code elimination involves removing portions of code that have no impact on the program's final result. This optimization identifies and eliminates unreachable or redundant code, reducing the program's execution time and memory footprint.

### Constant Folding and Propagation:
Constant folding replaces constant expressions with their computed values at compile time, eliminating the need for runtime computation. Constant propagation further extends this optimization by replacing variables with their constant values whenever possible, improving code performance and reducing overhead.

### Escape Analysis:
Escape analysis is a technique that determines whether objects created within a method stay confined within that method or escape to other parts of the program. If objects are detected as not escaping, the compiler can apply further optimizations, such as stack allocation instead of heap allocation. This optimization reduces memory allocation overhead and improves garbage collection efficiency.

### Array Bounds Check Elimination:
Array bounds check elimination is an optimization that eliminates unnecessary bounds checking when accessing array elements. By analyzing array access patterns and ensuring they are within valid bounds, the JIT compiler can eliminate redundant checks, improving performance in array-intensive computations.

## Advantages of JIT Compilation

JIT compilation brings significant performance improvements. It translates the frequently executed parts of the bytecode into machine code, which runs much faster. Furthermore, the JIT compiler can apply various optimization techniques on the fly, such as inlining (replacing method calls with the method body), loop unrolling, and dead code elimination.

## Drawbacks of JIT Compilation

While JIT compilers improve performance, they also add complexity to the JVM. Furthermore, the compilation process consumes CPU and memory resources, which may not be suitable for all situations. Also, the startup time of applications may be longer due to the additional compilation step.

## In Conclusion

Understanding the Java JIT compilation process is key to optimizing Java applications. Through the combination of interpretation and JIT compilation, Java manages to balance the startup time and execution speed of applications, while maintaining its write once, run anywhere philosophy. The provided debugging options can give valuable insights into the JIT compilation process and help tune the application for optimal performance.
