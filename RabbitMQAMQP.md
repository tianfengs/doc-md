---
typora-root-url: pics
typora-copy-images-to: pics
---

## RabbitMQ基础

[RabbitMQ基础概念详细介绍]: https://www.cnblogs.com/williamjie/p/9481774.html
[RabbitMQ基础]: https://www.jianshu.com/p/e55e971aebd8



### 优势

消息服务擅长于解决多系统、异构系统间的数据交换（消息通知/通讯）问题，你也可以把它用于系统间服务的相互调用（RPC）。本文将要介绍的RabbitMQ就是当前最主流的消息[中间件](http://www.diggerplus.org/archives/tag/中间件)之一。

###  RabbitMQ简介

AMQP，即Advanced Message Queuing Protocol，高级消息队列协议，是应用层协议的一个开放标准，为面向消息的[中间件](http://www.diggerplus.org/archives/tag/中间件)设计。消息中间件主要用于组件之间的解耦，消息的发送者无需知道消息使用者的存在，反之亦然。
AMQP的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全。
RabbitMQ是一个开源的AMQP实现，服务器端用Erlang语言编写，支持多种客户端，如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。

### 基本概念

1. ConnectionFactory，Connection，Channel对象

   Connection是RabbitMQ的socket链接，它封装了socket协议相关部分逻辑。就是一个TCP的连接。Producer和Consumer都是通过TCP连接到RabbitMQ Server的。以后我们可以看到，程序的起始处就是建立这个TCP连接。

   ConnectionFactory为Connection的制造工厂。
   Channel是我们与RabbitMQ打交道的最重要的一个接口，我们大部分的业务操作是在Channel这个接口中完成的，包括定义Queue、定义Exchange、绑定Queue与Exchange、发布消息等。虚拟连接。它建立在上述的TCP连接中。数据流动都是在Channel中进行的。也就是说，一般情况是程序起始建立TCP连接，第二步就是建立这个Channel。

2. **Message Queue**队列对象

   Queue（队列）是RabbitMQ的内部对象，用于存储消息。

   RabbitMQ中的消息都只能存储在Queue中，生产者（下图中的P）生产消息并最终投递到Queue中，消费者（下图中的C）可以从Queue中获取消息并消费。

   ![img](https://img2018.cnblogs.com/blog/774371/201908/774371-20190819103814085-804287529.png)![img](https://img2018.cnblogs.com/blog/774371/201908/774371-20190819103830954-867723738.png)

3. **Exchange**

   生产者将消息发送到Exchange（交换器，下图中的X），由Exchange将消息路由到一个或多个Queue中（或者丢弃）。

   ![img](/774371-20190819104028789-412276700.png)

   **Exchange是按照什么逻辑将消息路由到Queue的？**

   **根据routing key --> Exchange Type -->Binding 的联合使用，来决定分发规则**

4. routing key

   生产者在将消息发送给Exchange的时候，一般会指定一个routing key。

   在Exchange Type与binding key固定的情况下（在正常使用时一般这些内容都是固定配置好的），我们的生产者就可以在发送消息给Exchange时，通过指定routing key来决定消息流向哪里。

   RabbitMQ为routing key设定的长度限制为255 bytes。

5. **Binding** 和 Binding key

   RabbitMQ中通过Binding将Exchange与Queue关联起来，这样RabbitMQ就知道如何正确地将消息路由到指定的Queue了。

   ![img](/774371-20190819104128931-1338459538.png)

   在绑定（Binding）Exchange与Queue的同时，一般会指定一个binding key；消费者将消息发送给Exchange时，一般会指定一个routing key；当binding key与routing key相匹配时，消息将会被路由到对应的Queue中。

6. Exchange Types

   RabbitMQ常用的Exchange Type有fanout、direct、topic、headers这四种（AMQP规范里还提到两种Exchange Type，分别为system与自定义，这里不予以描述），下面分别进行介绍。

   - **fanout**

     它把所有发送到该Exchange的消息路由到所有与它绑定的Queue中。广播。

     ![RabbitMQ系列二（构建消息队列） - 网易杭研后台技术中心 - 网易杭研后台技术中心的博客](http://static.oschina.net/uploads/img/201312/20112307_RULl.png)

   - **direct**

     它会把消息路由到那些binding key与routing key完全匹配的Queue中。按字典匹配。

     ![RabbitMQ系列二（构建消息队列） - 网易杭研后台技术中心 - 网易杭研后台技术中心的博客](http://static.oschina.net/uploads/img/201312/20112307_334Q.png)

   - **topic**

     它与direct类型的Exchage相似，也是将消息路由到binding key与routing key相匹配的Queue中，但按照正则表达式，对RoutingKey与BindingKey进行匹配，如果匹配成功，它约定：

     - routing key用点号“. ”分隔的字符串（被点号“. ”分隔开的每一段独立的字符串称为**单词**），如“stock.usd.nyse”、“nyse.vmw”、“quick.orange.rabbit”
     - binding key与routing key一样也是句点号“. ”分隔的字符串
     - binding key中用两种特殊字符“\*”与“#”用于模糊匹配，“\*”匹配1个单词，“#”匹配0...n个单词

     ![RabbitMQ系列二（构建消息队列） - 网易杭研后台技术中心 - 网易杭研后台技术中心的博客](http://static.oschina.net/uploads/img/201312/20112307_0Gpx.png)

   - headers

     headers类型的Exchange不依赖于routing key与binding key的匹配规则来路由消息，而是根据发送的消息内容中的headers属性进行匹配。

     在绑定Queue与Exchange时指定一组键值对；当消息发送到Exchange时，RabbitMQ会取到该消息的headers（也是一个键值对的形式），对比其中的键值对是否完全匹配Queue与Exchange绑定时指定的键值对；如果完全匹配则消息会路由到该Queue，否则不会路由到该Queue。

### 消息处理

1. Message acknowledgment 消息回执

   可以要求消费者在消费完消息后发送一个回执给RabbitMQ，RabbitMQ收到消息回执（Message acknowledgment）后才将该消息从Queue中移除；如果RabbitMQ没有收到回执并检测到消费者的RabbitMQ连接断开，则RabbitMQ会将该消息发送给其他消费者（如果存在多个消费者）进行处理。

   这里会产生另外一个问题，如果我们的开发人员在处理完业务逻辑后，忘记发送回执给RabbitMQ，这将会导致严重的bug——Queue中堆积的消息会越来越多；消费者重启后会重复消费这些消息并重复执行业务逻辑…

2. Message durability消息持久化

   我们可以将Queue与Message都设置为可持久化的（durable），这样可以保证绝大部分情况下我们的RabbitMQ消息不会丢失。但依然解决不了小概率丢失事件的发生（比如RabbitMQ服务器已经接收到生产者的消息，但还没来得及持久化该消息时RabbitMQ服务器就断电了），如果我们需要对这种小概率事件也要管理起来，那么我们要用到事务。

3. Prefetch count预分配消息数

   我们可以通过设置prefetchCount来限制Queue每次发送给每个消费者的消息数，比如我们设置prefetchCount=1，则Queue每次给每个消费者发送一条消息；消费者处理完这条消息后Queue会再给该消费者发送一条消息。

### RPC

MQ本身是基于异步的消息处理，前面的示例中所有的生产者（P）将消息发送到RabbitMQ后不会知道消费者（C）处理成功或者失败（甚至连有没有消费者来处理这条消息都不知道）。
但实际的应用场景中，我们很可能需要一些同步处理，需要同步等待服务端将我的消息处理完成后再进行下一步处理。这相当于RPC（Remote Procedure Call，远程过程调用）。在RabbitMQ中也支持RPC。

![img](/774371-20190819104253450-1490098886.png)

RabbitMQ中实现RPC的机制是：

- 客户端发送请求（消息）时，在消息的属性（MessageProperties，在AMQP协议中定义了14中properties，这些属性会随着消息一起发送）中设置两个值replyTo（一个Queue名称，用于告诉服务器处理完成后将通知我的消息发送到这个Queue中）和correlationId（此次请求的标识号，服务器处理完成后需要将此属性返还，客户端将根据这个id了解哪条请求被成功执行了或执行失败）
- 服务器端收到消息并处理
- 服务器端处理完消息后，将生成一条应答消息到replyTo指定的Queue，同时带上correlationId属性
- 客户端之前已订阅replyTo指定的Queue，从中收到服务器的应答消息后，根据其中的correlationId属性分析哪条请求被执行了，根据执行结果进行后续业务处理





​	