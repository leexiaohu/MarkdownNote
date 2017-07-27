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
如果你需要在不同端之间读取文件，你可以根据该文件是二进制文件还是文本文件来选择使用`FileInputStream`或者`FileReader`。

###### 通过Java IO写文件
如果你需要在不同端之间进行文件的写入，你可以根据你要写入的数据是二进制型数据还是字符型数据选用FileOutputStream或者FileWriter。

###### 通过Java IO随机存取文件
正如我所提到的，你可以通过`RandomAccessFile`对文件进行随机存取。

随机存取并不意味着你可以在真正随机的位置进行读写操作，它只是意味着你可以跳过文件中某些部分进行操作，并且支持同时读写，不要求特定的存取顺序。这使得`RandomAccessFile`可以覆盖一个文件的某些部分、或者追加内容到它的末尾、或者删除它的某些内容，当然它也可以从文件的任何位置开始读取文件。

###### 文件和目录信息的获取
如果需要知道文件的大小和文件的属性，或者需要获取某个目录下的文件列表可以通过`File`类可以获取文件和目录的信息。

### Java IO: 管道

Java IO中的管道为运行在同一个JVM中的两个线程提供了通信的能力。所以管道也可以作为数据源以及目标媒介。

不能利用管道与不同的JVM中的线程通信(不同的进程)。在概念上，Java的管道不同于Unix/Linux系统中的管道。在Unix/Linux中，运行在不同地址空间的两个进程可以通过管道通信。在Java中，通信的双方应该是运行在同一进程中的不同线程。

###### Java IO管道示例

![](http://ifeve.com/wp-content/uploads/2014/10/1.png)

你也可以使用两个管道共有的connect()方法使之相关联。`PipedInputStream`和`PipedOutputStream`都拥有一个可以互相关联的connect()方法。

### Java IO: 字节和字符数组

- 从InputStream或者Reader中读入数组
- 从OutputStream或者Writer中写数组

###### 从 InputStream 或 Reader中读取数组

```java
//字节流
byte[] bytes = new byte[1024];
//把数据写入字节数组...
InputStream input = new ByteArrayInputStream(bytes);
int data = input.read();
while(data != -1) {
    //操作数据
    //读下一个字节
    data = input.read();
}
//字符流
char[] chars = new char[1024];
//把数据写入字符数组...
Reader reader = new CharArrayReader(chars);
int data = reader.read();
while(data != -1) {
    //操作数据
    //读下一个字符
    data = reader.read();
}
```
###### 通过 OutputStream 或者 Writer写数组
```java
//字节流
//把数据写入字节数组...
OutputStream output = new ByteArrayOutputStream();
//写输出流
output.write("This text is converted to bytes".toBytes("UTF-8"));
//写入的数据一数组的形式返回
byte[] bytes = output.toByteArray();

//字符流
//把数据写入字符数组...
Writer writer = new CharArrayWriter();
writer.write(//string or int );
char[] chars = writer.toCharArray();
```

###### 替换系统输出流
```java
OutputStream output = new FileOutputStream("c:\\data\\system.out.txt");
PrintStream printOut = new PrintStream(output);
System.setOut(printOut);
```

### IO整合

###### 输入流整合

你可以将流整合起来以便实现**更高级**的输入和输出操作。比如，一次读取一个字节是很慢的，所以**可以从磁盘中一次读取一大块数据，然后从读到的数据块中获取字节**。为了实现缓冲，可以把`InputStream`包装到`BufferedInputStream`中。
```java
InputStream input = new BufferedInputStream(new FileInputStream("c:\\data\\input-file.txt"));
```
缓冲同样可以应用到OutputStream中。你可以实现将大块数据批量地写入到磁盘(或者相应的流)中，这个功能由BufferedOutputStream实现。

缓冲只是通过流整合实现的其中一个效果。你可以把InputStream包装到PushbackInputStream中，之后可以将读取过的数据推回到流中重新读取，在解析过程中有时候这样做很方便。或者，你可以将两个InputStream整合成一个SequenceInputStream。

###### 整合Reader和Writer

和字节流一样，Reader和Writer可以相互结合实现更多更有趣的IO，工作原理和把Reader与InputStream或者Writer与OutputStream相结合类似。

```java
Reader reader = new BufferedReader(new FileReader(...));
Writer writer = new BufferedWriter(new FileWriter(...));
```

###Java IO: 并发IO

在同一时刻不能有多个线程同时从`InputStream`或者`Reader`中读取数据，也不能同时往`OutputStream`或者`Writer`里写数据。你没有办法保证每个线程读取多少数据，以及多个线程写数据时的顺序。

如果线程之间能够保证操作的顺序，它们可以使用同一个stream、reader、writer。比如，你有一个线程判断当前的输入流来自哪种类型的请求，然后将流数据传递给其他合适的线程做后续处理。当有序存取流、reader、writer时，这种做法是可行的。请注意，在线程之间传递流数据的代码应当是同步的。

### Java IO：异常处理模板（编程技巧：使代码更加优雅）

#### 1、抽象类模板
- 模板的定义
```java
public abstract class InputStreamProcessingTemplate {

    public void process(String fileName){
        IOException processException = null;
        InputStream input = null;
        try{
            input = new FileInputStream(fileName);

            doProcess(input);
        } catch (IOException e) {
            processException = e;
        } finally {
           if(input != null){
              try {
                 input.close();
              } catch(IOException e){
                 if(processException != null){
                    throw new MyException(processException, e,
                      "Error message..." +
                      fileName);
                 } else {
                    throw new MyException(e,
                        "Error closing InputStream for file " +
                        fileName;
                 }
              }
           }
           if(processException != null){
              throw new MyException(processException,
                "Error processing InputStream for file " +
                    fileName;
        }
    }

    //override this method in a subclass, to process the stream.
    public abstract void doProcess(InputStream input) throws IOException;
}
```
- 模板的使用
```java
new InputStreamProcessingTemplate(){
        public void doProcess(InputStream input) throws IOException{
            int inChar = input.read();
            while(inChar !- -1){
                //do something with the chars...
            }
        }
    }.process("someFile.txt");
```
这个例子通过创建`InputStreamProcessingTemplate`的一个匿名子类，并实例化子类的实例，然后调用它的.`process()`方法

#### 2、接口模板

- 模板的定义

```java
public interface InputStreamProcessor {
    public void process(InputStream input) throws IOException;
}

public class InputStreamProcessingTemplate {

    public void process(String fileName, InputStreamProcessor processor){
        IOException processException = null;
        InputStream input = null;
        try{
            input = new FileInputStream(fileName);

            processor.process(input);
        } catch (IOException e) {
            processException = e;
        } finally {
           if(input != null){
              try {
                 input.close();
              } catch(IOException e){
                 if(processException != null){
                    throw new MyException(processException, e,
                      "Error message..." +
                      fileName;
                 } else {
                    throw new MyException(e,
                        "Error closing InputStream for file " +
                        fileName);
                 }
              }
           }
           if(processException != null){
              throw new MyException(processException,
                "Error processing InputStream for file " +
                    fileName;
        }
    }
}
```
- 模板的使用方法

```java
new InputStreamProcessingTemplate()
        .process("someFile.txt", new InputStreamProcessor(){
            public void process(InputStream input) throws IOException{
                int inChar = input.read();
                while(inChar !- -1){
                    //do something with the chars...
                }
            }
        });
```
### 3、静态模板方法

- 模板的定义

```java
public class InputStreamProcessingTemplate {

    public static void process(String fileName,
    InputStreamProcessor processor){
        IOException processException = null;
        InputStream input = null;
        try{
            input = new FileInputStream(fileName);

            processor.process(input);
        } catch (IOException e) {
            processException = e;
        } finally {
           if(input != null){
              try {
                 input.close();
              } catch(IOException e){
                 if(processException != null){
                    throw new MyException(processException, e,
                      "Error message..." +
                      fileName);
                 } else {
                    throw new MyException(e,
                        "Error closing InputStream for file " +
                        fileName;
                 }
              }
           }
           if(processException != null){
              throw new MyException(processException,
                "Error processing InputStream for file " +
                    fileName;
        }
    }
}
```
-模板的使用

```java
InputStreamProcessingTemplate.process("someFile.txt",
        new InputStreamProcessor(){
            public void process(InputStream input) throws IOException{
                int inChar = input.read();
                while(inChar !- -1){
                    //do something with the chars...
                }
            }
        });
```
