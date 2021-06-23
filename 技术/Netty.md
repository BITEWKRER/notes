# Netty

Netty（基于NIO）是- -个异步的、基干事件驱动的网络应用框架，用以快速开发高性能、高可靠性的网络I0程序。

## I/O模型

 Java BIO:同步并阻塞(传统阻塞型)，服务器实现模式为一**一个连接一一个线程**， 即客户端有连接请求时服务器端就需要启动-一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销。

Java NIO : 同步非阻塞，服务器实现模式为-**一个线程处理多个请求**(连接)，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求就进行处理。适用于聊天服务器，弹幕系统，服务器通讯等。
Java AIO(NIO.2): 异步非阻塞，AIO引入异步通道的概念，采用了Proactor模式， 简化了程序编写，**有效的请求才启动线程**，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于**连接数较多且连接时间较长**的应用。

## BIO工作流程

1)服务器端启动一-个ServerSocket
2)客户端启动Socket对服务器进行通
信，默认情况下服务器端需要对每
个客户建立一一个线程与之通讯
3) 客户端发出请求后，先咨询服务器
是否有线程响应，如果没有则会等
待，或者被拒绝
4)如果有响应， 客户端线程会等待请
求结束后，在继续执行

## Nio（buffer，channel，selector）

### 三者关系

​	![image-20210112100406041](D:\notes\images\image-20210112100406041.png)

1)每 个channel都会对应一-个Bufer
2)Selector对对应一个线程，-个线程对应多个channel(连接)
3)该图反应了有三个channel注册到该selector /程序
4)程序切换到哪个channel是有事件决定的，Event就是一-个重 要的概念
5)Selector会根据不同的事件，在各个通道上切换
6)Buffer就是-一个内存块，底层是有一-个数组
7)数据的读取写入是通过Buffer,这个和BIO , BIO中要么是输入流，或者是
输出流，不能双向，但是NIO的Buffer 是可以读也可以写，需要flip方法切换
8)channel是双向的,可以返回底层操作系统的情况，比如Linux，底层的操作系统通道就是双向的.

## Buffer

![image-20210116105610897](D:\notes\images\image-20210116105610897.png)

- 常用Buffer子类
  1) ByteBuffer,存储字节数据到缓冲区
  2) ShortBuffer, 存储字符串数据到缓冲区
  3) CharBuffer, 存储字符数据到缓冲区
  4)IntBuffer,存储整数数据到缓冲区
  5) LongBuffer, 存储长整型数据到缓冲区
  6) DoubleBuffer, 存储小数到缓冲区
  7)FloatBuffer,存储小数到缓冲区

- 重要属性

![image-20210116104227423](D:\notes\images\image-20210116104227423.png)

- buffer方法

  **public final int capacity( )//返回此缓冲区的容量**
  **public final int position( )/返回此缓冲区的位置**
  **public final Buffer position (int newPositio)//设置此缓冲区的位置**
  **public final int limit( )//返回此缓冲区的限制**
  **public final Buffer limit (int newLimit//设置此缓冲区的限制**
  public final Buffer mark( )/在此缓冲区的位置设置标记
  public final Buffer reset( )//将此缓冲区的位置重置为以前标记的位置
  **public final Buffer clear( )/清除此缓冲区，即将各个标记恢复到初始状态，但是数据并没有真正擦除，**
  **public final Buffer flip( )//反转此缓冲区**
  public final Buffer rewind( )//重绕此缓冲区
  public final int remaining( )//返回当前位置与限制之间的元素数
  **public final boolean hasRemaining( )//告知在当前位置和限制之间是否有元素**
  **public abstract boolean isReadOnly( );//告知此缓冲区是否为只读缓冲区**
  //JDK1.6时引入的api
  **public abstract boolean hasArray(;//告知此缓冲区是否具有可访问的底层实现数组**
  **public abstract Object array)//返回此缓冲区的底层实现数组**
  public abstract int rraoffset(;//返回此缓冲区的底层实现数组中第一一个缓冲区元素的偏移量
  public abstract boolean isDirect();//告知此缓冲区是否为直接缓冲区

- ByteBuffer（最常用）

  //缓冲区创建相关api
  **public static ByteBuffer allocateDirectint capacity//创建直接缓冲区**
  **public static ByteBuffer allocate(int capacity//设置缓冲区的初始容量**
  public static ByteBuffer wrap(byte[] array)//把一 个数组放到缓冲区中使用
  //构造初始化位置ffset和上界length的缓冲区
  public static ByteBuffer wrap(byte[] array,int offset, int length)
  //缓存区存取相关API
  **public abstract byte get( );//从当前位置position.上get, get之后，position会自动+1**
  **public abstract byte get (int index);//从绝对位置get**
  **public abstract ByteBuffer put (byte b);//从当前位置上添加，put之后，position会自动+1**
  **public abstract ByteBuffer put (int index, byte b);//从绝对位置上put**

## Channel