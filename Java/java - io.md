# Java - IO

### Java IO 概述

Java IO 是一套Java用来读写数据（输入和输出）的API。大部分程序都要处理一些输入，并由输入产生一些输出。Java为此提供了`java.io`包。

`Java.io` 包主要涉及**文件**，**网络数据流**，**内存缓冲**等的输入输出。

###### IO字节流的类层次结构

![](http://wiki.jikexueyuan.com/project/java/images/iostreams.jpg)

Java的IO包主要关注的是从原始数据源的读取以及输出原始数据到目标媒介。以下是最典型的数据源和目标媒介：
-  文件
-  管道
-  网络连接
-  内存缓存
-  System.in, System.out, System.error(注：Java标准输入、输出、错误输出)

###### Java IO类概述表

![](http://ifeve.com/wp-content/uploads/2014/10/QQ%E6%88%AA%E5%9B%BE20141020174145.png)

### Java IO: 文件

###### 通过Java IO读文件
如果你需要在不同端之间读取文件，你可以根据该文件是二进制文件还是文本文件来选择使用FileInputStream或者FileReader。

###### 通过Java IO写文件
如果你需要在不同端之间进行文件的写入，你可以根据你要写入的数据是二进制型数据还是字符型数据选用FileOutputStream或者FileWriter。

###### 通过Java IO随机存取文件
正如我所提到的，你可以通过RandomAccessFile对文件进行随机存取。

随机存取并不意味着你可以在真正随机的位置进行读写操作，它只是意味着你可以跳过文件中某些部分进行操作，并且支持同时读写，不要求特定的存取顺序。这使得RandomAccessFile可以覆盖一个文件的某些部分、或者追加内容到它的末尾、或者删除它的某些内容，当然它也可以从文件的任何位置开始读取文件。

###### 文件和目录信息的获取
有时候你可能需要读取文件的信息而不是文件的内容，举个例子，如果你需要知道文件的大小和文件的属性。对于目录来说也是一样的，比如你需要获取某个目录下的文件列表。通过File类可以获取文件和目录的信息。

### Java IO: 管道

Java IO中的管道为运行在同一个JVM中的两个线程提供了通信的能力。所以管道也可以作为数据源以及目标媒介。

不能利用管道与不同的JVM中的线程通信(不同的进程)。在概念上，Java的管道不同于Unix/Linux系统中的管道。在Unix/Linux中，运行在不同地址空间的两个进程可以通过管道通信。在Java中，通信的双方应该是运行在同一进程中的不同线程。

###### Java IO管道示例

![](http://ifeve.com/wp-content/uploads/2014/10/1.png)


