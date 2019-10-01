---
typora-root-url: pics
typora-copy-images-to: pics
---

# Socket通信原理

### 一、概述

#### 什么是TCP/IP、UDP

TCP/IP(Transmission Control Protocol/Internet Protocol)即传输控制协议/网间协议，它是为广域网(WANs)设计的。

UDP(User Data Protocal，用户数据报文协议)。它属于TCP/IP协议族的一种

![img](/socket1.jpg)

TCP/IP三次握手：

```
1 客户端发送syn报文到服务器端，并置发送序号为x。
2 服务器端接收到客户端发送的请求报文，然后向客户端发送syn报文，并且发送确认序号x+1，并置发送序号为y。
3 客户端受到服务器发送确认报文后，发送确认信号y+1，并置发送序号为z。至此客户端和服务器端建立连接。
```

![img](/31093719-34ccd74b68c646859b6b9ddd8620217f.png)

#### Socket在哪里

Socket是应用层与TCP/IP协议族通信的中间**软件抽象层**，它**是一组接口**。在设计模式中，Socket其实就是一个门面模式，它把复杂的TCP/IP协议族隐藏在Socket接口后面。

#### Socket如何使用

![img](/socket3.jpg)

	1. 服务器端：服务器初始化Socket，然后与端口绑定(bind)，对端口进行监听(listen)，调用accept阻塞，等待客户端连接
	2. 客户端：初始化一个Socket，然后连接服务器(connect)，如果连接成功，则客户端与服务器就建立连接了（服务器响应客户端的请求，建立一个新的socket，把服务器socket发送给客户端；客户端确认，则连接建立）。
	3. 客户端发起数据请求(write,read)，服务器端接收请求并处理请求(read,write)，然后回应客户端数据请求。
	4. 客户端读取数据，并关闭连接(close)，一次交互结束。
### 二、如何通信

1. 网络中进程间如何通信

   本地进程间通信(IPC, Inter-Process Communication)有很多种方式，包括4类：

   - 消息传递(管道、FIFO、消息队列)
   - 同步(互斥量、条件变量、读写锁、文件和写记录锁、信号量)
   - 共享内存(匿名和具名)
   - 远程过程调用(Solaris门和Sun RPC)

   网络中进程通信方式，唯一标识问题：

   本地进程通过**PID**来唯一标识一个进程，网路进程___"**ip地址**"+传输层"**协议**+**端口**"___唯一标识一个进程。

   使用TCP/IP协议的应用程序通常采用应用编程接口：**UNIX BSD套接字(Socket)**，来实现网路进程间通信。

2. 什么是Socket

   socket起源于Unix，而Unix/Linux基本哲学之一就是“一切皆文件”，都可以用“打开open –> 读写write/read –> 关闭close”模式来操作。我的理解就是Socket就是该模式的一个实现，socket即是一种特殊的文件，一些socket函数就是对其进行的操作（读/写IO、打开、关闭）。

3. Socket的基本操作(以TCP为例)

   1. socket()

      ```c
      int socket(int domain, int type, int protocol);
      ```

      socket()用于创建一个socket描述符(socket descriptor)。

      三个参数：

      - domain 协议域，协议族family。常用的协议族有，AF_INET、AF_INET6、AF_LOCAL（或称AF_UNIX，Unix域socket）、AF_ROUTE等等。协议族决定了socket的地址类型，在通信中必须采用对应的地址，如AF_INET决定了要用ipv4地址（32位的）与端口号（16位的）的组合、AF_UNIX决定了要用一个绝对路径名作为地址。
      - type：指定socket类型。常用的socket类型有，SOCK_STREAM、SOCK_DGRAM、SOCK_RAW、SOCK_PACKET、SOCK_SEQPACKET等等（socket的类型有哪些？）
      - protocol：故名思意，就是指定协议。常用的协议有，IPPROTO_TCP、IPPTOTO_UDP、IPPROTO_SCTP、IPPROTO_TIPC等，它们分别对应TCP传输协议、UDP传输协议、STCP传输协议、TIPC传输协议（这个协议我将会单独开篇讨论！）。

      > 当我们调用**socket**创建一个socket时，返回的socket描述字它存在于协议族（address family，AF_XXX）空间中，但没有一个具体的地址。如果想要给它赋值一个地址，就必须调用bind()函数，否则就当调用connect()、listen()时系统会自动随机分配一个端口。

   2. bind()

      ```c
      int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
      ```

      bind()函数把一个地址族中的特定地址赋给socket。例如对应AF_INET、AF_INET6就是把一个ipv4或ipv6地址和端口号组合赋给socket。

      三个参数：

      - sockfd：即socket描述字，它是通过socket()函数创建了，唯一标识一个socket。bind()函数就是将给这个描述字绑定一个名字。

      - addr：一个const struct sockaddr *指针，指向要绑定给sockfd的协议地址。这个地址结构根据地址创建socket时的地址协议族的不同而不同：

        ```c
        ipv4对应的是
        struct sockaddr_in {
            sa_family_t    sin_family; 
            in_port_t      sin_port;   
            struct in_addr sin_addr;   
        };
        
        
        struct in_addr {
            uint32_t       s_addr;     
        };
        
        ipv6对应的是：
        struct sockaddr_in6 { 
            sa_family_t     sin6_family;    
            in_port_t       sin6_port;      
            uint32_t        sin6_flowinfo;  
            struct in6_addr sin6_addr;      
            uint32_t        sin6_scope_id;  
        };
        
        struct in6_addr { 
            unsigned char   s6_addr[16];    
        };
        
        Unix域对应的是：
        #define UNIX_PATH_MAX    108
        
        struct sockaddr_un { 
            sa_family_t sun_family;                
            char        sun_path[UNIX_PATH_MAX];   
        };
        ```

      - addrlen：对应的是地址的长度。

      > 通常服务器在启动的时候都会绑定一个众所周知的地址（如ip地址+端口号），用于提供服务，客户就可以通过它来接连服务器；而客户端就不用指定，有系统自动分配一个端口号和自身的ip地址组合。这就是为什么通常服务器端在listen之前会调用bind()，而客户端就不会调用，而是在connect()时由系统随机生成一个。

   3. listen()、connect()

      ```c
      int listen(int sockfd, int backlog);
      int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
      ```

      如果作为一个服务器，在调用socket()、bind()之后就会调用listen()来监听这个socket，如果客户端这时调用connect()发出连接请求，服务器端就会接收到这个请求。

      listen()函数两个参数

      - 第一个参数即为要监听的socket描述字，
      - 第二个参数为相应socket可以排队的最大连接个数。

      socket()函数创建的socket默认是一个主动类型的，listen函数将socket变为被动类型的，等待客户的连接请求。

      connect()函数三个参数

      - 第一个参数即为客户端的socket描述字，
      - 第二参数为服务器的socket地址，
      - 第三个参数为socket地址的长度。

      客户端通过调用connect函数来建立与TCP服务器的连接。

   4. accept()

      ```c
      int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
      ```

      - TCP服务器端依次调用socket()、bind()、listen()之后，就会监听指定的socket地址了。
      - TCP客户端依次调用socket()、connect()之后就想TCP服务器发送了一个连接请求。
      - TCP服务器监听到这个请求之后，就会调用accept()函数取接收请求，这样连接就建立好了。之后就可以开始网络I/O操作了，即类同于普通文件的读写I/O操作。

      accept()函数三个参数：

      - 第一个参数为服务器的socket描述字，
      - 第二个参数为指向struct sockaddr *的指针，用于返回客户端的协议地址，
      - 第三个参数为协议地址的长度。

      如果accpet成功，那么其返回值是由内核自动生成的一个全新的描述字，代表与返回客户的TCP连接。

      >accept的第一个参数为服务器的socket描述字，是服务器开始调用socket()函数生成的，称为监听socket描述字；而accept函数返回的是已连接的socket描述字。一个服务器通常通常仅仅只创建一个监听socket描述字，它在该服务器的生命周期内一直存在。内核为每个由服务器进程接受的客户连接创建了一个已连接socket描述字，当服务器完成了对某个客户的服务，相应的已连接socket描述字就被关闭。

   5. read()、write()等函数

      - read()/write()
      - recv()/send()
      - readv()/writev()
      - **recvmsg()/sendmsg()**
      - recvfrom()/sendto()

      ```c
      推荐使用recvmsg()/sendmsg()函数，这两个函数是最通用的I/O函数，实际上可以把上面的其它函数都替换成这两个函数。它们的声明如下：
             ssize_t read(int fd, void *buf, size_t count);
             ssize_t write(int fd, const void *buf, size_t count);
      
             ssize_t send(int sockfd, const void *buf, size_t len, int flags);
             ssize_t recv(int sockfd, void *buf, size_t len, int flags);
      
             ssize_t sendto(int sockfd, const void *buf, size_t len, int flags,
                            const struct sockaddr *dest_addr, socklen_t addrlen);
             ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,
                              struct sockaddr *src_addr, socklen_t *addrlen);
      
             ssize_t sendmsg(int sockfd, const struct msghdr *msg, int flags);
             ssize_t recvmsg(int sockfd, struct msghdr *msg, int flags);
      ```

      read函数是负责从fd中读取内容.当读成功时，read返回实际所读的字节数，如果返回的值是0表示已经读到文件的结束了，小于0表示出现了错误。如果错误为EINTR说明读是由中断引起的，如果是ECONNREST表示网络连接出了问题。

      write函数将buf中的nbytes字节内容写入文件描述符fd.成功时返回写的字节 数。失败时返回-1，并设置errno变量。在网络程序中，当我们向套接字文件描述符写时有俩种可能。1)write的返回值大于0，表示写了部分或者是 全部的数据。2)返回的值小于0，此时出现了错误。我们要根据错误类型来处理。如果错误为EINTR表示在写的时候出现了中断错误。如果为EPIPE表示 网络连接出现了问题(对方已经关闭了连接)。

   6. close()

      ```c
      int close(int fd);
      ```

      close操作只是使相应socket描述字的引用计数-1，只有当引用计数为0的时候，才会触发TCP客户端向服务器发送终止连接请求。

4. socket中TCP的三次握手建立连接详解

   我们知道tcp建立连接要进行“三次握手”，即交换三个分组。大致流程如下：

   - 客户端向服务器发送一个SYN J
   - 服务器向客户端响应一个SYN K，并对SYN J进行确认ACK J+1
   - 客户端再想服务器发一个确认ACK K+1

   只有就完了三次握手，但是这个三次握手发生在socket的那几个函数中呢？请看下图：

   ![image](/201012122157476286.png)
   $$
   总结：客户端的connect在三次握手的第二个次返回，而服务器端的accept在三次握手的第三次返回。
   $$

### 三、.NET 下的Socket编程

> 下面的例子摘自：https://www.cnblogs.com/zxtceq/p/7728543.html

1. 最基本简单的socket通信（同步通信方法，只连接一次）

   - 服务器端

     ```c++
     int port = 6000;
     string host = "127.0.0.1";
     
     IPAddress ip = IPAddress.Parse(host);
     IPEndPoint ipe = new IPEndPoint(ip, port);
     
     Socket sSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
     sSocket.Bind(ipe);
     sSocket.Listen(0);
     Console.WriteLine("服务器监听已经打开，请等待");
     
     // 接收信息(收到客户端请求后)
     Socket serverSocket = sSocket.Accept();
     Console.WriteLine("连接已经建立");
     string recStr = "";
     byte[] recByte = serverSocket.Receive(recByte, recByte.Length, 0);
     recStr += Encoding.ASCII.GetString(recByte, 0, bytes);
     
     // 发送信息
     Console.WriteLine("服务器端获得信息：{0}", recStr);
     string sendStr = "send to client: hello";
     byte[] sendByte = Encoding.ASCII.GetBytes(sendStr);
     serverSocket.Send(sendByte, sendByte.Length, 0);
     serverSocket.Close();
     sSocket.Close();
     ```

   - 客户端

     ```c++
     int port = 6000;
     string host = "127.0.0.1";
     
     IPAddress ip = IPAddress.Parse(host);
     IPEndPoint ipe = new IPEndPoint(ip, port);
     
     Socket clientSocket = new Socket(AddressFamilly.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
     clientSocket.Connect(ipe);
     
     // 发送信息
     string sendStr = "send to server: hello, ni hao";
     byte[] sendBytes = Encoding.ASCII.GetBytes(sendStr);
     clientSocket.Send(sendBytes);
     
     // 接收信息
     string recStr = "";
     byte[] recBytes = new byte[4096];
     int bytes = clientSocket.Receive(recBytes, recBytes.Length, 0);
     recStr += Encoding.ASCII.GetString(recBytes, 0, bytes);
     Console.WriteLine(recStr);
     
     clientSocket.Close();
     ```

2. 异步Socket通信方法，这样Server和Client端可以进行多次互发信息的通信而不用担心通道会关闭。

   服务器端和客户端发送信息和接收信息的方法是一样的。

   - 异步连接的方法

     ```c#
     public void Connect(IPAddress ip, int port)
     {
     	this.clientSocket.BeginConnect(ip, port, new AsyncCallback(ConnectCallback), this.clientSocket);
     }
     
     private void ConnectCallback(IAsyncResult ar)
     {
         try
         {
             Socket handler = (Socket)ar.AsyncState;
             handler.EndConnect(ar);
         }
         catch(SocketException ex)
         {}
     }
     ```

   - 异步发送信息的方法

     ```c#
     public void Send(string data)
     {
     	Send(System.Text.Encoding.UTF8.GetBytes(data));
     }
     
     private void Send(byte[] byteData)
     {
         try
         {
             int length = byteData.Length;
             byte[] head = BitConverter.GetBytes(length);
             byte[] data = new byte[head.Length + byteData.Length];
             Array.Copy(head, data, head.Length);
             Array.Copy(byteData, data, byteData.Length);
             
             this.clienSocket.BeginSend(data, 0, data.Length, 0, new AsyncCallback(SendCallback), this.clientSocket);
         }
         catch(SocketException ex)
         {}
     }
     
     private void SendCallback(IAsyncResult ar)
     {
         try
         {
             Socket handler = (Socket)ar.AsyncState;
             handler.EndSend(ar);
         }
         catch(SocketException ex)
         {}
     }
     ```

   - 异步接收信息的方法：

     ```c#
     public void ReceiveData()
     {
     	this.clientSocket.BeginReceive(MsgBuffer, 0, MsgBuffer.Length, 0, new AsyncCallback(ReceiveCallback), null);
     }
     
     private void ReceiveCallback(IAsyncResult ar)
     {
         try
         {
             int REnd = this.clientSocket.EndReceive(ar);
             if(REnd>0)
             {
                 byte[] data = new byte[REnd];
                 Array.Copy(MsgBuffer, 0, data, 0, REnd);
                 
                 // 此处可以对data进行各种处理
                 
                 this.clientSocket.BeginReceive(MsgBuffer, 0, MsgBuffer.Length, 0, new AsyncCallback(ReceiveCallback), null);
             }
             else
             {
                 dispose();
             }
         }
         catch(SocketException ex)
         {}
     }
     
     private void dispose()
     {
         try
         {
             this.clientSocket.Shutdown(SocketShutdown.Both);
             this.clientSocket.Close();
         }
         catch(Exception ex)
         {}
     }
     ```

     

















