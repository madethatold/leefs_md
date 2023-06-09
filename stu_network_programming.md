# 网络编程

## 计网基础

## 网络通信要素

主机间通过网络进行通信是需要遵循**网络通信协议**，是通过**IP地址**准确定位主机，通过**端口号**准确定位主机上的应用。

IP 地址：InetAddress(在Java中使用`InetAddress类`代表IP)

### **InetAddress类**

Internet上的主机有两种方式表示地址：

①域名(hostName)：www.baidu.com

②IP 地址(hostAddress)：14.215.177.38

InetAddress类主要表示IP地址，两个子类：Inet4Address、Inet6Address

InetAddress 类对象含有一个 Internet 主机地址的域名和IP地址：www.baidu.com 和 14.215.177.38

```java
try {
            InetAddress ip1 = InetAddress.getByName("192.168.3.2");
            InetAddress ip2 = InetAddress.getByName("www.baidu.com");
            System.out.println(ip1);//192.168.3.2
            System.out.println(ip2);//www.baidu.com/220.181.38.150

            InetAddress localHost = InetAddress.getLocalHost();//本机IP
            System.out.println(localHost);//DESKTOP-47CTN7V/192.168.31.233

            System.out.println(ip2.getHostAddress());//220.181.38.150
            System.out.println(ip2.getHostName());//www.baidu.com
        }catch (UnknownHostException e){
            e.printStackTrace();
        }
```

## HTTP协议

ServerSocket 走的是 [TCP 协议](https://tobebetterjavaer.com/socket/network-base.html)，HTTP 协议本身是在 TCP 协议之上的一层。

TCP 是一种面向连接的、可靠的、基于字节流的传输层协议。TCP 在两个网络节点之间提供了一条可靠的通信信道，确保数据在传输过程中不会丢失、重复或乱序。TCP 使用握手过程建立连接，通过确认和重传机制确保数据可靠传输，并使用流量控制和拥塞控制算法来优化网络性能。

HTTP 是一个用于在 Web 浏览器和 Web 服务器之间传输超文本、图像、视频和其他媒体资源的应用层协议。HTTP 使用请求-响应模型，即客户端（通常是 Web 浏览器）发送请求给服务器，服务器处理请求并返回响应。HTTP 协议定义了一组方法（如 GET、POST、PUT、DELETE 等），用于指定请求的类型和目的。此外，HTTP 协议还定义了一组状态代码（如 200、404、500 等），用于表示响应的结果。

HTTP 协议依赖于 TCP 协议来传输数据。当 Web 浏览器向 Web 服务器发送 HTTP 请求时，它首先使用 TCP 协议与服务器建立连接。一旦连接建立，HTTP 请求消息会被封装在 TCP 数据包中，然后通过 TCP 信道发送给服务器。服务器收到 TCP 数据包后，解包提取 HTTP 请求消息，处理请求并生成 HTTP 响应消息。最后，HTTP 响应消息被封装在 TCP 数据包中，并通过相同的 TCP 信道发送回客户端。客户端收到 TCP 数据包后，解包提取 HTTP 响应消息并显示给用户。

[![img](https://cdn.tobebetterjavaer.com/stutymore/http-20230331112928.png)](https://cdn.tobebetterjavaer.com/stutymore/http-20230331112928.png)

这幅图展示了客户端（Web 浏览器）与服务器（Web 服务器）之间的 HTTP 请求和响应，它们通过可靠的、面向连接的 TCP 连接进行数据传输。

### HTTP数据格式

#### **请求消息**

HTTP 请求消息由请求行（Request Line）、请求头（Request Headers）、空行（Empty Line）、请求体（Request Body，可选）几个部分组成。

请求行又包含三个部分，HTTP 方法（例如 GET, POST, PUT, DELETE 等）、请求的目标 URL（通常是相对 URL，但也可以是绝对 URL）、HTTP 版本（例如 HTTP/1.1 或 HTTP/2），这些部分用空格分隔，例如：

```
GET /index.html HTTP/1.1
```

请求头是一系列以键值对表示的元数据，用于描述请求的附加信息。每个请求头占一行，键和值之间用冒号（:）分隔。请求头包含诸如 Host、User-Agent、Content-Type、Content-Length、Accept 等信息。例如：

```
Host: www.tobebetterjavaer.com
User-Agent: Mozilla/5.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

请求头和请求体之间有一个空行，表示请求头的结束。

对于某些 HTTP 方法（例如 POST、PUT 等），还可以在请求消息中包含请求体。请求体用于传输要发送给服务器的数据。请求体的格式和内容取决于 Content-Type 请求头的值。

例如，当提交 HTML 表单时，请求体可能如下所示：

```
username=沉默王二&password=123456
```



完整的HTTP请求消息：

```
POST /login HTTP/1.1
Host: Host: www.tobebetterjavaer.com
User-Agent: Mozilla/5.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 29
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8

username=沉默王二&password=123456
```

我用一张思维导图来表示下：

[![img](https://cdn.tobebetterjavaer.com/stutymore/http-20230331114404.png)](https://cdn.tobebetterjavaer.com/stutymore/http-20230331114404.png)

#### **响应消息**

一个典型的 HTTP 响应消息由三部分组成：状态行（Status Line）、响应头（Response Headers）、响应体（Response Body）。

[![img](https://cdn.tobebetterjavaer.com/stutymore/http-20230331120336.png)](https://cdn.tobebetterjavaer.com/stutymore/http-20230331120336.png)

上面两张图，可以让你对 HTTP 请求和响应有个直观映象，接下来开始抓重点。

不管是请求消息还是响应消息，都可以划分为三部分，这就为我们后面的处理简化了很多工作。

- 第一行：状态行
- 第二行到第一个空行：header（请求头/响应头）
- 剩下所有：正文





# TCP网络编程

## Socket

利用套接字(Socket)开发网络应用程序早已被广泛的采用，以至于成为事实上的标准。

网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标识符套接字。

通信的两端都要有Socket，是两台机器间通信的端点。

网络通信其实就是Socket间的通信

`Socket`允许程序把网络连接当成一个流，数据在两个`Socket`间通过IO传输。

一般主动发起通信的应用程序属客户端，等待通信请求的为服务端。



Socket分为两类：

1. 流套接字（stream socket）：使用TCP提供可依赖的字节流服务

2. 数据报套接字（datagram socket）：使用UDP提供“尽力而为”的数据报服务



### 客户端Socket的工作过程

1. 创建 Socket：根据指定服务端的 IP 地址或端口号构造 Socket 类对象。若服务器端响应，则建立客户端到服务端的通信路线。若连接失败，则会出现异常。

2. 打开连接到 Socket 的输入/出流： 使用 getInputStream()方法获得输入流，使用 getOutputStream()方法获得输出流，进行数据传输

3. 按照一定的协议对 Socket 进行读/写操作：通过输入流读取服务器放入线路的信息(但不能读取自己放入路线的信息)，通过输出流将信息写入线程

4. 关闭 Socket：断开客户端到服务器的连接，释放线路

```java
try(Socket socket = new Socket("192.168.40.165",9999)) {//创建Socket对象，指明服务端的IP和端口号
            OutputStream out = socket.getOutputStream();//获取一个输出流，用于输出数据
            out.write("123".getBytes());//写出数据
            socket.close();//回收资源
}catch (UnknownHostException e){
            e.printStackTrace();
}

 try (Socket socket = new Socket("bbs.newsmth.net", 23)) {//创建套接字
            socket.setSoTimeout(1000);//设置套接字创建失败的超时时间
            InputStream is = socket.getInputStream();//获取输入流
            Scanner scanner = new Scanner(is, "gbk");
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                System.out.println(line);
            }
        } catch (UnknownHostException e) {
            //无法确定主机的 IP 地址
            e.printStackTrace();
        } catch (IOException e) {
            //创建套接字时发生IO错误
            e.printStackTrace();
        }
```



客户端创建Socket对象:

客户端程序可以使用Socket类创建对象，创建的同时会自动向服务器方发起连接。Socket的构造器是：

```java
// 构造器一
Socket(String host,int port)throws UnknownHostException,IOException
/* 向服务器(域名是 host。端口号为port)发起TCP连接，若成功，则创建Socket对象，否则抛出异常。*/

// 构造器二
Socket(InetAddress address,int port)throws IOException
/* 根据InetAddress对象所表示的 IP地址以及端口号port发起连接。*/
```



### 服务器(服务端)Socket的工作过程

1. 调用 ServerSocket(int port) ：创建一个服务器端套接字，并绑定到指定端口 上。用于监听客户端的请求。

2. 调用 accept()：监听连接请求，如果客户端请求连接，则接受连接，返回通信 套接字对象。

3. 调用 该Socket类对象的 getOutputStream() 和 getInputStream ()：获取输出 流和输入流，开始网络数据的发送和接收。

4. 关闭ServerSocket和Socket对象：客户端访问结束，关闭通信套接字。



```java
ServerSocket ss = new ServerSocket(9999);//创建服务端的ServerSocket，指明自己的端口号
        Socket s = ss.accept();//调用accept()监听来自客户端的连接
        //ss.accept()方法返回一个Socket对象，该对象表示与客户端的连接。
        //每当有一个新的客户端连接进来时，accept()方法会返回一个新的Socket对象，该对象用于与该客户端进行通信。
        InputStream inputStream = s.getInputStream();//获取输入流，读取输入流的数据
        byte []buffer = new byte[1024];//创建一个大小为1024字节的字节数组作为缓冲区
        int num = inputStream.read(buffer);//read()方法返回读取的字节数
        String str = new String(buffer,0,num);//将缓冲区中的字节数据转换为字符串。
        //这里使用字节数组、起始位置和读取的字节数来构造一个新的字符串对象
        System.out.println(s.getInetAddress().toString()+" "+str);
        s.close();
        ss.close();
```

服务器建立 ServerSocket 对象:

ServerSocket 对象负责等待客户端请求建立套接字连接，类似邮局某个窗口中的业务员。也就是说，**服务器必须事先建立一个等待客户请求建立套接字连接的ServerSocket对象。**所谓“接收”客户的套接字请求，就是accept()方法会返回一个 Socket 对象



上面的例子中，服务器端只能为一个客户端服务——这不符合服务器端一对多的要求

优化方案：
服务器端接收到客户端的套接字请求时，可以启动一个线程来处理，而主程序继续等待下一个连接。代码示例如下。

```java
	try (ServerSocket server = new ServerSocket(8888)) {
            while (true) {
                Socket socket = server.accept();
                Thread thread = new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            InputStream is = socket.getInputStream();
                            OutputStream os = socket.getOutputStream();
                            Scanner scanner = new Scanner(is);

                            // 其他代码省略
                            // 向客户端发送消息
                            // 读取客户端发送过来的消息
                        } catch (IOException e) {
                            e.printStackTrace();
                        } finally {
                            try {
                                socket.close();
                            } catch (IOException e) {
                                e.printStackTrace();
                            }
                        }
                    }
                });
                thread.start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

```



### TCP编程简单C/S通信示例

```java
	public void server() {
        ServerSocket serverSocket = null;
        Socket socket = null;
        InputStream input = null;
        ByteArrayOutputStream out = null;

        try {
            serverSocket = new ServerSocket(8848);
            socket = serverSocket.accept();
            System.out.println("client ip:" + socket.getInetAddress());
            input = socket.getInputStream();
            out = new ByteArrayOutputStream();
            byte[] buffer = new byte[10];
            int len;
            while ((len = input.read(buffer)) != -1) {
                out.write(buffer, 0, len);
            }
            System.out.println(out.toString());

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (out != null) {
                try {
                    out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (input != null) {
                try {
                    input.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (serverSocket != null) {
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public void client() {
        OutputStream output = null;
        Socket socket = null;
        try {
            InetAddress localHost = InetAddress.getLocalHost();
            socket = new Socket(localHost, 8848);
            output = socket.getOutputStream();
            output.write("123".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (output != null) {
                try {
                    output.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```



### TCP编程实现C/S文件传输

```java
//TCP编程，客户端发送文件给服务端，服务端将文件保存在本地。

	/* 客户端 */
    public void client() {
        Socket socket = null;
        OutputStream writer = null;
        BufferedInputStream bis = null;
        try {
            socket = new Socket(InetAddress.getByName("127.0.0.1"),8089);
            writer = socket.getOutputStream();

            bis = new BufferedInputStream(new FileInputStream(new File("me.jpg")));
            byte[] buffer = new byte[1024];
            int len;
            while((len = bis.read(buffer)) != -1){
                writer.write(buffer,0,len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(socket != null){
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(writer != null){
                try {
                    writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(bis != null){
                try {
                    bis.close();
                    System.out.println("发送成功！");
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
	/* 服务端 */
    public void server() throws IOException { // 这里异常应该使用try-catch-finally
        ServerSocket socket = new ServerSocket(8089);
        System.out.println("正在等待客户端连接...");
        Socket clientSocket = socket.accept();

        System.out.println("客户端已连接IP地址为："+clientSocket.getInetAddress().getHostName());
        InputStream is = clientSocket.getInputStream();
        BufferedOutputStream reader = new BufferedOutputStream(new FileOutputStream(new File("new_me.jpg")));

        byte[] buffer = new byte[1024];
        int len;
        while((len = is.read(buffer)) != -1){
            reader.write(buffer,0,len);
        }
        System.out.println("接收成功");

        socket.close();
        clientSocket.close();
        is.close();
        reader.close();
    }

```

### TCP编程实现C/S信息反馈

```
//description: TCP编程，从客户端发送文件给服务端，服务端保存到本地。并返回“发送成功”给 客户端。并关闭相应的连接。
    public void client() {
        Socket socket = null;
        OutputStream writer = null;
        BufferedInputStream bis = null;
        try {
            socket = new Socket(InetAddress.getByName("127.0.0.1"),8089);
            writer = socket.getOutputStream();

            bis = new BufferedInputStream(new FileInputStream(new File("me.jpg")));
            byte[] buffer = new byte[1024];
            int len;
            while((len = bis.read(buffer)) != -1){
                writer.write(buffer,0,len);
            }
            // 关闭数据的输出
            socket.shutdownOutput();

            // 接收服务端反馈的信息并输出到控制台
            InputStream is = socket.getInputStream();
            ByteArrayOutputStream byteArray = new ByteArrayOutputStream();
            byte[] buf = new byte[10];
            int l;
            while((l = is.read(buf)) != -1){
                byteArray.write(buf,0,l);
            }
            System.out.println(byteArray.toString());
            is.close();
            byteArray.close();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(socket != null){
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(writer != null){
                try {
                    writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(bis != null){
                try {
                    bis.close();
                    System.out.println("发送成功！");
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
	/* 服务端 */
    public void server() throws IOException { // 这里异常应该使用try-catch-finally
        ServerSocket socket = new ServerSocket(8089);
        System.out.println("正在等待客户端连接...");
        Socket clientSocket = socket.accept();

        System.out.println("客户端已连接IP地址为："+clientSocket.getInetAddress().getHostName());
        InputStream is = clientSocket.getInputStream();
        BufferedOutputStream reader = new BufferedOutputStream(new FileOutputStream(new File("new_me.jpg")));

        byte[] buffer = new byte[1024];
        int len;
        while((len = is.read(buffer)) != -1){
            reader.write(buffer,0,len);
        }
        System.out.println("接收成功");

        // 服务端给客户端反馈信息
        OutputStream os = clientSocket.getOutputStream();
        os.write("你好客户端，照片已经收到".getBytes());

        socket.close();
        clientSocket.close();
        is.close();
        reader.close();
        os.close();
    }

```

# UDP网络编程

类 **DatagramSocket** 和 **DatagramPacket** 实现了基于 UDP 协议网络程序。

UDP数据报通过数据报套接字 **DatagramSocket** 发送和接收，**系统不保证 UDP数据报一定能够安全送到目的地，也不能确定什么时候可以抵达。**

DatagramPacket 对象封装了UDP数据报，在数据报中包含了发送端的IP地址和端口号以及接收端的IP地址和端口号。

DP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接收方的连接。如同发快递包裹一样。

## UDP通信流程

```java
	public void send() throws Exception{
        DatagramSocket socket = new DatagramSocket();
        byte []data = "hello".getBytes();
        //每个数据报的大小限制在64K内
        DatagramPacket packet = new DatagramPacket(data,data.length, InetAddress.getLocalHost(),8080);
        socket.send(packet);
        socket.close();
    }

    public void receive() throws Exception{
        DatagramSocket socket = new DatagramSocket(8080);
        byte[] buffer = new byte[100];
        DatagramPacket packet = new DatagramPacket(buffer,0,buffer.length);
        socket.receive(packet);
        System.out.println(new String(packet.getData(),0,packet.getLength()));
        socket.close();
    }
```

# 用Socket实现一个HTTP服务器

- 创建 ServerSocket 对象，绑定监听端口
- 通过 `accept()` 方法监听客户端请求
- 连接建立后，通过输入流读取客户端发送的请求信息
- 通过输出流向客户端发送响应信息
- 关闭相关资源



目标是基于 Socket 创建一个 HTTP 服务器,重点是：

- 对请求数据进行解析
- 封装返回结果

## 请求数据解析

我们从 Socket 中拿到所有的数据，然后解析为对应的 HTTP 请求，我们先定义个 Request 对象，内部保存一些基本的 HTTP 信息，接下来重点就是将 Socket 中的所有数据都捞出来，封装为 request 对象。

```java
	public static class Request {
        /**
         * http的请求可以分为三部分
         *
         * 第一行为请求行: 即 方法 + URI + 版本
         * 第二部分到一个空行为止，表示请求头
         * 空行
         * 第三部分为接下来所有的，表示发送的内容,message-body；其长度由请求头中的 Content-Length 决定
         */

        /**
         * 请求方法 GET/POST/PUT/DELETE/OPTION...
         */
        private String method;
        /**
         * 请求的uri
         */
        private String uri;
        /**
         * http版本
         */
        private String version;

        /**
         * 请求头
         */
        private Map<String, String> headers;

        /**
         * 请求参数相关
         */
        private String message;
    }

    /**
     * 接下来三个方法将对请求行，请求头和请求的正文进行解析
     */

    private static void decodeRequestLine(BufferedReader reader,Request request) throws IOException{
        String[] strs = StringUtils.split(reader.readLine(), " ");
        assert strs.length == 3;
        request.setMethod(strs[0]);
        request.setUri(strs[1]);
        request.setVersion(strs[2]);
    }

    private static void decodeRequestHeader(BufferedReader reader, Request request) throws IOException {
        Map<String, String> headers = new HashMap<>(16);
        String line = reader.readLine();
        String[] kv;
        while (!"".equals(line)) {
            kv = StringUtils.split(line, ":");
            assert kv.length == 2;
            headers.put(kv[0].trim().toLowerCase(), kv[1].trim());
            line = reader.readLine();
        }
        // 将解析出来的请求头信息存入 Request 对象中
        request.setHeaders(headers);
    }

    private static void decodeRequestMessage(BufferedReader reader, Request request) throws IOException {
        int contentLen = Integer.parseInt(request.getHeaders().getOrDefault("content-length", "-1"));
        if (contentLen > 0) {
            char[] message = new char[contentLen];
            reader.read(message);
            request.setMessage(new String(message));
            return;
        }
        // 如get/options请求就没有message
        // 表示没有message，直接返回
        if (contentLen == -1) {
            return;
        }
    }

    /**
     *本方法将 InputStream 中的 HTTP 请求数据解析为一个 Request 对象
     */
    public static Request parse2request(InputStream reqStream) throws IOException {
        BufferedReader httpReader = new BufferedReader(new InputStreamReader(reqStream, "UTF-8"));
        Request httpRequest = new Request();
        decodeRequestLine(httpReader, httpRequest);
        decodeRequestHeader(httpReader, httpRequest);
        decodeRequestMessage(httpReader, httpRequest);
        return httpRequest;
    }
```

对Http请求结果进行封装

```java

    /**
     * 请求结果的封装
     * Response 类表示一个 HTTP 响应，包括版本、状态码、状态信息、响应头和响应正文。
     */
    public static class Response {
        private String version;
        private int code;
        private String status;
        private Map<String, String> headers;
        private String message;
    }

    public static String buildResponse(Request request, String response) {
        Response httpResponse = new Response();
        httpResponse.setCode(200);
        httpResponse.setStatus("ok");
        httpResponse.setVersion(request.getVersion());

        Map<String, String> headers = new HashMap<>();
        headers.put("Content-Type", "application/json");
        headers.put("Content-Length", String.valueOf(response.getBytes().length));
        httpResponse.setHeaders(headers);

        httpResponse.setMessage(response);

        StringBuilder builder = new StringBuilder();
        buildResponseLine(httpResponse, builder);
        buildResponseHeaders(httpResponse, builder);
        buildResponseMessage(httpResponse, builder);
        return builder.toString();
    }

    /**
     * 构建响应行，包括版本、状态码和状态信息。
     */
    private static void buildResponseLine(Response response, StringBuilder stringBuilder) {
        stringBuilder.append(response.getVersion()).append(" ")
                .append(response.getCode()).append(" ")
                .append(response.getStatus()).append("\n");
    }

    /**
     * 构建响应头。
     */
    private static void buildResponseHeaders(Response response, StringBuilder stringBuilder) {
        for (Map.Entry<String, String> entry : response.getHeaders().entrySet()) {
            stringBuilder.append(entry.getKey()).append(":").append(entry.getValue()).append("\n");
        }
        stringBuilder.append("\n");
    }

    /**
     * 构建响应正文。
     */
    private static void buildResponseMessage(Response response, StringBuilder stringBuilder) {
        stringBuilder.append(response.getMessage());
    }

```

## 请求任务 HttpTask

每个请求，单独分配一个任务来干这个事情，就是为了支持并发，对于 ServerSocket 而言，接收到了一个请求，那就创建一个 HttpTask 任务来实现 HTTP 通信。

httpTask的作用是：

- 从请求中捞数据
- 响应请求
- 封装结果并返回

```java
public class HttpTask implements Runnable {
    private Socket socket;

    public HttpTask(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        if (socket==null){
            throw new IllegalArgumentException("socket is null!");
        }

        try {
            // 获取 Socket 的输出流，并创建一个 PrintWriter 对象
            OutputStream outputStream = socket.getOutputStream();
            PrintWriter out = new PrintWriter(outputStream);
            // 从 Socket 的输入流中解析 HTTP 请求
            HttpMessageParser.Request httpRequest = HttpMessageParser.parse2request(socket.getInputStream());
            try {
                // 根据请求结果进行响应，省略返回
                String result = null;
                // 根据请求和结果构建 HTTP 响应
                String httpRes = HttpMessageParser.buildResponse(httpRequest, result);
                // 将 HTTP 响应发送到客户端
                out.print(httpRes);
            } catch (Exception e) {
                // 如果发生异常，构建一个包含异常信息的 HTTP 响应
                String httpRes = HttpMessageParser.buildResponse(httpRequest, e.toString());
                out.print(httpRes);
            }
            // 刷新输出流，确保响应消息被发送
            out.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭 Socket 连接
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## HTTP 服务搭建

创建`ServerSocket`，绑定端口接收请求，我们在线程池中跑这个 HTTP 服务

```java
public class BasicHttpServer {
    // 创建一个单线程执行器，用于启动 HTTP 服务器
    private static ExecutorService bootstrapExecutor = Executors.newSingleThreadExecutor();
    // 创建一个线程池，用于处理来自客户端的 HTTP 请求
    private static ExecutorService taskExecutor;
    // 设置服务器监听的端口号
    private static int PORT = 8999;

    // 启动 HTTP 服务器的方法
    static void startHttpServer() {
        // 获取处理器可用核心数，用于设置线程池大小
        int nThreads = Runtime.getRuntime().availableProcessors();
        // 初始化线程池，设置线程池大小，队列大小和丢弃策略
        taskExecutor = new ThreadPoolExecutor(nThreads,
                nThreads,
                0L,
                TimeUnit.MILLISECONDS,
                new LinkedBlockingQueue<>(100),
                new ThreadPoolExecutor.DiscardPolicy());

        // 循环尝试启动服务器，如果启动失败，则等待10秒后重试
        while (true) {
            try {
                ServerSocket serverSocket = new ServerSocket(PORT);
                bootstrapExecutor.submit(new ServerThread(serverSocket));
                break;
            } catch (Exception e) {
                try {
                    // 重试，等待 10 秒
                    TimeUnit.SECONDS.sleep(10);
                } catch (InterruptedException ie) {
                    Thread.currentThread().interrupt();
                }
            }
        }
        // 关闭启动执行器
        bootstrapExecutor.shutdown();
    }

    // HTTP 服务器主要任务类
    private static class ServerThread implements Runnable {
        // 保存传递给构造函数的 ServerSocket 实例
        private ServerSocket serverSocket;

        // 构造函数
        public ServerThread(ServerSocket s) throws IOException {
            this.serverSocket = s;
        }

        // 任务主体方法
        @Override
        public void run() {
            while (true) {
                try {
                    // 等待客户端连接
                    Socket socket = this.serverSocket.accept();
                    // 创建一个 HttpTask 实例，将 Socket 实例作为参数传递
                    HttpTask eventTask = new HttpTask(socket);
                    // 将 HttpTask 提交给 taskExecutor 执行
                    taskExecutor.submit(eventTask);
                } catch (Exception e) {
                    e.printStackTrace();
                    try {
                        // 如果发生异常，等待 1 秒后继续尝试
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException ie) {
                        Thread.currentThread().interrupt();
                    }
                }
            }
        }
    }
}
```



# URL网络编程

**URL(Uniform Resource Locator)**：统一资源定位符，**它表示 Internet 上某一 资源的地址。**

通过 URL 我们可以访问 Internet 上的各种网络资源，比如最常见的 www，ftp 站点。浏览器通过解析给定的 URL 可以在网络上查找相应的文件或其他资源。

URL的基本结构由5部分组成： **<传输协议>://<主机名>:<端口号>/<文件名>#片段名?参数列表**,eg:

http://192.168.1.100:8080/helloworld/index.jsp#a?username=shkstart&password=123

## URL类常用方法

public String getProtocol( ) 获取该URL的协议名

public String getHost( ) 获取该URL的主机名

public String getPort( ) 获取该URL的端口号

public String getPath( ) 获取该URL的文件路径

public String getFile( ) 获取该URL的文件名

public String getQuery( ) 获取该URL的查询名

