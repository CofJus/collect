## 1 Java线程模型

![](https://cdn.tobebetterjavaer.com/stutymore/thread-state-and-method-20230829143200.png)

### 1.1 阻塞、等待、超时等待的区别

|          | BLOCKED（阻塞）                     | WAITING（等待）         | TIMED_WAITING（超时等待）               |
| -------- | ----------------------------------- | ----------------------- | --------------------------------------- |
| 进入原因 | 竞争资源（**锁**）或I/O（文件读写） | 主动放弃CPU资源         | 主动放弃CPU资源 **并设置超时时间**      |
| 转变原因 | 被竞争资源的资源得到释放            | 需要其他线程唤醒        | 需要其他线程唤醒/**等待超时后自动唤醒** |
| 关键字   | synchronized/lock                   | wait/notify,park/unpark | sleep,wait(long)/notify,parkNanos       |

### 1.2 Java线程和操作系统线程的关系

Java 线程状态是在操作系统线程状态的基础上进行的抽象和封装。Java 线程的运行最终还是要依赖操作系统的线程调度和执行机制。

Java线程模型简化了操作系统的线程模型。

Java中的线程和操作系统的线程并非一一对应，JVM可以将多个Java线程的任务分配给一个操作系统线程。

### 1.3 并行 并发

### 1.4 进程 线程 协程

## 2 Java内存模型（JMM）

JMM（Java Memory Model）主要聚焦于多线程环境下的内存访问规则，是对 JVM 内存管理的一种抽象和规范补充。

它涵盖的主题包括变量的可见性、指令重排、原子操作等，旨在解决由于多线程并发编程带来的一些问题。

### 2.1 happens-before原则

面向开发者而非编译器，happens-before在多线程程序的时间轴上建立了一个先后顺序的约束。

指令重排必须遵守这些规则。

- 程序顺序规则：代码顺序执行，前面的代码 happens-before后面的代码

- 管程锁定规则：加锁 happens-before 解锁 （monitorenter happens-before monitorexit）
- volatile变量规则：写happens-before读
- 线程启动规则：start方法happens-before其他任何操作
- 传递性规则：顾名思义

### 2.2 工作内存

JMM则规定，在线程共享的主内存之外，每个线程持有一块私有的工作内存。

主内存负责存储对象实例、静态变量等数据，而线程对变量的操作（如读取、赋值等）都在自己的工作内存中进行。

JMM 中的主内存概念可以对应到 JVM 中的堆、方法区等共享内存区域，线程的工作内存则可以看作是线程栈以及部分缓存机制（如 CPU 缓存）的抽象结合。

Java 内存模型定义了包括read、write、lock、unlock、load、store等8种同步操作，来保证工作内存和主内存的正确交互。

### 2.3 JMM和JVM运行时内存区域

简而言之，一个抽象，一个具体。

JMM针对的是多线程环境下，如何在主内存与工作内存之间安全地执行操作。它是一种规范，一个模型。

JVM运行时内存区域则是具体详细的内存划分，比如堆、栈、方法区等等。

JMM 定义的规则需要通过 JVM 来具体实现。JVM 在实现过程中会利用硬件的内存架构和自身的内存管理机制来满足 JMM 的要求。

## 3 volatile

## 4 synchronized

## 5 CAS

## 6 AQS

## 7 ReentrantLock

## 8 ReentrantReadWriteLock

## 9 Condition

## 10 LockSupport

## 11 并发容器

## 12 ThreadLocal

## 13 线程池

## 14 unsafe

## 15 ForkJoinPool