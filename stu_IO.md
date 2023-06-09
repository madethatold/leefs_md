# 概述

[![](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/io/shangtou-01.png)](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/io/shangtou-01.png)

好久没搞过 IO 了，老王看到这幅思维导图也是吃了一惊。想想也是，他当初学习 Java IO 的时候头也大，乌央乌央的一片，全是类，估计是所有 Java 包里面类最多的，一会是 Input 一会是 Output，一会是 Reader 一会是 Writer，真不知道 Java 的设计者是怎么想的。

IO，即in和out，也就是输入和输出，指应用程序和外部设备之间的数据传递，常见的外部设备包括文件、管道、网络连接。

流（Stream），是一个抽象的概念，是指一连串的数据（字符或字节），是以先进先出的方式发送信息的通道。

当程序需要读取数据的时候，就会开启一个通向数据源的流，这个数据源可以是文件，内存，或是网络连接。类似的，当程序需要写入数据的时候，就会开启一个通向目的地的流。这时候你就可以想象数据好像在这其中“流”动一样。

一般来说关于流的特性有下面几点：

- 先进先出：最先写入输出流的数据最先被输入流读取到。
- 顺序存取：可以一个接一个地往流中写入一串字节，读出时也将按写入顺序读取一串字节，不能随机访问中间的数据。（RandomAccessFile除外）
- 只读或只写：每个流只能是输入流或输出流的一种，不能同时具备两个功能，输入流只能进行读操作，对输出流只能进行写操作。在一个数据传输通道中，如果既要写入数据，又要读取数据，则要分别提供两个流。



## 传输方式

传输方式有两种，字节和字符，那首先得搞明白字节和字符有什么区别，对吧？

字节（byte）是计算机中用来表示存储容量的一个计量单位，通常情况下，一个字节有 8 位（bit）。

字符（char）可以是计算机中使用的字母、数字、和符号，比如说 A 1 $ 这些。

通常来说，一个字母或者一个字符占用一个字节，一个汉字占用两个字节。

具体还要看字符编码，比如说在 UTF-8 编码下，一个英文字母（不分大小写）为一个字节，一个中文汉字为三个字节；在 Unicode 编码中，一个英文字母为一个字节，一个中文汉字为两个字节。

**字节流用来处理二进制文件，比如说图片啊、MP3 啊、视频啊。**

**字符流用来处理文本文件，文本文件可以看作是一种特殊的二进制文件，只不过经过了编码，便于人们阅读。**



- 字节流一般用来处理图像、视频、音频、PPT、Word等类型的文件。字符流一般用于处理纯文本类型的文件，如TXT文件等，但不能处理图像视频等非文本文件。用一句话说就是：**字节流可以处理一切文件，而字符流只能处理纯文本文件。**
- 字节流本身没有缓冲区，缓冲字节流相对于字节流，效率提升非常高。而字符流本身就带有缓冲区，缓冲字符流相对于字符流效率提升就不是那么大了。



## 基本方法

**InputStream 类**

- `int read()`：读取数据
- `int read(byte b[], int off, int len)`：从第 off 位置开始读，读取 len 长度的字节，然后放入数组 b 中
- `long skip(long n)`：跳过指定个数的字节
- `int available()`：返回可读的字节数
- `void close()`：关闭流，释放资源

**OutputStream 类**

- `void write(int b)`： 写入一个字节，虽然参数是一个 int 类型，但只有低 8 位才会写入，高 24 位会舍弃（这块后面再讲）
- `void write(byte b[], int off, int len)`： 将数组 b 中的从 off 位置开始，长度为 len 的字节写入
- `void flush()`： 强制刷新，将缓冲区的数据写入
- `void close()`：关闭流

**Reader 类**

- `int read()`：读取单个字符
- `int read(char cbuf[], int off, int len)`：从第 off 位置开始读，读取 len 长度的字符，然后放入数组 b 中
- `long skip(long n)`：跳过指定个数的字符
- `int ready()`：是否可以读了
- `void close()`：关闭流，释放资源

**Writer 类**

- `void write(int c)`： 写入一个字符
- `void write( char cbuf[], int off, int len)`： 将数组 cbuf 中的从 off 位置开始，长度为 len 的字符写入
- `void flush()`： 强制刷新，将缓冲区的数据写入
- `void close()`：关闭流

## 操作对象划分



# 文件流

在 IO 操作中，文件的操作相对来说是比较复杂的，但也是使用频率最高的部分，我们几乎所有的项目中几乎都躺着一个叫做 FileUtil 或者 FileUtils 的工具类。

`java.io.File` 类是专门对文件进行操作的类，注意只能对文件本身进行操作，不能对文件内容进行操作，想要操作内容，必须借助输入输出流。

`File` 类是文件和目录的抽象表示，主要用于文件和目录的创建、查找和删除等操作。

怎么理解上面两句话？其实很简单！

第一句是说 File 跟流无关，File 类不能对文件进行读和写，也就是输入和输出！

第二句是说 File 可以表示`D:\\文件目录1`与`D:\\文件目录1\\文件.txt`，前者是文件夹（Directory，或者叫目录）后者是文件(file)，File 类就是用来操作它俩的。

## File 构造方法

在 Java 中，一切皆是对象，File 类也不例外，不论是哪个对象都应该从该对象的构造说起，所以我们来分析分析`File`类的构造方法。

比较常用的构造方法有三个：

1、 `File(String pathname)` ：通过给定的**路径**来创建新的 File 实例。

2、 `File(String parent, String child)` ：从**父路径（字符串）和子路径**创建新的 File 实例。

3、 `File(File parent, String child)` ：从**父路径（File）和子路径名字符串**创建新的 File 实例。

```java
// 文件路径名
String path = "/Users/username/123.txt";
File file1 = new File(path);
// 通过父路径和子路径字符串
String parent = "/Users/username/aaa";
String child = "bbb.txt";
File file3 = new File(parent, child);//相当于/Users/username/aaa/bbb.txt
// 通过父级File对象和子路径字符串
File parentDir = new File("/Users/username/aaa");
String child = "bbb.txt";
File file4 = new File(parentDir, child); --------相当于/Users/username/aaa/bbb.txt
```

macOS 路径使用正斜杠（`/`）作为路径分隔符，而 Windows 路径使用反斜杠（`\`）作为路径分隔符。所以在遇到路径分隔符的时候，不要直接去写`/`或者`\`。

Java 中提供了一个跨平台的方法来获取路径分隔符，即使用 `File.separator`，这个属性会根据操作系统自动返回正确的路径分隔符。

File 类的构造方法不会检验这个文件或目录是否真实存在，因此无论该路径下是否存在文件或者目录，都不影响 File 对象的创建。

## File 常用方法

File 的常用方法主要分为获取功能、获取绝对路径和相对路径、判断功能、创建删除功能的方法。

```java
		/**
         *获取功能的方法
         */
        File f = new File("./bbb.txt");
        System.out.println("文件绝对路径:"+f.getAbsolutePath());
        System.out.println("文件构造路径:"+f.getPath());
        System.out.println("文件名称:"+f.getName());
        System.out.println("文件长度:"+f.length()+"字节");

        File f2 = new File("./a");
        System.out.println("目录绝对路径:"+f2.getAbsolutePath());
        System.out.println("目录构造路径:"+f2.getPath());
        System.out.println("目录名称:"+f2.getName());
        System.out.println("目录长度:"+f2.length());//length() 表示文件的长度，File 对象表示目录的时候，返回值并无意义

        /**
         * 判断功能的方法
         */
        File file = new File("/Users/username/example");
        // 判断文件或目录是否存在
        if (file.exists()) {
            System.out.println("文件或目录存在");
        } else {
            System.out.println("文件或目录不存在");
        }

        // 判断是否是目录
        if (file.isDirectory()) {
            System.out.println("是目录");
        } else {
            System.out.println("不是目录");
        }

        // 判断是否是文件
        if (file.isFile()) {
            System.out.println("是文件");
        } else {
            System.out.println("不是文件");
        }

        /**
         * 创建、删除功能的方法
         */
        // 创建文件
        File file1 = new File("/Users/username/example/test.txt");
        if (file1.createNewFile()) {
            System.out.println("创建文件成功：" + file1.getAbsolutePath());
        } else {
            System.out.println("创建文件失败：" + file1.getAbsolutePath());
        }

        // 删除文件
        if (file1.delete()) {
            System.out.println("删除文件成功：" + file1.getAbsolutePath());
        } else {
            System.out.println("删除文件失败：" + file1.getAbsolutePath());
        }

        // 创建多级目录
        //可以创建多级目录，如果父目录不存在，则会一并创建。返回 true 表示创建成功，返回 false 表示创建失败或目录已经存在。
        File directory = new File("/Users/username/example/subdir1/subdir2");
        if (directory.mkdirs()) {
            System.out.println("创建目录成功：" + directory.getAbsolutePath());
        } else {
            System.out.println("创建目录失败：" + directory.getAbsolutePath());
        }

        /**
         * 目录的遍历
         */
        File directory1 = new File("/Users/itwanger/Documents/Github/paicoding");

        // 列出目录下的文件名
        String[] files = directory1.list();
        System.out.println("目录下的文件名：");
        for (String ff : files) {
            System.out.println(ff);
        }

        // 列出目录下的文件和子目录
        File[] filesAndDirs = directory1.listFiles();
        System.out.println("目录下的文件和子目录：");
        for (File fileOrDir : filesAndDirs) {
            if (fileOrDir.isFile()) {
                System.out.println("文件：" + fileOrDir.getName());
            } else if (fileOrDir.isDirectory()) {
                System.out.println("目录：" + fileOrDir.getName());
            }
        }
```

递归遍历一个目录

```java
public static void main(String[] args) {
    File directory = new File("/Users/itwanger/Documents/Github/paicoding");

    // 递归遍历目录下的文件和子目录
    traverseDirectory(directory);
}

public static void traverseDirectory(File directory) {
    // 列出目录下的所有文件和子目录
    File[] filesAndDirs = directory.listFiles();

    // 遍历每个文件和子目录
    for (File fileOrDir : filesAndDirs) {
        if (fileOrDir.isFile()) {
            // 如果是文件，输出文件名
            System.out.println("文件：" + fileOrDir.getName());
        } else if (fileOrDir.isDirectory()) {
            // 如果是目录，递归遍历子目录
            System.out.println("目录：" + fileOrDir.getName());
            traverseDirectory(fileOrDir);
        }
    }
}
```



#### 绝对路径-相对路径

绝对路径是从文件系统的根目录开始的完整路径，它描述了一个文件或目录在文件系统中的确切位置。在 Windows 系统中，绝对路径通常以盘符（如 C:）开始，例如 "`C:\Program Files\Java\jdk1.8.0_291\bin\java.exe`"。在 macOS 和 Linux 系统中，绝对路径通常以斜杠（`/`）开始，例如 "`/usr/local/bin/python3`"。

相对路径是相对于当前工作目录的路径，它描述了一个文件或目录与当前工作目录之间的位置关系。在 Java 中，相对路径通常是相对于当前 Java 程序所在的目录，例如 "`config/config.properties`"。如果当前工作目录是 "`/Users/username/project`"，那么相对路径 "`config/config.properties`" 就表示 "`/Users/username/project/config/config.properties`"

- 在 Windows 操作系统中，文件系统默认是不区分大小写的，即在文件系统中，文件名和路径的大小写可以混合使用。例如，"`C:\Users\username\Documents\example.txt`" 和 "`C:\Users\Username\Documents\Example.txt`" 表示的是同一个文件。但是，Windows 操作系统提供了一个区分大小写的选项，可以在格式化磁盘时选择启用，这样文件系统就会区分大小写。
- 在 macOS 和 Linux 等 Unix 系统中，文件系统默认是区分大小写的。例如，在 macOS 系统中，"`/Users/username/Documents/example.txt`" 和 "`/Users/username/Documents/Example.txt`" 表示的是两个不同的文件。

## RandomAccessFile

与其他 IO 类（如 FileInputStream 和 FileOutputStream）不同，RandomAccessFile 允许您跳转到文件的任何位置，从那里开始读取或写入。这使得它特别适用于需要在文件中随机访问数据的场景，如数据库系统。

### 构造方法

- `RandomAccessFile(File file, String mode)`：使用给定的文件对象和访问模式创建一个新的 RandomAccessFile 实例。
- `RandomAccessFile(String name, String mode)`：使用给定的文件名和访问模式创建一个新的 RandomAccessFile 实例。



- "r"：以只读模式打开文件。调用结果对象的任何 write 方法都将导致 IOException。
- "rw"：以读写模式打开文件。如果文件不存在，它将被创建。
- "rws"：以读写模式打开文件，并要求对内容或元数据的每个更新都被立即写入到底层存储设备。这种模式是同步的，可以确保在系统崩溃时不会丢失数据。
- "rwd"：与“rws”类似，以读写模式打开文件，但仅要求对文件内容的更新被立即写入。元数据可能会被延迟写入。

#### 

### 主要方法

- `long getFilePointer()`：返回文件指针的当前位置。
- `long length()`：返回此文件的长度。
- `int read()`：从该文件中读取一个字节数据。
- `int read(byte[] b)`：从该文件中读取字节数据并将其存储到指定的字节数组中。
- `int read(byte[] b, int off, int len)`：从该文件中读取字节数据并将其存储到指定的字节数组中，从偏移量 off 开始，最多读取 len 个字节。
- `String readLine()`：从该文件中读取一行文本。
- `readUTF()`：从文件读取 UTF-8 编码的字符串。此方法首先读取两个字节的长度信息，然后根据这个长度读取字符串的 UTF-8 字节。最后，这些字节被转换为 Java 字符串。这意味着当你使用 readUTF 方法读取字符串时，需要确保文件中的字符串是使用 writeUTF 方法写入的，这样它们之间的长度信息和编码方式才能保持一致。
- `void seek(long pos)`：将文件指针设置到文件中的 pos 位置。
- `void write(byte[] b)`：将指定的字节数组的所有字节写入该文件。
- `void write(byte[] b, int off, int len)`：将指定字节数组的部分字节写入该文件，从偏移量 off 开始，写入 len 个字节。
- `void write(int b)`：将指定的字节写入该文件。
- `writeUTF(String str)`：将一个字符串以 UTF-8 编码写入文件。此方法首先写入两个字节的长度信息，表示字符串的 UTF-8 字节长度，然后写入 UTF-8 字节本身。因此，当你使用 writeUTF 写入字符串时，实际写入的字节数会比字符串的 UTF-8 字节长度多两个字节。这两个字节用于在读取字符串时确定正确的字符串长度。

```java
	public static void main(String[] args) {
        String filePath = "logs/javabetter/itwanger.txt";

        try {
            // 使用 RandomAccessFile 写入文件
            writeToFile(filePath, "Hello!");

            // 使用 RandomAccessFile 读取文件
            String content = readFromFile(filePath);
            System.out.println("文件内容: " + content);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static void writeToFile(String filePath, String content) throws IOException {
        try (RandomAccessFile randomAccessFile = new RandomAccessFile(filePath, "rw")) {
            // 将文件指针移动到文件末尾（在此处追加内容）
            randomAccessFile.seek(randomAccessFile.length());

            // 写入内容
            randomAccessFile.writeUTF(content);
        }
    }

    private static String readFromFile(String filePath) throws IOException {
        StringBuilder content = new StringBuilder();

        try (RandomAccessFile randomAccessFile = new RandomAccessFile(filePath, "r")) {
            // 将文件指针移动到文件开始处（从头开始读取）
            randomAccessFile.seek(0);

            content.append(randomAccessFile.readUTF());
        }

        return content.toString();
    }
```

### Apache FileUtils 

用到再查吧

### Hutool FileUtil 类

用到再查吧

# 字节流

## 字节输出流(OutputStream)

`java.io.OutputStream` 是**字节输出流**的**超类**（父类），我们来看一下它定义的一些共性方法：

1、 `close()` ：关闭此输出流并释放与此流相关联的系统资源。

2、 `flush()` ：刷新此输出流并强制缓冲区的字节被写入到目的地。

3、 `write(byte[] b)`：将 b.length 个字节从指定的字节数组写入此输出流。

4、 `write(byte[] b, int off, int len)` ：从指定的字节数组写入 len 字节到此输出流，从偏移量 off开始。 **也就是说从off个字节数开始一直到len个字节结束**



### FileOutputStream类

```java
//文件名构造对象
String fileName = "example.txt";
FileOutputStream fos = new FileOutputStream(fileName);
//文件对象构造对象
File file = new File("example.txt");
FileOutputStream fos = new FileOutputStream(file);
//使用文件名和追加标志创建 FileOutputStream 对象
String fileName = "example.txt";
boolean append = true;
FileOutputStream fos = new FileOutputStream(fileName, append);
//true 表示追加数据，false 表示不追加也就是清空原有数据

//FileOutputStream 的使用示例
FileOutputStream fos = null;
try {
  fos = new FileOutputStream("example.txt");
  fos.write("沉默王二".getBytes());
} catch (IOException e) {
  e.printStackTrace();
} finally {
  if (fos != null) {
    try {
      fos.close();
    } catch (IOException e) {
      e.printStackTrace();
    }
  }
}
```

使用 `FileOutputStream` 写入字节数据主要通过 `write` 方法：

```java
write(int b)
write(byte[] b)
write(byte[] b,int off,int len)  //从`off`索引开始，`len`个字节
```



```java
		FileOutputStream fos = new FileOutputStream("fos.txt");
        fos.write(97); // 第1个字节
        /**
         * 当使用 write(int b) 方法写出一个字节时，参数 b 表示要写出的字节的整数值。
         * 由于一个字节只有8位，因此参数 b 的取值范围应该在 0 到 255 之间，超出这个范围的值将会被截断。
         * 例如，如果参数 b 的值为 -1，那么它会被截断为 255，如果参数 b 的值为 256，那么它会被截断为 0。
         */

        byte[] b = "一二三".getBytes();
        fos.write(b);

        byte[] c = "abcde".getBytes();
        // 从索引2开始，2个字节。索引2是c，两个字节，也就是cd。
        fos.write(c,2,2);
        fos.close();
```





## 字节输入流(InputStream)

`java.io.InputStream` 是**字节输入流**的**超类**（父类），我们来看一下它的一些共性方法：

1、`close()` ：关闭此输入流并释放与此流相关的系统资源。

2、`int read()`： 从输入流读取数据的下一个字节。

3、`read(byte[] b)`： 该方法返回的 int 值代表的是读取了多少个字节，读到几个返回几个，读取不到返回-1

### FileInputStream类

InputStream 有很多子类，我们从最简单的一个子类 FileInputStream 开始。看名字就知道是文件输入流，用于将数据从文件中读取数据。

FileInputStream的构造方法:

1、`FileInputStream(String name)`：创建一个 FileInputStream 对象，并打开指定名称的文件进行读取。文件名由 name 参数指定。如果文件不存在，将会抛出 FileNotFoundException 异常。

2、`FileInputStream(File file)`：创建一个 FileInputStream 对象，并打开指定的 File 对象表示的文件进行读取。

```java
// 创建一个 FileInputStream 对象
FileInputStream fis = new FileInputStream("test.txt");
// 读取文件内容
int data;
while ((data = fis.read()) != -1) {
    System.out.print((char) data);
}
// 关闭输入流
fis.close();
```

使用字节数组读取

```java
// 创建一个 FileInputStream 对象
FileInputStream fis = new FileInputStream("test.txt");

// 读取文件内容到缓冲区
byte[] buffer = new byte[1024];
int count;
while ((count = fis.read(buffer)) != -1) {
    System.out.println(new String(buffer, 0, count));
}

// 关闭输入流
fis.close();
```

#### 使用FileInputStream复制图片

图片信息读入到字节输入流中，再通过字节输出流写入到文件中。

```java
		FileInputStream fis = new FileInputStream("1.jpg");
        FileOutputStream fos = new FileOutputStream("copy.jpg");
        byte []buffer = new byte[1024];
        int count;
        while ((count=fis.read(buffer))!=-1){
            fos.write(buffer,0,count);
        }
        fis.close();
        fos.close();
```

# 字符流

[![img](https://cdn.tobebetterjavaer.com/stutymore/reader-writer-20230320164938.png)](https://cdn.tobebetterjavaer.com/stutymore/reader-writer-20230320164938.png)

字符流是一种用于读取和写入字符数据的输入输出流。与字节流不同，字符流以字符为单位读取和写入数据，而不是以字节为单位。常用来处理文本信息。

如果用字节流直接读取中文，可能会遇到乱码问题。之所以出现乱码是因为在字节流中，一个字符通常由多个字节组成，而不同的字符编码使用的字节数不同。如果我们使用了错误的字符编码，或者在读取和写入数据时没有正确处理字符编码的转换，就会导致读取出来的中文字符出现乱码。

```java
//FileInputStream为操作文件的字符输入流
FileInputStream inputStream = new FileInputStream("a.txt");//内容为“沉默王二是傻 X”

int len;
while ((len=inputStream.read())!=-1){
    System.out.print((char)len);
}
```

我们如果使用字节流读文件，就不会出现乱码，如下：

```java
try (FileInputStream inputStream = new FileInputStream("a.txt")) {
    byte[] bytes = new byte[1024];
    int len;
    while ((len = inputStream.read(bytes)) != -1) {
        System.out.print(new String(bytes, 0, len));
    }
}
```

因为，`new String(byte bytes[], int offset, int length)`有解码功能





字符流，`专门用于处理文本`文件（音频、图片、视频等为非文本文件）。

从另一角度来说：**字符流 = 字节流 + 编码表**

## 字符输入流（Reader）

`java.io.Reader`是**字符输入流**的**超类**（父类），它定义了字符输入流的一些共性方法：

- 1、`close()`：关闭此流并释放与此流相关的系统资源。
- 2、`read()`：从输入流读取一个字符。
- 3、`read(char[] cbuf)`：从输入流中读取一些字符，并将它们存储到字符数组 `cbuf`中

FileReader 是 Reader 的子类，用于从文件中读取字符数据。它的主要特点如下：

- 可以通过构造方法指定要读取的文件路径。
- 每次可以读取一个或多个字符。
- 可以读取 Unicode 字符集中的字符，通过指定字符编码来实现字符集的转换。



FileReader构造方法

- `FileReader(File file)`：创建一个新的 FileReader，参数为File对象。
- `FileReader(String fileName)`：创建一个新的 FileReader，参数为文件名。

代码示例如下：

```java
// 使用File对象创建流对象
File file = new File("a.txt");
FileReader fr = new FileReader(file);

// 使用文件名称创建流对象
FileReader fr = new FileReader("b.txt");
```





读字符数据：

```java
// 使用文件名称创建流对象
FileReader fr = new FileReader("abc.txt");
// 定义变量，保存数据
int b;
// 循环读取
while ((b = fr.read())!=-1) {
    System.out.println((char)b);
}
// 关闭资源
fr.close();

/**
 * 读取指定长度的字符
 */
File textFile = new File("docs/约定.md");
// try-with-resources FileReader
//FileReader 实现了 AutoCloseable 接口，因此可以使用 try-with-resources 语句自动关闭资源，避免了手动关闭资源的繁琐操作。
try(FileReader reader = new FileReader(textFile);) {
    // read(char[] cbuf)
    char[] buffer = new char[1024];
    int len;
    while ((len = reader.read(buffer, 0, buffer.length)) != -1) {
        System.out.print(new String(buffer, 0, len));
    }
}
```

## 字符输出流（Writer）

`java.io.Writer` 是**字符输出流**类的**超类**（父类），可以将指定的字符信息写入到目的地，来看它定义的一些共性方法：

- 1、`write(int c)` 写入单个字符。
- 2、`write(char[] cbuf)` 写入字符数组。
- 3、`write(char[] cbuf, int off, int len)` 写入字符数组的一部分，off为开始索引，len为字符个数。
- 4、`write(String str)` 写入字符串。
- 5、`write(String str, int off, int len)` 写入字符串的某一部分，off 指定要写入的子串在 str 中的起始位置，len 指定要写入的子串的长度。
- 6、`flush()` 刷新该流的缓冲。
- 7、`close()` 关闭此流，但要先刷新它。

`java.io.FileWriter` 类是 Writer 的子类，用来将字符写入到文件。





 构造方法

- `FileWriter(File file)`： 创建一个新的 FileWriter，参数为要读取的File对象。
- `FileWriter(String fileName)`： 创建一个新的 FileWriter，参数为要读取的文件的名称。

FileWriter写入数据

```java
/**
         * 写入字节
         */
        FileWriter fw = null;
        try {
            fw = new FileWriter("output.txt");
            fw.write(72); // 写入字符'H'的ASCII码
            fw.write(101); // 写入字符'e'的ASCII码
            fw.write(108); // 写入字符'l'的ASCII码
            fw.write(108); // 写入字符'l'的ASCII码
            fw.write(111); // 写入字符'o'的ASCII码
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fw != null) {
                    fw.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        /**
         * 写入字符数组
         */
        FileWriter fw1 = null;
        try {
            fw1 = new FileWriter("output.txt");
            char[] chars = {'H', 'e', 'l', 'l', 'o'};
            fw1.write(chars); // 将字符数组写入文件
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fw1 != null) {
                    fw1.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        /**
         *写入字符串
         */
        FileWriter fw2 = new FileWriter("output.txt");
        String str = "沉默王二";
        fw2.write(str); // 将字符串写入文件
```

#### 关闭close和刷新flush

因为 FileWriter 内置了缓冲区 ByteBuffer，所以如果不关闭输出流，就无法把字符写入到文件中。

但是关闭了流对象，就无法继续写数据了。如果我们既想写入数据，又想继续使用流，就需要 `flush` 方法了。

`flush` ：刷新缓冲区，流对象可以继续使用。

`close` ：先刷新缓冲区，然后通知系统释放资源。流对象不可以再被使用了。

```java
//源   也就是输入流【读取流】 读取a.txt文件
FileReader fr=new FileReader("abc.txt");  //必须要存在a.txt文件，否则报FileNotFoundException异常
//目的地  也就是输出流
FileWriter fw=new FileWriter("b.txt");  //系统会自动创建b.txt，因为它是输出流！
int len;
while((len=fr.read())!=-1){
    fw.write(len);
}
//这里就完成不了赋值，b依旧是空的
//添加以下代码
fr.close();
fw.flush();
fw.close();
```

我的理解就是`flush`方法允许我在不关闭流的情况下，将写入的数据更新到文件中，继续写。

## 文本文件的复制

```java
	public static void main(String[] args) throws IOException {
        FileReader fr=new FileReader("a.txt");//文件不存在会抛出java.io.FileNotFoundException
        //创建输出流对象
        FileWriter fw=new FileWriter("b.txt");
        /**创建输出流做的工作：
         *      1、调用系统资源创建了一个文件
         *      2、创建输出流对象
         *      3、把输出流对象指向文件
         */

        //文本文件复制，一次读一个字符
        copyMethod1(fr, fw);
        //文本文件复制，一次读一个字符数组
        copyMethod2(fr, fw);

        fr.close();
        fw.close();
    }
    public static void copyMethod1(FileReader fr, FileWriter fw) throws IOException{
        int len;
        while ((len=fr.read())!=-1){
            fw.write(len);
        }
        fw.flush();
    }
    public static void copyMethod2(FileReader fr, FileWriter fw) throws IOException{
        char []chars = new char[1024];
        int len = 0;
        while ((len=fr.read(chars))!=-1){
            fw.write(chars);
        }
        fw.flush();
    }
```

# 缓冲流

Java 的缓冲流是对字节流和字符流的一种封装，通过在内存中开辟缓冲区来提高 I/O 操作的效率。Java 通过 BufferedInputStream 和 BufferedOutputStream 来实现字节流的缓冲，通过 BufferedReader 和 BufferedWriter 来实现字符流的缓冲。

缓冲流的工作原理是将数据先写入缓冲区中，当缓冲区满时再一次性写入文件或输出流，或者当缓冲区为空时一次性从文件或输入流中读取一定量的数据。这样可以减少系统的 I/O 操作次数，提高系统的 I/O 效率，从而提高程序的运行效率。

## 字节缓冲流

- `BufferedInputStream(InputStream in)` ：创建一个新的缓冲输入流，注意参数类型为**InputStream**。
- `BufferedOutputStream(OutputStream out)`： 创建一个新的缓冲输出流，注意参数类型为**OutputStream**



```java
// 创建字节缓冲输入流，先声明字节流
FileInputStream fps = new FileInputStream(b.txt);
BufferedInputStream bis = new BufferedInputStream(fps)

// 创建字节缓冲输入流（一步到位）
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("b.txt"));
```

使用缓冲流复制一个大文件，可以提现缓冲流的效率

```java
// 创建流对象
try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream("py.mp4"));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("copyPy.mp4"));){
    // 读写数据
    int len;
    byte[] bytes = new byte[8*1024];
    while ((len = bis.read(bytes)) != -1) {
        bos.write(bytes, 0 , len);
    }
}
```

传统的 Java IO 是阻塞模式的，它的工作状态就是“读/写，等待，读/写，等待。。。。。。”

字节缓冲流解决的就是这个问题：**一次多读点多写点，减少读写的频率，用空间换时间**。

- 减少系统调用次数：在使用字节缓冲流时，数据不是立即写入磁盘或输出流，而是先写入缓冲区，当缓冲区满时再一次性写入磁盘或输出流。这样可以减少系统调用的次数，从而提高 I/O 操作的效率。
- 减少磁盘读写次数：在使用字节缓冲流时，当需要读取数据时，缓冲流会先从缓冲区中读取数据，如果缓冲区中没有足够的数据，则会一次性从磁盘或输入流中读取一定量的数据。同样地，当需要写入数据时，缓冲流会先将数据写入缓冲区，如果缓冲区满了，则会一次性将缓冲区中的数据写入磁盘或输出流。这样可以减少磁盘读写的次数，从而提高 I/O 操作的效率。
- 提高数据传输效率：在使用字节缓冲流时，由于数据是以块的形式进行传输，因此可以减少数据传输的次数，从而提高数据传输的效率

## 字符缓冲流

BufferedReader 类继承自 Reader 类，提供了一些便捷的方法，例如 `readLine()` 方法可以一次读取一行数据，而不是一个字符一个字符地读取。

BufferedWriter 类继承自 Writer 类，提供了一些便捷的方法，例如 `newLine()` 方法可以写入一个系统特定的行分隔符。



- `BufferedReader(Reader in)` ：创建一个新的缓冲输入流，注意参数类型为**Reader**。
- `BufferedWriter(Writer out)`： 创建一个新的缓冲输出流，注意参数类型为**Writer**。

字符缓冲流的基本方法与普通字符流调用方式一致，这里不再赘述，我们来看字符缓冲流**特有**的方法。

- BufferedReader：`String readLine()`: **读一行数据**，读取到最后返回 null
- BufferedWriter：`newLine()`: **换行**，由系统定义换行符

```java
// 创建流对象
BufferedReader br = new BufferedReader(new FileReader("a.txt"));
// 定义字符串,保存读取的一行文字
String line  = null;
// 循环读取,读取到最后返回null
while ((line = br.readLine())!=null) {
    System.out.print(line);
    System.out.println("------");
}
// 释放资源
br.close();

// 创建流对象
BfferedWriter bw = new BufferedWriter(new FileWriter("b.txt"));
bw.write("沉");
bw.newLine();// 写出换行
bw.write("默");
bw.newLine();
// 释放资源
bw.close();
```

# 转换流

转换流可以将一个字节流包装成字符流，或者将一个字符流包装成字节流。这种转换通常用于处理文本数据，如读取文本文件或将数据从网络传输到应用程序。

转换流主要有两种类型：InputStreamReader 和 OutputStreamWriter。



**用到再看**

# 序列流

Java 的序列流（ObjectInputStream 和 ObjectOutputStream）是一种可以将 Java 对象序列化和反序列化的流。

## 序列化

序列化是指将一个对象转换为一个字节序列（包含`对象的数据`、`对象的类型`和`对象中存储的属性`等信息），以便在网络上传输或保存到文件中，或者在程序之间传递。在 Java 中，序列化通过实现 java.io.Serializable 接口来实现，只有实现了 `Serializable` 接口的对象才能被序列化。

反序列化是指将一个字节序列转换为一个对象，以便在程序中使用。

[![img](https://cdn.tobebetterjavaer.com/stutymore/serialize-20230323105551.png)](https://cdn.tobebetterjavaer.com/stutymore/serialize-20230323105551.png)





## ObjectOutputStream

ObjectOutputStream 的构造方法： `ObjectOutputStream(OutputStream out)`

该构造方法接收一个 OutputStream 对象作为参数，用于将序列化后的字节序列输出到指定的输出流中。例如：

```java
FileOutputStream fos = new FileOutputStream("file.txt");
ObjectOutputStream oos = new ObjectOutputStream(fos);
```

一个对象要想序列化，必须满足两个条件:

- 该类必须实现`java.io.Serializable` 接口，否则会抛出`NotSerializableException` 。
- 该类的所有字段都必须是可序列化的。如果一个字段不需要序列化，则需要使用`transient` 关键字进行修饰。

```java
public class ObjectOutputStreamDemo {
    public static void main(String[] args) {
        Person person = new Person("沉默王二", 20);
        try {
            FileOutputStream fos = new FileOutputStream("logs/person.dat");
            ObjectOutputStream oos = new ObjectOutputStream(fos);
            oos.writeObject(person);
            oos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
class Person implements Serializable {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

## ObjectInputStream

ObjectInputStream 可以读取 ObjectOutputStream 写入的字节流，并将其反序列化为相应的对象（包含`对象的数据`、`对象的类型`和`对象中存储的属性`等信息）。

说简单点就是，序列化之前是什么样子，反序列化后就是什么样子。

来看一下构造方法：`ObjectInputStream(InputStream in)` ： 创建一个指定 InputStream 的 ObjectInputStream。

其中，ObjectInputStream 的 readObject 方法用来读取指定文件中的对象，示例如下：

```java
String filename = "logs/person.dat"; // 待反序列化的文件名
try (FileInputStream fileIn = new FileInputStream(filename);
     ObjectInputStream in = new ObjectInputStream(fileIn)) {
     // 从指定的文件输入流中读取对象并反序列化
     Object obj = in.readObject();
     // 将反序列化后的对象强制转换为指定类型
     Person p = (Person) obj;
     // 打印反序列化后的对象信息
     System.out.println("Deserialized Object: " + p);
} catch (IOException | ClassNotFoundException e) {
     e.printStackTrace();
}
```

## Kryo

用的时候看