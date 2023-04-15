#### 12.10晚

研究guide-rpc-framework的pom.xml，看用到了什么技术：

1. Guava工程包含了若干被Google的 Java项目广泛依赖 的核心库，例如：集合 [collections] 、缓存 [caching] 、原生类型支持 [primitives support] 、并发库 [concurrency libraries] 、通用注解 [common annotations] 、字符串处理 [string processing] 、I/O 等等。

   [Google Guava官方教程（中文版）]: https://wizardforcel.gitbooks.io/guava-tutorial/content/1.html

   ~~~ xml
   <dependency>
       <groupId>com.google.guava</groupId>
       <artifactId>guava</artifactId>
       <version>${guava.version}</version>
   </dependency>
   ~~~

   

#### 12.11上 

- 研究了整数除法，用类似二进制做效率更高

  ~~~java
  //106=64+32+8+2=01101010B
          public int divide(int a, int b) {
          // 溢出处理
          if (a == Integer.MIN_VALUE && b == -1)
                return Integer.MAX_VALUE;
          //最后的符号判断
          int sign = (a > 0) ^ (b > 0) ? -1 : 1;
          a = Math.abs(a);
          b = Math.abs(b);
          int res = 0,flag=0;
          for (int i = 31; i >= 0; i--) {
              // 首先，右移的话，再怎么着也不会越界
              // 其次，无符号右移的目的是：将 -2147483648 看成 2147483648
              // 注意，这里不能是 (a >>> i) >= b 而应该是 (a >>> i) - b >= 0
              // 这个也是为了避免 b = -2147483648，如果 b = -2147483648
              // 那么 (a >>> i) >= b 永远为 true，但是 (a >>> i) - b >= 0 为 false
              if ((a >>> i) - b >= 0) { // a >= (b << i)
                  a -= (b << i);
                  // 代码优化：这里控制 res 大于等于 INT_MAX
                  if (res > Integer.MAX_VALUE - (1 << i)) {
                      return Integer.MIN_VALUE;
                  }
                  res += (1 << i);
              }
          }
  ~~~

#### 12.11下

- 学习JVM

  [JVM学习]: https://nyimac.gitee.io/2020/07/03/JVM%E5%AD%A6%E4%B9%A0/

  - 虚拟机栈中方法内的局部变量是否是线程安全的？

    - 如果方法内**局部变量没有逃离方法的作用范围**，则是**线程安全**的
    - 如果**局部变量引用了对象**，并**逃离了方法的作用范围**，则需要考虑线程安全问题

  - 栈帧有五个部分组成

    - 局部变量表
    - 操作数栈
    - 动态链接【方法的符号引用，在这里我们可以讨论虚方法（在运行时确定方法，把符号引用转为直接引用）和非虚方法（在编译时确定方法，把符号引用转为直接引用）】这里有很多可以讨论的，静态链接和动态链接，虚方法表等
    - 方法返回地址（PC寄存器中的值）
    - 其他

  - 通过new关键字**创建的对象**都会被放在堆内存

  - 线程运行诊断

    CPU占用过高

    - Linux环境下运行某些程序的时候，可能导致CPU的占用过高，这时需要定位占用CPU过高的线程
      - **top**命令，查看是哪个**进程**占用CPU过高
      - **ps H -eo pid, tid（线程id）, %cpu | grep 刚才通过top查到的进程号**    通过ps命令进一步查看是哪个线程占用CPU过高
      - **jstack 进程id**  通过查看进程中的线程的nid，刚才通过ps命令看到的tid来**对比定位**，注意jstack查找出的线程id是**16进制的**，**需要转换**

  - 堆内存诊断

    **jps**、**jmap**、**jconsole**、**jvirsalvm**

  - 常量池、1.8在堆内存中

    二进制字节码的组成：类的基本信息、常量池、类的方法定义（包含了虚拟机指令）
    
  - StringTable调优
  
    - 因为StringTable是由HashTable实现的，所以可以**适当增加HashTable桶的个数**，来减少字符串放入串池所需要的时间
  
      ~~~java
      -XX:StringTableSize=xxxx
      ~~~
  
      
  
    - 考虑是否需要将字符串对象入池，可以通过**intern方法减少重复入池**
  
  - 直接内存的回收不是通过JVM的垃圾回收来释放的，而是通过**unsafe.freeMemory**来手动释放
  
    ~~~java
    //通过ByteBuffer申请1M的直接内存
    ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_1M);
    
    DirectByteBuffer(int cap) {                   // package-private
    
            super(-1, 0, cap, cap, null);
            boolean pa = VM.isDirectMemoryPageAligned();
            int ps = Bits.pageSize();
            long size = Math.max(1L, (long)cap + (pa ? ps : 0));
            Bits.reserveMemory(size, cap);
    
            long base = 0;
            try {
                base = UNSAFE.allocateMemory(size);
            } catch (OutOfMemoryError x) {
                Bits.unreserveMemory(size, cap);
                throw x;
            }
            UNSAFE.setMemory(base, size, (byte) 0);
            if (pa && (base % ps != 0)) {
                // Round up to page boundary
                address = base + ps - (base & (ps - 1));
            } else {
                address = base;
            }
            cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
            att = null;
        }
    
    
    //分配一个新的本机内存块，以字节为单位。内存的内容是未初始化的;它们通常都是垃圾。生成的本机指针永远不会为零，并且将对所有值类型进行对齐。通过调用freeMemory来处理这个内存，或者使用reallocateMemory来调整它的大小。
        public long allocateMemory(long bytes) {
            bytes = alignToHeapWordSize(bytes);
    
            allocateMemoryChecks(bytes);
    
            if (bytes == 0) {
                return 0;
            }
    
            long p = allocateMemory0(bytes);
            if (p == 0) {
                throw new OutOfMemoryError("Unable to allocate " + bytes + " bytes");
            }
    
            return p;
        }
    
    //这里调用了一个Cleaner的create方法，且后台线程还会对虚引用的对象监测，如果虚引用的实际对象（这里是DirectByteBuffer）被回收以后，就会调用Cleaner的clean方法，来清除直接内存中占用的内存
    public void clean() {
           if (remove(this)) {
               try {
                   this.thunk.run(); //调用run方法
               } catch (final Throwable var2) {
                   AccessController.doPrivileged(new PrivilegedAction<Void>() {
                       public Void run() {
                           if (System.err != null) {
                               (new Error("Cleaner terminated abnormally", var2)).printStackTrace();
                           }
    
                           System.exit(1);
                           return null;
                       }
                   });
               }
               
    public void run() {
        if (address == 0) {
            // Paranoia
            return;
        }
        unsafe.freeMemory(address); //释放直接内存中占用的内存
        address = 0;
        Bits.unreserveMemory(size, capacity);
    }
               
    /**
     * 直接内存的回收机制总结
     *    使用了Unsafe类来完成直接内存的分配回收，回收需要主动调用freeMemory方法
     *    ByteBuffer的实现内部使用了Cleaner（虚引用）来检测ByteBuffer。一旦ByteBuffer被垃圾回收，那么会由
     *    ReferenceHandler来调用Cleaner的clean方法调用freeMemory来释放内存
     */
    ~~~
  
- 可达性分析算法

  - JVM中的垃圾回收器通过**可达性分析**来探索所有存活的对象
  - 扫描堆中的对象，看能否沿着GC Root对象为起点的引用链找到该对象，如果**找不到，则表示可以回收**
  - 可以作为GC Root的对象
    - 虚拟机栈（栈帧中的本地变量表）中引用的对象。　
    - 方法区中类静态属性引用的对象
    - 方法区中常量引用的对象
    - 本地方法栈中JNI（即一般说的Native方法）引用的对象

- Java的五种引用

  [Java的四种引用方式]: https://blog.csdn.net/linzhiqiang0316/article/details/88591907
  [强引用/软引用/弱引用/虚引用解析和应用场景分析]: https://cloud.tencent.com/developer/article/1354351

  - 强引用：只有GC Root**都不引用**该对象时，才会回收**强引用**对象
  - 软引用：当GC Root指向软引用对象时，在**内存不足时**，会**回收软引用所引用的对象**，可配合引用队列使用
  - 弱引用：。。。。。。无论内存足不足都会回收弱引用所引用的对象，可配合引用队列使用
  - 虚引用：虚引用对象所引用的对象被回收后，虚引用对象本身会被放入引用队列，然后调用虚引用方法
  - 终结器引用：当某个对象不再被其他的对象所引用时，会先将终结器引用对象放入引用队列中，然后根据终结器引用对象找到它所引用的对象，然后调用该对象的finalize方法。调用以后，该对象就可以被垃圾回收了

#### 12.12上

- 睡大觉到10点，玩

#### 12.12下

- 调通grpc-hello的客户端和服务端
  - maven项目生成客户端和服务端的话目录结构一定要对
  - ![image-20221222145543019](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20221222145543019.png)
  - ![image-20221222145419549](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20221222145419549.png)
- 继续JVM的学习

#### 12.12晚

- 了解了grpc的基本流程 
- 学netty

#### 12.13 上

- 睡大觉

#### 12.13下

- 学netty

#### 12.16

- 刷算法
- 刷不太下去就继续netty吧

#### 12.17

- 学netty

  - pipeline必须实现writeandflush才能执行outhandler


#### 12.19

- netty总结

  - 为什么使用NIO

- 日志的使用‘

  [Java 日志记录最佳实践，写得太好了吧！ - 掘金 (juejin.cn)](https://juejin.cn/post/7029968437933768734)


#### 12.21

- NIO中
  - channel.regist(),在keys中加值
  - select()方法触发selectedKeys加值
  - 一次select()对应一次accept，read等操作。



#### 12.22

- 学习Java-grpc

  [教程 - Java 教程 - 《gRPC 官方文档中文版 v1.0》 - 书栈网 · BookStack](https://www.bookstack.cn/read/grpc-v1.0/10.md#:~:text=gRPC 基础： Java 1 在一个 .proto 文件内定义服务。 2,和 protocol buffers 的 Github 仓库的 版本注释 发现更多关于新版本的内容。)

- 了解protocol buffers

  [语言指南 (proto3)  | Protocol Buffers  | Google Developers](https://developers.google.com/protocol-buffers/docs/proto3)

  
