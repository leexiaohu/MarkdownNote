# Java - IO


[TOC]

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
- 模板的使用

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
### Java IO: RandomAccessFile

简单的RandomAccessFile 类使用方法

```java
		RandomAccessfile file = new RandomAccessfile("C:\\Data||file.txt","rw");
        file.seek(200);//移动指针
        long pointer = file.geFilePointer();//获取文件指针位置
        int aByte = file.read();//读取文件的一个字节
        file.write("Hello word!".getBytes());//写入字节数组
        file.close();
```
### Java IO: File

###### Java IO API中的FIle类可以让你访问底层文件系统，通过File类，你可以做到以下几点：

- 检测文件是否存在
- 读取文件长度
- 重命名或移动文件
- 删除文件
- 检测某个路径是文件还是目录
- 读取目录中的文件列表

```java
	File file = new File("c:\\data\\input-file.txt");
    boolean fileExists = file.exists();
	long length = file.length();
	boolean success = file.renameTo(new File("c:\\data\\new-file.txt"));
    boolean success = file.delete();
	boolean isDirectory = file.isDirectory();
	String[] fileNames = file.list();
	File[] files = file.listFiles();
```

### Java IO: DataInputStream&DataOutputStream

DataInputStream可以使你从输入流中读取Java基本类型数据，而不必每次读取字节数据。你可以把InputStream包装到DataInputStream中，然后就可以从此输入流中读取基本类型数据了，代码如下：

```java
DataInputStream input = new DataInputStream(new FileInputStream("binary.data"));

int aByte = input.read();

int anInt = input.readInt();

float aFloat = input.readFloat();

double aDouble = input.readDouble();//etc.

input.close();
```
###### DataOutputStream

```java
DataOutputStream output = new DataOutputStream(new FileOutputStream("binary.data"));

output.write(45);

//byte data output.writeInt(4545);

//int data output.writeDouble(109.123);

//double data  output.close();
```

### Java IO: 序列化与ObjectInputStream、ObjectOutputStream

#### Serializable

如果你希望类能够序列化和反序列化，必须实现Serializable接口，就像所展示的ObjectInputStream和ObjectOutputStream例子一样。

#### ObjectInputStream

ObjectInputStream能够让你从输入流中读取Java对象，而不需要每次读取一个字节。你可以把InputStream包装到ObjectInputStream中，然后就可以从中读取对象了。代码如下：

```java
ObjectInputStream input = new ObjectInputStream(new FileInputStream("object.data"));

MyClass object = (MyClass) input.readObject(); //etc.

input.close();
```
在这个例子中，你读取的对象必须是MyClass的一个实例，并且必须事先通过ObjectOutputStream序列化到“object.data”文件中。

#### ObjectOutputStream

ObjectOutputStream能够让你把对象写入到输出流中，而不需要每次写入一个字节。你可以把OutputStream包装到ObjectOutputStream中，然后就可以把对象写入到该输出流中了。代码如下：

```java
ObjectOutputStream output = new ObjectOutputStream(new FileOutputStream("object.data"));

MyClass object = new MyClass();  output.writeObject(object); //etc.

output.close();
```

### Java IO: 其他*字节流*

本小节会简要概括Java IO中的PushbackInputStream，SequenceInputStream和PrintStream。其中，最常用的是PrintStream，System.out和System.err都是PrintStream类型的变量.

#### PushbackInputStream

PushbackInputStream用于解析InputStream内的数据。有时候你需要提前知道接下来将要读取到的字节内容，才能判断用何种方式进行数据解析。PushBackInputStream允许你这么做，你可以把读取到的字节重新推回到InputStream中，以便再次通过read()读取。

```java
PushbackInputStream input = new PushbackInputStream(new FileInputStream("c:\\data\\input.txt"));

int data = input.read();

input.unread(data);
```

#### SequenceInputStream

SequenceInputStream把一个或者多个InputStream整合起来，形成一个逻辑连贯的输入流。当读取SequenceInputStream时，会先从第一个输入流中读取，完成之后再从第二个输入流读取，以此推类。代码如下：

```java
InputStream input1 = new FileInputStream("c:\\data\\file1.txt");

InputStream input2 = new FileInputStream("c:\\data\\file2.txt");

InputStream combined = new SequenceInputStream(input1, input2);
```
通过SequenceInputStream，例子中的2个InputStream使用起来就如同只有一个InputStream一样(SequenceInputStream的read()方法会在读取到当前流末尾时，关闭流，并把当前流指向逻辑链中的下一个流，最后返回新的当前流的read()值)。

#### PrintStream

PrintStream允许你把格式化数据写入到底层OutputStream中。比如，写入格式化成文本的int，long以及其他原始数据类型到输出流中，而非它们的字节数据。代码如下：
```java
PrintStream output = new PrintStream(outputStream);

output.print(true);

output.print((int) 123);

output.print((float) 123.456);

output.printf(Locale.UK, "Text + data: %1$", 123);

output.close();
```
PrintStream包含2个强大的函数，分别是format()和printf()(这两个函数几乎做了一样的事情，但是C程序员会更熟悉printf())。

### Java IO: 其他*字符流*

本小节会简要概括Java IO中的PushbackReader，LineNumberReader，StreamTokenizer，PrintWriter，StringReader，StringWriter。

#### PushbacjReader

PushbackReader与PushbackInputStream类似，唯一不同的是PushbackReader处理字符，PushbackInputStream处理字节。代码如下：

```java
PushbackReader reader = new PushbackReader(new FileReader("c:\\data\\input.txt"));

int data = reader.read();

reader.unread(data);

//设置缓冲区大小
PushbackReader reader = new PushbackReader(new FileReader("c:\\data\\input.txt"), 8);

```

#### LineNumberReader

`LineNumberReader`是记录了已读取数据行号的BufferedReader。默认情况下，行号从0开始，当`LineNumberReader`读取到行终止符时，行号会递增(译者注：换行\n，回车\r，或者换行回车\n\r都是行终止符)。

你可以通过`getLineNumber()`方法获取当前行号，通过`setLineNumber()`方法设置当前行数(`setLineNumber()`仅仅改变`LineNumberReader`内的记录行号的变量值，不会改变当前流的读取位置。流的读取依然是顺序进行，意味着你不能通过`setLineNumber()`实现流的跳跃读取)。代码如下：

```java
LineNumberReader reader = new LineNumberReader(new FileReader("c:\\data\\input.txt"));

int data = reader.read();

while(data != -1){

    char dataChar = (char) data;

    data = reader.read();

    int lineNumber = reader.getLineNumber();

}
```

如果解析的文本有错误，LineNumberReader可以很方便地定位问题。当你把错误报告给用户时，如果能够同时把出错的行号提供给用户，用户就能迅速发现并且解决问题。

### StreamTokenizer

StreamTokenizer可以把输入流(InputStream和Reader。通过InputStream构造StreamTokenizer的构造函数已经在JDK1.1版本过时，推荐将InputStream转化成Reader，再利用此Reader构造StringTokenizer)分解成一系列符号。比如，句子”Mary had a little lamb”的每个单词都是一个单独的符号。

当你解析文件或者计算机语言时，为了进一步的处理，需要将解析的数据分解成符号。通常这个过程也称作分词。

通过循环调用nextToken()可以遍历底层输入流的所有符号。在每次调用nextToken()之后，StreamTokenizer有一些变量可以帮助我们获取读取到的符号的类型和值。这些变量是：

ttype 读取到的符号的类型(字符，数字，或者行结尾符)

sval 如果读取到的符号是字符串类型，该变量的值就是读取到的字符串的值

nval 如果读取到的符号是数字类型，该变量的值就是读取到的数字的值

```java
StreamTokenizer tokenizer = new StreamTokenizer(new StringReader("Mary had 1 little lamb..."));

while(tokenizer.nextToken() != StreamTokenizer.TT_EOF){

    if(tokenizer.ttype == StreamTokenizer.TT_WORD) {

        System.out.println(tokenizer.sval);
    } else if(tokenizer.ttype == StreamTokenizer.TT_NUMBER) {

        System.out.println(tokenizer.nval);

    } else if(tokenizer.ttype == StreamTokenizer.TT_EOL) {

        System.out.println();

    }

}
```
StreamTokenizer可以识别标示符，数字，引用的字符串，和多种注释类型。你也可以指定何种字符解释成空格、注释的开始以及结束等。在StreamTokenizer开始解析之前，所有的功能都可以进行配置。

#### StringReader
StringReader能够将原始字符串转换成Reader，代码如下：

```java
Reader reader = new StringReader("input string...");

int data = reader.read();

while(data != -1) {

    //do something with data...

    doSomethingWithData(data);

    data = reader.read();

}

reader.close();
```

#### StringWriter

StringWriter能够以字符串的形式从Writer中获取写入到其中数据，代码如下：

```java
StringWriter writer = new StringWriter();

//write characters to writer.

String data = writer.toString();

StringBuffer dataBuffer = writer.getBuffer();
```

toString()方法能够获取StringWriter中的字符串数据。

getBuffer()方法能够获取StringWriter内部构造字符串时所使用的StringBuffer对象。