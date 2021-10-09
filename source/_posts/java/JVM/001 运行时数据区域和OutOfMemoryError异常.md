---
title: 001 运行时数据区域和OutOfMemoryError异常
date: 2020-06-09 10:52:19
tags: [Java, JVM]
categories: [Java, JVM]
top: false
---

本文主要讲解JVM运行时数据区域，并通过简单的案例来实现说明各个区域中的常见OOM异常。

<!-- more -->


# 运行时数据区域
运行时数据区域图：
![运行时数据区域图](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvm-rda.png)

Java内存区域：
JVM内存区域主要分为`线程私有区域`【程序计数器、虚拟机栈、本地方法栈】、`线程共享区域`【Java堆、方法区】、直接内存。
![JVM内存导图](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvm-mind.png)

- 线程私有区域：
线程私有数据区域生命周期与线程相同, 依赖用户线程的启动/结束 而 创建/销毁(在Hotspot VM内,每个线程都与操作系统的本地线程直接映射, 因此这部分内存区域的存/否跟随本地线程的生/死对应)。

- 线程共享区域：
线程共享区域随虚拟机的启动/关闭而创建/销毁。

![运行时数据区域图2](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvm-rda2.png)


# JVM主要区域溢出异常
在Java虚拟机规范中规定，除了`程序计数器`外，虚拟机的其他几个运行时区域都有发生OOM异常的可能，如：**方法区（运行时常量池）、Java堆、虚拟机栈（局部变量表）、本地方法栈和直接内存。**

下面将通过案例来验证各个运行时区域的溢出异常，并分析我们来如何解决和避免这些异常。

> 注：下面的代码基于**JDK8**进行开发测试；

## 线程独占区
### 程序计数器（无OOM）
程序计数器（Program Counter Register）是一块较小的内存空间, 是当前线程所执行的字节码的行号指示器，每条线程都要有一个独立的程序计数器，这类内存也称为`线程私有`的内存。
- 正在执行java方法的话，计数器记录的是虚拟机字节码指令的地址(当前指令的地址)。
- 如果是Native方法，则为空（undefined）。

这个内存区域是唯一一个在虚拟机中没有规定任何OutOfMemoryError 情况的区域。

### 虚拟机栈和本地方法栈溢出
#### 虚拟机栈
**虚拟机栈描述的是`Java方法`执行的线程内存模型**。

##### `栈帧（Stack Frame）`：
每个方法被执行的时候，Java虚拟机都会同步创建一个`栈帧`用于**存储局部变量表、操作数栈、动态连接、方法出口等信息**。
每一个方法被调用直至执行完毕的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。

![栈帧](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvm-stack.png)

##### `局部变量表`：
局部变量表存放了编译期可知的：
- 各种`Java虚拟机基本数据类型`：boolean、byte、char、short、int、 float、long、double；
- `对象引用` ：reference类型，它并不等同于对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或者其他与此对象相关的位置；
- `returnAddress类型`：指向了一条字节码指令的地址。

这些数据类型在局部变量表中的存储空间以`局部变量槽(Slot)`来表示。
**其中64位长度的long和double类型的数据会占用两个变量槽，其余的数据类型只占用一个**。
局部变量表所需的内存空间在编译期间完成分配，当进入一个方法时，这个方法需要在栈帧中分配多大的局部变量空间是完全确定 的，**在方法运行期间不会改变局部变量表的大小**。

##### 两种异常：栈/堆溢出
- 如果线程请求的栈深度大于虚拟机所允许的深度，将抛出`StackOverflowError异常`；
- 如果Java虚拟机栈容量可以动态扩展，当栈扩展时无法申请到足够的内存会抛出`OutOfMemoryError异常`。


#### 虚拟机栈&本地方法栈
Java虚拟机栈（Java Virtual Machine Stack）和本地方法栈（Native Method Stacks）非常相似，`都属于线程独占区`，区别是：
- 虚拟机栈为虚拟机执行`Java方法（也就是字节码）服务`；
- 本地方法栈为虚拟机使用到的`本地方法（Native方法）服务`；

**Hot-Spot虚拟机直接就把本地方法栈和虚拟机栈合二为一**。与虚拟机栈一样，本地方法栈也会在栈深度溢出或者栈扩展失败时分别抛出`StackOverflowError`和`OutOfMemoryError`异常。

#### 虚拟机栈和本地方法栈溢出案例
##### 栈容量参数：-Xss
HotSpot虚拟机中并不区分虚拟机栈和本地方法栈，因此对于HotSpot来说，`-Xoss参数`(设置本地方法栈大小)虽然存在，但实际上是没有任何效果的，`栈容量只能由-Xss参数来设定`。

##### 栈/堆溢出的场景
《Java虚拟机规范》明确允许Java虚拟机实现自行选择是否支持栈的动态扩展，而HotSpot虚拟机的选择是不支持扩展。
所以除非`在创建线程申请内存时就因无法获得足够内存而出现 OutOfMemoryError异常`，否则`在线程运行时是不会因为扩展而导致内存溢出的，只会因为栈容量无法容纳新的栈帧而导致StackOverflowError异常`。

##### 测试案例
将实验范围限制在**单线程中操作**，尝试下面两种行为是否能让HotSpot虚拟机产生OutOfMemoryError异常：
- 使用`-Xss参数减少栈内存容量`。
结果：抛出StackOverflowError异常，异常出现时输出的堆栈深度相应缩小。 

- `定义大量的本地变量，增大此方法帧中本地变量表的长度`。
结果：抛出StackOverflowError异常，异常出现时输出的堆栈深度相应缩小。 

###### 单线程下：使用`-Xss参数减少栈内存容量`（SOF异常）
```java
/**
 * Java虚拟机栈和本地方法机栈异常（单线程操作下）
 * VM Args: -Xss160k（Mac 64Bit要求最低栈内存为160K）
 * 使用-Xss参数减少栈内存容量。
 * 结果：抛出StackOverflowError异常，异常出现时输出的堆栈深度相应缩小。
 *
 * @author wugang
 * date: 2020-06-04 15:23
 **/
public class VMStackSOF {
    private int stackLength = 1;

    private void stackLeak() {
        stackLength++;
        stackLeak();
    }


    public static void main(String[] args) {
        VMStackSOF stackSOF = new VMStackSOF();
        try {
            stackSOF.stackLeak();
        } catch (Throwable e) {
            System.out.println("stack length: " + stackSOF.stackLength);
            throw e;
        }
    }
}
```

输出：
```
stack length: 773
Exception in thread "main" java.lang.StackOverflowError
	at com.bubble.jvm.error.VMStackSOF.stackLeak(VMStackSOF.java:16)
...
```

###### 单线程下：定义大量的本地变量，增大此方法帧中本地变量表的长度（SOF异常）
```java
/**
 * Java虚拟机栈和本地方法机栈异常（单线程操作下）
 * 定义大量的本地变量，增大此方法帧中本地变量表的长度
 * 结果：抛出StackOverflowError异常，异常出现时输出的堆栈深度相应缩小。
 *
 * @author wugang
 * date: 2020-06-04 15:23
 **/
public class VMStackSOF02 {
    private static int stackLength = 1;

    public static void test() {
        long unused1, unused2, unused3, unused4, unused5, unused6, unused7, unused8, unused9, unused10,
                unused11, unused12, unused13, unused14, unused15, unused16, unused17, unused18, unused19, unused20,
                unused21, unused22, unused23, unused24, unused25, unused26, unused27, unused28, unused29, unused30,
                unused31, unused32, unused33, unused34, unused35, unused36, unused37, unused38, unused39, unused40,
                unused41, unused42, unused43, unused44, unused45, unused46, unused47, unused48, unused49, unused50,
                unused51, unused52, unused53, unused54, unused55, unused56, unused57, unused58, unused59, unused60,
                unused61, unused62, unused63, unused64, unused65, unused66, unused67, unused68, unused69, unused70,
                unused71, unused72, unused73, unused74, unused75, unused76, unused77, unused78, unused79, unused80,
                unused81, unused82, unused83, unused84, unused85, unused86, unused87, unused88, unused89, unused90,
                unused91, unused92, unused93, unused94, unused95, unused96, unused97, unused98, unused99, unused100;
        stackLength++;
        test();
        unused1 = unused2 = unused3 = unused4 = unused5 = unused6 = unused7 = unused8 = unused9 = unused10 = unused11 =
                unused12 = unused13 = unused14 = unused15 = unused16 = unused17 = unused18 = unused19 = unused20 = unused21 = unused22 = unused23 = unused24 = unused25 = unused26 = unused27 = unused28 = unused29 = unused30 = unused31 = unused32 = unused33 = unused34 = unused35 = unused36 = unused37 = unused38 = unused39 = unused40 = unused41 = unused42 = unused43 = unused44 = unused45 = unused46 = unused47 = unused48 = unused49 = unused50 = unused51 = unused52 = unused53 = unused54 = unused55 = unused56 = unused57 = unused58 = unused59 = unused60 = unused61 = unused62 = unused63 = unused64 = unused65 = unused66 = unused67 = unused68 = unused69 = unused70 = unused71 = unused72 = unused73 = unused74 = unused75 = unused76 = unused77 = unused78 = unused79 = unused80 = unused81 = unused82 = unused83 = unused84 = unused85 = unused86 = unused87 = unused88 = unused89 = unused90 = unused91 = unused92 = unused93 = unused94 = unused95 = unused96 =
                        unused97 = unused98 = unused99 = unused100 = 0;
    }

    public static void main(String[] args) {
        try {
            test();
        } catch (Throwable e) {
            System.out.println("stack length: " + stackLength);
            throw e;
        }
    }
}
```
输出：
```
stack length: 8121Exception in thread "main" 
java.lang.StackOverflowError
	at com.bubble.jvm.error.VMStackSOF02.test(VMStackSOF02.java:26)
...
```

###### 多线程下：OOM异常
```java
/**
 * 多线程下，Java虚拟机栈和本地方法栈OOM异常
 * VM Args:-Xss2M
 *
 * @author wugang
 * date: 2020-06-04 16:38
 **/
public class VMStackOOM {

    private void dontStop() {
        while (true) {

        }
    }

    public void stackLeakByThread() {
        while (true) {
            Thread thread = new Thread(() -> dontStop());
            thread.start();
        }
    }

    public static void main(String[] args) {
        VMStackOOM vmStackOOM = new VMStackOOM();
        vmStackOOM.stackLeakByThread();
    }

}
```
输出：
```log
Exception in thread "main" java.lang.OutOfMemoryError: unable to create native thread
```

###### 结论
实验结果表明：
- 单线程下：
**无论是由于栈帧太大还是虚拟机栈容量太小，当新的栈帧内存无法分配的时候，HotSpot虚拟机抛出的都是StackOverflowError异常。**
如果在允许动态扩展栈容量大小的虚拟机上，相同代码则会导致不一样的情况，如第二个代码（定义大量的本地变量，增大此方法帧中本地变量表的长度）示例就会抛出OutOfMemoryError异常。

- 多线程下：
如果通过不断建立线程的方式，在HotSpot上也是可以产生内存溢出异常的，如第三个代码。
但是这样**产生的内存溢出异常和栈空间是否足够并不存在任何直接的关系**，**主要取决于操作系统本身的内存使用状态**。
甚至可以说，在这种情况下，`给每个线程的栈分配的内存越大，反而越容易产生内存溢出异常`。

> 因为：**操作系统分配给每个进程的内存是有限制的**。
> 如32位Windows的单个进程最大内存限制为2GB。
> HotSpot虚拟机提供了参数可以控制Java堆和方法区这两部分的内存的最大值，
> 那`剩余的内存（由虚拟机栈和本地方法栈来分配的内存）`为**2GB(操作系统限制)减去最大堆容量，再减去最大方法区容量**。
>> 注意：由于程序计数器消耗内存很小，可以忽略掉，如果把直接内存和虚拟机进程本身耗费的内存也去掉的话。
>> 
> 因此`为每个线程分配到的栈内存越大，可以建立的线程数量自 然就越少，建立线程时就越容易把剩下的内存耗尽`。

- 通过`减少内存`的手段来解决内存溢出的方式：
如果是建立过多线程导致的内存溢出，在不能减少线程数量或者更换64位虚拟机的情况下，就只**通过`减少最大堆`和`减少栈容量`来换取更多的线程**。

## 线程共享区
### Java堆溢出
#### 概念
Java堆（Java Heap）是虚拟机管理的内存中的最大一块区域，也是垃圾回收的主要区域，它属于`线程共享区`，用于`存储对象实例`。

#### Java堆时垃圾收集器管理的内存区域
Java堆也被称为GC堆（Garbage Collected Heap）。
- 从**回收内存角度**来看：
垃圾收集器大部分都是基于`分代收集理论`设计的，会有 **新生代、老年代、永久代（JDK8改为元空间）、Eden空间、From Survivor空间、To Survivor空间**等名词。
这些区域划分仅仅是一部分垃圾收集器的共同特性或者说设计风格，而非某个Java虚拟机具体实现的固有内存布局，更不是《Java虚拟机规范》里对Java堆的进一步细致划分。

- 从**分配内存角度**来看：
所有线程共享的Java堆中可以划分出多个线程私有的`分配缓冲区`（TLAB, Thread Local Allocation Buffer），可以提升对象分配时的效率。
不过无论从什么角度，无论如 何划分，都不会改变Java堆中存储内容的共性，无论是哪个区域，**存储的都只能是对象的实例**，将Java 堆细分的目的只是为了更好地回收内存，或者更快地分配内存。

#### 物理存储空间
Java堆可以处于**物理上不连续的内存空间**中，但在逻辑上它应该被视为连续的。
就像我们用磁盘空间去存储文件一样，并不要求每个文件都连续存放。
> 注意：但对于**大对象**(典型的如数组对象)，多数虚拟机实现出于实现简单、存储高效的考虑，很可能会要求连续的内存空间。

#### 配置参数
Java堆可以设置为固定大小，也可以为可动态扩展的。
通过参数`-Xmx`和`-Xms`来设定堆的最大和最小内存容量。**将堆的最小值-Xms参数与最大值-Xmx参数设置为一样即可避免堆自动扩展**。

如果**在Java堆中没有内存完成实例分配，并且堆也无法再扩展时**，Java虚拟机将会抛出`OutOfMemoryError异常`。

#### 为什么会堆溢出？
当我们不断地创建新对象时，并且使GC Roots到对象之间有可达路径来避免垃圾回收机制清除这些对象，这时候，对象数量不断增加，总容量超过最大堆的容量限制后，就会发生内存溢出异常。

#### OMM的两种情况
可以通过内存映像分析工具对dump出的堆存储快照进行分析。
首先应确认内存中导致OOM的对象是否是必要的，也就是要先分清楚到底是出现了内存泄漏(Memory Leak)还是内存溢出(Memory Overflow)。

##### 内存泄露（Memory Leak）
如果是内存泄漏（`内存中的对象不是必须存活的，垃圾收集器未收集`），可进一步通过工具查看泄漏对象到GC Roots的引用链，找到泄漏对象是通过怎 样的引用路径、与哪些GC Roots相关联，才导致`垃圾收集器无法回收它们`，根据泄漏对象的类型信息 以及它到GC Roots引用链的信息，一般可以比较准确地定位到这些对象创建的位置，进而找出产生内存泄漏的代码的具体位置。

##### 内存溢出（Memory Overflow）
如果不是内存泄漏，换句话说就是`内存中的对象确实都是必须存活的`，那就应当：
- 检查Java虚拟机的堆参数(-Xmx与-Xms)设置，与机器的内存对比，看看是否还有向上调整的空间。
- 再从代码上检查 是否存在某些对象生命周期过长、持有状态时间过长、存储结构设计不合理等情况，尽量减少程序运行期的内存消耗。

#### 溢出案例和dump快照分析
使用虚拟机参数：
```
VM Args:-Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError
```
限制Java堆的大小为20M B，不可扩展。
通过参数`-XX:+HeapDumpOnOutOf-MemoryError`可以**让虚拟机在出现内存溢出异常的时候Dump出当前的内存堆转储快照**，以便进行事后分析。(文件存储在该项目父目录下，如：java_pid80802.hprof)

```java
/**
 * Java堆内存异常测试
 * VM Args: -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError
 **/
public class JavaHeapOOM {
    private static class OOMObject {

    }

    public static void main(String[] args) {
        List<OOMObject> list = Lists.newArrayList();
        while (true) {
            list.add(new OOMObject());
        }
    }
}
```
输出：
```log
java.lang.OutOfMemoryError: Java heap space
Dumping heap to java_pid80802.hprof ...
Heap dump file created [27964242 bytes in 0.193 secs]
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at java.util.Arrays.copyOf(Arrays.java:3210)
	at java.util.Arrays.copyOf(Arrays.java:3181)
	at java.util.ArrayList.grow(ArrayList.java:265)
	at java.util.ArrayList.ensureExplicitCapacity(ArrayList.java:239)
	at java.util.ArrayList.ensureCapacityInternal(ArrayList.java:231)
	at java.util.ArrayList.add(ArrayList.java:462)
	at com.bubble.jvm.error.JavaHeapOOM.main(JavaHeapOOM.java:23)
```

可以在目录/Users/wugang/code/java/multi-dev下看到java_pid80802.hprof文件。

- 分析dump文件：
可以通过内存映像分析工具(如Eclipse Memory Analyzer)对Dump出来的堆转储快照进行分析，也可以通过JDK自带的工具jvisualvm来可视化分析。
```
# 控制行执行命令
jvisualvm
```
会打开下面的窗口，打开对应的堆dump文件：
![jvisualvm-起始页](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvisualvm-01.png)

主要内容为：
![jvisualvm-概要](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvisualvm-02.png)
由上图可知：导致 OutOfMemoryError 异常错误的线程是 main

查看dump的类信息：
![jvisualvm-类信息](https://cdn.jsdelivr.net/gh/bubblewu/cdn/images/jvm/jvisualvm-03.png)
由上图可知：dump文件记录的堆中的实例总大小约19M，指定的堆的固定大小为20M。
> 用第一行的实例大小除以百分比就能算出来：
> 堆中实例大小：12965216B/1024/1024=12.36M，占了总大小的65%。
> 堆中实例总大小：12.36M/0.65=19.02M

说明：dump文件中的实例列表其实是**反映了使用的堆的情况**，而使用的堆内存并没有达到预先设置的最大堆内存，只是在申请堆内存的过程中超出了预先设置的最大堆内存，然后内存溢出。

### 方法区和运行时常量池溢出
#### 方法区（Method Area）
`方法区`与Java堆一样，属于`线程共享区`，它用于**存储已被虚拟机加载的类型信息、常量、静态变量、即时编译器编译后的代码缓存**等数据。

《Java虚拟机规范》对方法区的约束是非常宽松的，除了和Java堆一样**不需要连续的内存**和**可以选择固定大小**或者**可扩展**外，甚至**还可以选择不实现垃圾收集**。

> 注意：
> - `关于方法区和永久代（元空间）`：
> 在JDK8以前，很多人把方法区称呼为`永久代（Permanent Generation`，或将两者混为一谈。
> 本质上这两者并不是等价的，**仅因为HotSpot使用永久代来实现方法区而已**，使得HotSpot的垃圾收集器能够像管理Java堆一样管理这部分内存，省去专门为方法区编写内存管理代码的工作。
> 到了JDK 7的HotSpot，已经把原本放在永久代的字符串常量池、静态变量等移出；
> 而到了JDK 8，完全废弃了永久代的概念，改用与JRockit、J9一样在本地内存中实现的`元空间(Metaspace)`来代替，把JDK 7中永久代还剩余的内容(主要是类型信息)全部移到元空间中。

##### 参数
###### JDK8之前：老年代（Permanent Gennration） 
- 如：`-XX:PermSize=10M ` 指定老年代的初始空间大小（10M），以字节为单位。
- 如：`-XX:MaxPermSize=10M` 设置老年代最大值，默认是-1，即不限制，或者说只受限于本地内存大小。

###### `JDK8始：元空间（Metaspace） `
**Metaspace使用的是本地内存，而不是堆内存**，也就是说在`默认情况下Metaspace的大小只与本地内存大小有关`。

- `-XX:MetaspaceSize`：指定**元空间的初始空间大小**，以字节为单位。
**达到该值就会触发垃圾收集进行类型卸载，同时收集器会对该值进行调整**：如果释放了大量的空间，就适当降低该值；如果释放了很少的空间，那么在不超过-XX:MaxMetaspaceSize(如果设置了的话)的情况下，适当提高该值。
> 该值越大触发Metaspace GC的时机就越晚。
> 随着GC的到来，虚拟机会根据实际情况调控Metaspace的大小，可能增加上限也可能降低。
> 在默认情况下，这个值大小根据不同的平台在12M到20M浮动。
> 使用`java -XX:+PrintFlagsInitial`命令查看本机的初始化参数，-XX:Metaspacesize为21810376B（大约20.8M） 。

- `-XX:MaxMetaspaceSize`：设置**元空间最大值**，默认是-1，即不限制，或者说只受限于本地内存大小。
> 防止因为某些情况导致Metaspace无限的使用本地内存，影响到其他程序。
> 在本机上该参数的默认值为4294967295B（大约4096MB）。

- `-XX:MinMetaspaceFreeRatio`：作用是在Metaspace GC收集之后，控制最小的元空间剩余容量的百分比，可减少因为元空间不足导致的垃圾收集的频率。
> 当进行过Metaspace GC之后，会计算当前Metaspace的空闲空间比。
> 如果空闲比小于这个参数，那么虚拟机将增长Metaspace的大小。
> 在本机该参数的默认值为40，也就是40%。
> 设置该参数可以控制Metaspace的增长的速度，太小的值会导致Metaspace增长的缓慢，Metaspace的使用逐渐趋于饱和，可能会影响之后类的加载。而太大的值会导致Metaspace增长的过快，浪费内存。

- `-XX:MaxMetaspaceFreeRatio`：用于控制大的元空间剩余容量的百分比。
> 当进行过Metaspace GC之后， 会计算当前Metaspace的空闲空间比，如果空闲比大于这个参数，那么虚拟机会释放Metaspace的部分空间。在本机该参数的默认值为70，也就是70%。

- `-XX:MaxMetaspaceExpansion`： Metaspace增长时的最大幅度。在本机上该参数的默认值为5452592B（大约为5MB）。
- `-XX:MinMetaspaceExpansion`： Metaspace增长时的最小幅度。在本机上该参数的默认值为340784B（大约330KB为）。


##### 运行时常量池（Runtime Constant Pool） 
**运行时常量池是方法区的一部分**。
Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是`常量池表(Constant Pool Table)`，用于**存放编译期生成的各种字面量与符号引用**，这部分内容将在类加载后存放到方法区的运行时常量池中。

运行时常量池相对于Class文件常量池的另外一个重要特征是具备**动态性**，Java语言并不要求常量一定只有编译期才能产生，在运行期间也可以将新的常量放入池中，如使用`String类的intern()方法`。

##### OOM异常
运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存时会抛出OutOfMemoryError异常。

#### 案例
HotSpot从JDK7开始逐步“去永久代”的计划，并在JDK8中完全**使用`元空间`来代替永久代**。

##### 方法区
```java
/**
 * 方法区的OOM异常：
 * - JDK8之前：指定老年代（方法区的大小固定为10M，不能进行自动扩展）
 * VM Args: -XX:PermSize=10M -XX:MaxPermSize=10M
 *
 * - JDK8：完全废除了老年代，用元空间代替。
 * VM Args: -XX:MetaspaceSize=10M -XX:MaxMetaspaceSize=10M
 *
 * @author wugang
 * date: 2020-06-04 18:44
 **/
public class JavaMethodAreaOOM {

    static class OOMObject {}

    /**
     * 借助CGLib使得方法区出现内存溢出异常：
     * 方法区的主要职责是用于存放类型的相关信息：如类名、访问修饰符、常量池、字段描述、方法描述等。
     * 对于这部分区域的测试，基本的思路是：运行时产生大量的类去填满方法区，直到溢出为止。
     * 所以：可以借助CGLib直接操作字节码，运行时生成了大量的动态类。
     * 注意：
     * 当前的很多主流框架，如Spring、Hibernate对类进行增强时，都会使用到 CGLib这类字节码技术，
     * 当增强的类越多，就需要越大的方法区以保证动态生成的新类型可以载入内存。
     */
    public static void main(String[] args) {
        while (true) {
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(OOMObject.class);
            enhancer.setUseCache(false);
            enhancer.setCallback(new MethodInterceptor() {
                @Override
                public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                    return methodProxy.invokeSuper(o, args);
                }
            });
            enhancer.create();
        }
    }

}
```
输出：
```log
Exception in thread "main" net.sf.cglib.core.CodeGenerationException: java.lang.reflect.InvocationTargetException-->null
	at net.sf.cglib.core.AbstractClassGenerator.generate(AbstractClassGenerator.java:348)
	at net.sf.cglib.proxy.Enhancer.generate(Enhancer.java:492)
	at net.sf.cglib.core.AbstractClassGenerator$ClassLoaderData.get(AbstractClassGenerator.java:117)
	at net.sf.cglib.core.AbstractClassGenerator.create(AbstractClassGenerator.java:294)
	at net.sf.cglib.proxy.Enhancer.createHelper(Enhancer.java:480)
	at net.sf.cglib.proxy.Enhancer.create(Enhancer.java:305)
	at com.bubble.jvm.error.JavaMethodAreaOOM.main(JavaMethodAreaOOM.java:39)
Caused by: java.lang.reflect.InvocationTargetException
	at sun.reflect.GeneratedMethodAccessor1.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at net.sf.cglib.core.ReflectUtils.defineClass(ReflectUtils.java:459)
	at net.sf.cglib.core.AbstractClassGenerator.generate(AbstractClassGenerator.java:339)
	... 6 more
Caused by: java.lang.OutOfMemoryError: Metaspace
	at java.lang.ClassLoader.defineClass1(Native Method)
	at java.lang.ClassLoader.defineClass(ClassLoader.java:763)
	... 11 more
```

##### 运行时常量池
```java
import java.util.HashSet;
import java.util.Set;

/**
 * 方法区（运行时常量池）的OOM异常，（方法区在JDK8后开始废除，之前也被称为永久代）
 * - 在JDK 6或更早之前的HotSpot虚拟机中，常量池都是分配在永久代中，
 * 我们可以通过-XX:PermSize和-XX:M axPermSize限制永久代的大小，即可间接限制其中常量池的容量。
 * 如：VM Args:-XX:PermSize=6M -XX:MaxPermSize=6M
 *
 * @author wugang
 * date: 2020-06-04 17:36
 **/
public class RuntimeConstantPoolOOM {

    /**
     * JDK6来运行代码，抛出异常：
     * Exception in thread "main" java.lang.OutOfMemoryError: PermGen space
     * at java.lang.String.intern(Native Method)
     * 说明运行时常量池是属于方法区(即JDK 6的HotSpot虚拟机中的永久代)的一部分。
     *
     * 注意：
     * 无论是在JDK7中继续使用-XX:MaxPermSize参数或者在JDK8及以上版本使用-XX:MaxMeta-spaceSize参数
     * 把方法区容量同样限制在6MB，也都不会重现JDK6中的溢出异常，循环将一直进行下去，永不停歇。
     *
     * 因为自JDK7起，原本存放在永久代的字符串常量池被移至Java堆之中，
     * 所以在JDK7及以上版本，限制方法区的容量对该测试用例来说是毫无意义的。
     *
     * 但使用-Xmx参数限制最大堆到6MB就能够看到下面两种运行结果之一，具体取决于哪里的对象分配时产生了溢出：
     * - OOM异常一：
     * Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
     * at java.base/java.lang.Integer.toString(Integer.java:440)
     * at java.base/java.lang.String.valueOf(String.java:3058)
     * - OOM异常二：
     * Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
     * at java.base/java.util.HashMap.resize(HashMap.java:699)
     * at java.base/java.util.HashMap.putVal(HashMap.java:658)
     * at java.base/java.util.HashMap.put(HashMap.java:607)
     * at java.base/java.util.HashSet.add(HashSet.java:220)
     *
     */
    private static void runByJDK6() {
        // 使用Set保持着常量池引用，避免Full GC回收常量池行为
        Set<String> set = new HashSet<>();
        // 在short范围内足以让6MB的PermSize产生OOM了
        short i = 0;
        while (true) {
            set.add(String.valueOf(i++).intern());
        }
    }

    public static void main(String[] args) {
        runByJDK6();
    }

}
```


##### `String::intern()方法`
`String::intern()`是一个Native方法，返回该对象在常量池中的引用。

```java
public native String intern();
```
作用：如果字符串常量池中已经包含一个等于该String对象的字符串，则返回代表池中这个字符串的String对象的引用；否则，会将此String对象包含的字符串添加到常量池中，并且返回此String对象的引用。

```java
    /**
     * - 在JDK 6中运行，会得到三个false；
     * 在JDK 6中，intern()方法会把首次遇到的字符串实例复制到永久代（方法区）的字符串常量池中存储，
     * 返回的也是永久代里面这个字符串实例的引用，而由StringBuilder创建的字符串对象实例在Java堆上，
     * 所以必然不可能是同一个引用，结果将返回false。
     *
     * - 而在JDK 7后中运行，会得到一个true、一个false和一个true；
     * 因为JDK7中，intern()方法实现就不需要再拷贝字符串的实例到永久代了，既然字符串常量池已经移到Java堆中，
     * 那只需要在常量池里记录一下首次出现的实例引用即可。
     * 因此intern()返回的引用和由StringBuilder创建的那个字符串实例就是同一个。
     * 而对str2比较返回false，这是因为java这个字符串在执行StringBuilder()之前就已经出现过了，(在加载sun.misc.Version这个类的时候进入常量池的)
     * 字符串常量池中已经有它的引用，不符合intern()方法要求“首次遇到”的原则，“JVM调优”这个字符串则是首次出现的，因此结果返回true。
     * 而str3和str1一样，"JDKJVM"这个字符串则是首次出现。
     *
     */
    private static void compare() {
        String str1 = new StringBuilder().append("JVM").append("调优").toString();
        System.out.println(str1.intern() == str1);
        // java这个字符串在执行StringBuilder()之前就已经出现过了，在加载sun.misc.Version这个类的时候进入常量池的。
        /*
         * 参考：https://www.zhihu.com/question/51102308/answer/124441115
         * sun.misc.Version类会在JDK类库的初始化过程中被加载并初始化，
         * 而在初始化时它需要对静态常量字段根据指定的常量值（ConstantValue）做默认初始化，
         * 此时被 sun.misc.Version.launcher 静态常量字段所引用的"java"字符串字面量就被intern到HotSpot VM的字符串常量池StringTable里了。
         */
        String str2 = new StringBuilder().append("ja").append("va").toString();
        System.out.println(str2.intern() == str2);
        // 而JDKJVM这个字符串则是首次出现
        String str3 = new StringBuilder().append("JDK").append("JVM").toString();
        System.out.println(str3.intern() == str3);
    }

    /**
     * java中的String是引用类型。创建的String对象，实际上存储的是一个地址。
     * 所以下面a和b都是引用类型，其存储的是字符串的地址。它们本身存储在Java虚拟机栈的局部变量表中。
     * - a：直接将字符串存储在常量池中，然后将a指向常量池种中的"JVM"。
     * - b：先将字符串"JVM"存储在常量池中，然后在heap中创建一个对象，该对象指向常量池中的"JVM"，最后将b指向heap中创建的这个对象。
     *
     * 也就是说，a和b存储的内容是一样的，都是"JVM"，但地址不一样：a中保存的是常量池中"JVM"的地址，b保存的是heap中那个对象的地址，
     *
     * 双等于号"=="比较的是地址，equals()比较的是内容。
     */
    private static void compareStr() {
        String a = "JVM";
        // new一个对象
        String b = new String("JVM");
        // == 比较地址是否相等
        // 都在运行时常量池中
        System.out.println("JVM" == a); // true
        System.out.println(a.intern() == a); // true
        // a为字符字面量（存储在运行时常量池中），b为对象（存储在堆中），所以不等。
        System.out.println(a == b); // false
        System.out.println(a.intern() == b); // false

        System.out.println(a.equals(b));  // true
    }
```

## 其他（不受JVM GC管理区域）
### 本机直接内存溢出
#### 直接内存（Direct Memory）
直接内存并不是虚拟机运行时数据区的一部分，也不是《Java虚拟机规范》中定义的内存区域。

容量大小可通过`-XX:MaxDirectMemorySize`参数来指定，如果不去指定，则默认与Java堆最大值(由-Xmx指定)一致。

##### NIO(New Input/Output)类
NIO引入了一种`基于通道(Channel)`与`缓冲区 (Buffer)`的I/O方式，它可以**使用Native函数库直接分配堆外内存**，然后**通过一个存储在Java堆里面的DirectByteBuffer对象作为这块内存的引用**进行操作。
这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。

##### OOM异常
当各个内存区域总和大于物理内存限制(包括物理的和操作系统级的限制)，从而导致动态扩展时出现 OutOfMemoryError异常。

```java
import sun.misc.Unsafe;

import java.lang.reflect.Field;

/**
 * 直接内存OOM异常：使用unsafe分配本机内存
 * VM Args: -Xmx20M -XX:MaxDirectMemorySize=10M
 *
 * @author wugang
 * date: 2020-06-04 19:43
 **/
public class DirectMemoryOOM {
    private static final int _1MB = 1024 * 1024;


    /**
     * 越过了DirectByteBuffer类直接通过反射获取Unsafe实例进行内存分配。
     * （Unsafe类的getUnsafe()方法指定只有引导类加载器才会返回实例，体现了设计者希望只有虚拟机标准类库里面的类才能使用Unsafe的功能，在JDK 10时才将Unsafe的部分功能通过VarHandle开放给外部使用）
     * 因为虽然使用DirectByteBuffer分配内存也会抛出内存溢出异常，但它抛出异常时并没有真正向操作系统申请分配内存，
     * 而是通过计算得知内存无法分配就会在代码里手动抛出溢出异常，真正申请分配内存的方法是Unsafe::allocateMemory ()。
     *
     * 抛出异常：
     * Exception in thread "main" java.lang.OutOfMemoryError
     * at sun.misc.Unsafe.allocateMemory(Native Method)
     */
    public static void main(String[] args) throws IllegalAccessException {
        Field unsafeField = Unsafe.class.getDeclaredFields()[0];
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        while (true) {
            unsafe.allocateMemory(_1MB);
        }
    }

}
```

由直接内存导致的内存溢出，一个明显的特征是在Heap Dump文件中不会看见有什么明显的异常情况。
如果发现内存溢出之后产生的Dump文件很小，而程序中又直接或间接使用了DirectMemory(典型的间接使用就是NIO)，那就可以考虑重点检查一下直接内存方面的原因了。


