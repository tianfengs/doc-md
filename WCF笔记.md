---
typora-root-url: pics
typora-copy-images-to: pics
---

# msdn上的官方文档
>
> 参考： https://docs.microsoft.com/zh-cn/dotnet/framework/wcf/guide-to-documentation

## 一、WCF概念

WCF（Windows Communication Foundation）是一个框架，用于构建面向服务的应用程序。使用WCF可以异步发送数据消息，从一个服务结点到另一个结点。服务可**以由IIS承载**的持续服务的一部分，也可以**由应用程序中承载**的服务。简单的消息**可以是作为XML**发送的单个字符或单个单词，复杂消息**可以是二进制数据流**。

```
应用场景举例：
。 处理企业事务的安全服务
。 将当前数据提供给其它服务的服务
。 使两个人能够实时通信或交换数据的聊天服务
。 轮询一个或多个服务以查找数据并将其展示的面板服务
。 等等
```

总之，WCF旨在提供一种创建Web服务和Web服务客户端的可管理方法。

### 1. WCF的功能

- 服务向导

  使用WS标准（Web Service）一个结果是WCF是你能够创建**面向服务**的应用程序。面向服务的体系结构（SOA）依赖Web服务放松和接收数据。

  这些服务具有松耦合的优点，而不是从一个程序到另一程序的硬编码。

  松耦合意味着只要符合基本协定，则在任何平台上创建的任何客户端都可以连接到任何服务。

- 互操作性

  WCF实现Web服务互操作性的现代行业标准。参见。。。

- 多种消息模式

  采用多种模式之一交换消息。

  最常用的模式：请求/答复模式，其中一个终结点请求数据，另一个终结点进行答复。其它模式：比如单向模式，其中一个终结点发送消息，而不期望得到答复。双工交换模式，两个终结点建立连接并来回发送数据。

  如何实现不同的消息交换模式，使用WCF参阅“协定”

- 服务元数据

  WCF支持使用WSDL、XML架构和Ws-policy等行业标准中指定的格式发布服务元数据。
  
  此元数据可用于自动生成并配置客户端用于访问WCF。
  
  可通过HTTP和HTTPS来发布元数据，也可使用Web服务元数据交换标准来发布元数据。
  
- 数据协定

  WCF还包括代码友好code friendly提供自己想要强制实施的约定的方法。

  **数据协定**就是其中一种通用类型的协定。用Visual C#编码时，最简单的方法是**使用属于数据实体的属性**创建**表示该数据实体的类**。WCF包含用于在这一简便方式处理数据的综合系统。

  在创建了表示数据的类之后，服务会自动生成使客户端能够符合所设计数据类型的元数据。

- 安全性

  可以对消息进行加密以保护隐私，而且可以要求用户对其自身进行身份验证，然后才允许接收消息。

  可以使用众所周知的标准（如SSL或WS-SecureConversation）实现安全性。
  
- 多种传输和编码方式

  可通过多种内置传输协议和编码中的任意一种发送消息。

  最常用的协议和编码时发送文本编码的soap消息，使用在万维网上使用超文本传输协议（HTTP）。或者，WCF允许您将消息发送通过TCP、命名管道或MSMQ。这些消息可以编码为文本，也可以使用优化的二进制格式。

  使用MTOM标准可有效的发送二进制数据。







# WCF编程权威指南

## 一、WCF应用程序基础

- 编写WCF应用项目的基本思路
- ServiceHost类的用法
- 客户端通过服务引用个来调用WCF服务
- 使用ChannelFactory类来调用WCF服务

### 1.1. WCF服务的基本实现步骤

   WCF服务端的实现步骤：

   - 定义协定：

     主要是**服务协定 ServiceContract**。服务协定是一个接口，接口本身作为服务协定的容器。

     在服务协定内，可以包含1到多个**服务操作协定 OperationContract(操作协定)**，映射到接口类型上，操作协定是一个方法。服务操作必须是方法，不能是属性和事件等成员。

     还有**数据协定 DataContract** 和**消息协定 MessageContract**。

   - 实现服务类：

     服务协定只是一个接口，它可以在服务器和客户端之间共享，作为双方用来通信的一种“**约定**”。

     服务协定只是一个接口，并不包含实际的代码实现，因此，需要一个类来实现协定接口，提供具体的功能，这就是**服务类**。

     客户端会通过访问服务协定接口的某个方法来调用WCF服务，当消息发送到服务器后，服务器会找到实现协定接口的类，并将其实例化，然后调用与操作协定匹配的方法（即**服务协定接口的实现方法**）。

   - **寄宿WCF服务**：

     对于Web项目，可以不考虑这一步，因为项目模板会生成相关的代码，使WCF服务能够在IIS服务上运行。

     对于独立进程中的WCF服务，必须使用ServiceHost类来启动服务，之后客户端才能调用服务。

   **完成一个简单的WCF服务应用程序**:

   - 以管理员身份运行Visual Studio，然后新建一个控制台应用程序。

     由于在进程中寄宿WCF服务时，某些情况下要求运行用户具有管理员权限。以管理员身份运行开发环境，这样在调试的时候，管理员权限会传递到示例进程中，以避免因权限不够而运行失败。

   - 与WCF相关的许多类型都位于程序集**System.ServiceModel**中，因此需要在项目中添加对该程序集的引用。

   - **定义服务协定接口**，本示例中接口的名字为IDemo，代码如下：

     ```c#
     [ServiceContract]
     public interface IDemo
     {
     	[OperationContract]
     	int Add(int a, int b);
     	
     	void Run();
     }
     ```

     服务协定的定义与一般的接口类型无异，Add方法会被视为**操作协定公开**，Run方法不会被作为操作协定公开。

   - 定义一个类，**实现服务协定接口**：

     ```
     internal class DemoService : IDemo
     {
     	public int Add(int a, int b)
     	{
     		return a+b;
     	}
     	
     	public void Run()
     	{
     		// 不会公开
     	}
     }
     ```

     服务器与客户端是通过协定来交互的，它只在服务器内部使用，因此该类不应该声明为public，可以定义为**internal**。

   - 创建ServiceHost实例，寄宿WCF服务

     ```c#
     // 基址
     Uri baseAddress = new Uri("httpl://localhost:500");
     
     using(ServiceHost host = new ServiceHost(typeof(DemoService), baseAddress))
     {
     	// 添加终结点
     	BasicHttpBinding binding = new BasicHttpBinding();
     	host.AddServiceEndpoint(typeof(IDemo), binding, "demo");
     	
     	// 打开服务
     	host.Open();
     	Console.WriteLine("WCF服务已启动。")
     	
     	Console.Read();
     	host.Close();	// 关闭服务
     }
     ```

     在实例化ServiceHost类时，需要传递一个表示服务实现类的Type作为参数，本例中为DemoService类，因为要进行实例化。另外，还有一个可选参数——baseAddresses，它是一个URI数组，可以包含多个URI，它表示服务公开的基础地址。

     调用ServiceHost类的构造函数后，可以对服务进行一些设置，比如**添加服务终结点（EndPoint）**。

     服务终结点用于向客户端公开WCF服务，它需要指定一个有效的地址，**本例中指定了一个相对地址demo**，结合传递给ServiceHost类构造函数的基址，该服务终结点的地址变为：

     ```
     http://localhost:500/demo
     ```

     如果在调用ServiceHost构造函数时没有指定任何基址，那么在添加终结点时就必须使用完整的URI：

     ```c#
     host.AddServiceEndpoint(typeof(IDemo), binding, "http://localhost:500/demo");
     ```

     服务终结点包含：一到多个地址(URI)、一个绑定（Binding）、一个协定（Contract）

     协定就是前面定义的服务协定。

     绑定负责通信层控制的，比如采用HTTP协议还是TCP协议通信、传输数据的缓冲区有多大等。

     将**地址、绑定和协定**三者结合起来，组成了**终结点**的基本功能，即WCF服务将在地址 http://localhost:500 上公开 IDemo服务协定，并以HTTP方式通信，这样一来，客户端就能通过这个地址来找到服务并进行调用了。

     设置完ServiceHost各个参数后，就可以调用Open方法打开服务，只有在服务出游打开状态下，客户端才能访问。在调用Open方法之前必须完成所有的服务配置，如向服务添加一个终结点，或者可以配置服务器证书等，服务一旦打开，就不能进行配置了。
     
     配置WCF服务：**使用代码**，**使用配置文件**。
     
     注意：由于服务器未发布元数据，因此访问 http://localhost:500 的时候，会显示如下错误信息：
     
     ```
     Service
     
     这是 Windows© Communication Foundation 服务。
     
     当前已禁用此服务的元数据发布。
     
     如果具有该服务的访问权限，则可以通过完成下列步骤来修改 Web 或应用程序配置文件以便启用元数据发布:
     1. 创建下列服务行为配置，或将 <serviceMetadata> 元素添加到现有服务行为配置:
     <behaviors>
         <serviceBehaviors>
             <behavior name="MyServiceTypeBehaviors" >
                 <serviceMetadata httpGetEnabled="true" />
             </behavior>
         </serviceBehaviors>
     </behaviors>
     2. 将行为配置添加到服务:
     <service name="MyNamespace.MyServiceType" behaviorConfiguration="MyServiceTypeBehaviors" >
     注意: 服务名称必须与服务实现的配置名称相匹配。
     
     3. 将下列终结点添加到服务配置:
     <endpoint contract="IMetadataExchange" binding="mexHttpBinding" address="mex" />
     注意: 服务必须有一个 http 基址以便添加此终结点。
     下面是启用了元数据发布的示例服务配置文件:
     <configuration>
         <system.serviceModel>
      
             <services>
                 <!-- 注意: 服务名称必须与服务实现的配置名称相匹配。 -->
                 <service name="MyNamespace.MyServiceType" behaviorConfiguration="MyServiceTypeBehaviors" >
                     <!-- 添加下列终结点。 -->
                     <!-- 注意: 服务必须有一个 http 基址以便添加此终结点。 -->
                     <endpoint contract="IMetadataExchange" binding="mexHttpBinding" address="mex" />
                 </service>
             </services>
      
             <behaviors>
                 <serviceBehaviors>
                     <behavior name="MyServiceTypeBehaviors" >
                         <!-- 将下列元素添加到服务行为配置中。 -->
                         <serviceMetadata httpGetEnabled="true" />
                     </behavior>
                 </serviceBehaviors>
             </behaviors>
      
         </system.serviceModel>
     </configuration>
     有关发布元数据的详细信息，请参见下列文档: http://go.microsoft.com/fwlink/?LinkId=65455。
     ```

### 1.2. 调用WCF服务

   客户端可以通过两种方法来调用WCF服务

   - 服务引用

     在Visual Studio开发环境中，只需要**添加服务引用**，开发工具就会自动生成客户端代理类。

     要让WCF服务支持在客户端以添加服务引用的方式生成代理类型，WCF服务必须**向客户端公开元数据（Metadata）**。元数据将以WSDL文档的形式公开，它的作用是描述服务的一些基本信息，如在哪个地址可以找到服务，服务包含哪些操作可以被调用，调用服务时所使用的输入输出消息的结构。

     Visual Studio开发环境是根据WCF服务所公开的元数据来生成客户端代理类。

     下面示例将演示一个简单的数学计算服务：

     - 首先定义服务协定

       ```
       [ServiceContract]
       public interface IService
       {
       	[OperationContract]
       	double Sqr(double i);
       }
       ```

     - 接着是实现服务协定接口，实现接口的类只在服务器上执行，不需要对外公开
     
       ```
       class MyService:IService
       {
       	public double Sqr(double i)
       	{
       		return i*i;
       	}
       }
       ```
     
     - 接着实例化一个ServiceHost对象，用以运行WCF服务
     
       ```c#
       // 服务基址
       Uri baseUri = new Uri("http://localhost:500");
       
       using (ServiceHost host = new ServiceHost(typeof(MyService), baseUri))
       {
       	// 公开服务的元数据
       	ServiceMetadataBehavior metadata = null;
       	host.Description.Behaviors.Find<ServiceMetadataBehavior>();
       	if(metadata == null)
       	{
       		metadata = new ServiceMetadataBehavior();
       	}
       	metadata.HttpGetEnabled = true;		// 必需
       	host.Description.Behaviors.Add(metadata);
       	
       	// 打开服务
       	host.Open();
       	Console.WriteLine("服务已运行")；
       	
       	Console.Read();
       }
       ```
     
       本示例，实例化ServiceHost对象后，并没有调用AddServiceEndpoint方法来添加服务终结点，只是在ServiceHost类的构造函数调用时，传递了服务类的Type及服务基址。这是因为ServiceHost具有一个默认终结点处理方案，如果没有显式调用AddServiceEndPoint()，那么ServiceHost会根据基址和服务类自动生成服务终结点。
       
       本例基址是：http://localhost:500 ，则通信协议是 HTTP，使用的绑定是 BasicHttpBinding；如果使用 net.tcp 协议，就会使用NetTcpBinding
       
       MyService服务类只实现了一个服务协定——IService，所以默认的服务终结点的服务协定将指向IService
       
       由于是默认生成的服务终结点，因此其地址与基址相同。
       
       另外，最为关键的一点是：一定要将一个ServiceMetadataBehavior实例添加到服务的Behavior集合中。
       
       **行为（Behavior）**用来描述服务将支持哪些扩展功能，比如是否支持获取元数据、是否把服务器上的错误信息传回给客户端/服务类实例在何时被清理等。
       
       本例中则需要**ServiceMetadataBehavior类**来使服务支持**向客户端公开元数据**。
       
       注意：开启此公开元数据的服务后，登录http://localhost:500 获得如下信息：
       
       ```
       MyService 服务
       
       已创建服务。
       
       若要测试此服务，需要创建一个客户端，并将其用于调用该服务。可以使用下列语法，从命令行中使用 svcutil.exe 工具来进行此操作:
       svcutil.exe http://localhost:500/?wsdl
       您还可以访问作为单个文件的服务说明:
       http://localhost:500/?singleWsdl
       这将生成一个配置文件和一个包含客户端类的代码文件。请将这两个文件添加到客户端应用程序，并使用生成的客户端类来调用服务。例如:
       C#
       class Test
       {
           static void Main()
           {
               ServiceClient client = new ServiceClient();
       
               // 使用 "client" 变量在服务上调用操作。
       
               // 始终关闭客户端。
               client.Close();
           }
       }
       
       Visual Basic
       Class Test
           Shared Sub Main()
               Dim client As ServiceClient = New ServiceClient()
               ' 使用 "client" 变量在服务上调用操作。
       
               ' 始终关闭客户端。
               client.Close()
           End Sub
       End Class
       ```
       
       访问元数据XML文档`http://localhost:500/?wsdl` 
       
       访问服务说明页`http://localhost:500/?singleWsdl` 
       
       用`svcutil.exe http://localhost:500/?wsdl`生成两个客户端文件，一个是客户端类的代码文件，一个是客户端配置文件，可以直接用来调用服务
       
       - MyService.cs
       - output.config
       
       使用客户端类调用服务的代码如下：
       
       ```c#
       static void Main(string[] args)
       {
           ServiceClient client = new ServiceClient();
           double retval = client.Sqr(10);
           Console.WriteLine($"10x10计算结果是：{retval}");
           Console.ReadKey();
       }
       
       屏幕输出为：
       10x10计算结果是：100
       ```
     
   - 接下来实现客户端：
   
     示例客户端是一个WPF项目，一个TextBox，一个Button，一个TextBlock：
   
     ```xaml
     <StackPanel Margin="15">
     	<TextBlock Text="请输入数值：" />
     	<TextBox Name="textInput" Margin="3,2,0,0" Width="100" HorizontalAlignment="Left" />
     	<Button Content="调用服务" HorizontalAlignment="Left" Padding="5,2" Margin="2,12,0,10" Click="OnClick" />
     	<TextBlock Name="tbResult" Foreground="Red" FontSize="15" FontWeight="Bold" Margin="2,3" />
     </StackPanel>
     ```
   
     此时先运行WCF服务器，然后打开解决方案资源管理器窗口，右击"引用"节点，添加服务引用，输入WCF服务的基址：http://localhost:500, 点击"转到"，最后点击"确定"。
   
     Visual Studio开发环境生成三个类型：
   
     - 根据服务协定生成一个接口
     
       ```
       public interface IService
       {
       	double Sqr(doublie i);									// 同步接口
       	System.Threading.Tasks.Task<double> SqrAsync(double i); // 异步接口
       }
       ```
     
     - 从刚才生成的IService接口与IClientChannel接口派生一个新接口
     
       ```
       public interface IServiceChannel : TestClient.WSSample.IService, System.ServiceModel.IClientChannel
       {
       }
       ```
     
       这只是可以让服务代理类的调用代码能够访问**客户端通道(ClientChannel)**的一些方法，如：调用Close方法关闭通信通道。开发者不需要实现IClientChannel接口，因为WCF运行时内部有默认的实现类型。
     
     - 最后一个类，派生自ClientBase<TChannel>，泛型参数TChannel可以用服务协定接口类型来替换，本例，可以用IService来替换
     
       ```
       public partial class ServiceClient : System.ServiceModel.ClientBase<TestClient.WSSample.IService>, TestClient.WSSample.IService
       {
     	public ServiceClient()
       	{
       		public ServiceClient(string endpointConfigurationName): 
       			base(endpointConfigurationName){		
       		}
       		
       		public ServiceClient(string endpointConfigurationName, string remoteAddress) :
       			base(endpointConfigurationName, remoteAddress){
               }
               
               public ServiceClient(System.ServiceModel.Channels.Binding binding, System.ServiceModel.EndpointAddress remoteAddress) : 
               	base(binding, remoteAddress){        	
               }
               
               public double Sqr(double i){
               	return base.Channel.Sqr(i);
               }
               
               public System.Threading.Tasks.Task<double> SqrAsync(double i){
               	return base.Channel.SqrAsync(i);
               }
       	}
       }
       ```
       
       基类ClientBase<TChannel>有一个**Channel属性**，派生类可以通过这个属性来访问服务协定的成员。
       
       生成代理类型后，服务调用就很简单了：
       
       ```
       private async void OnClick(object sender, RoutedEventArgs e)
       {
       	Button btn = e.Source as Button;
       	double ind = double.Parse(txtInput.Text);
       	WSSample.ServiceClient client = new WSSample.ServiceClient();
       	
       	// 调用服务操作
       	double retval = await client.SqrAsync(ind);
       	
       	// 显示结果
       	tbResult.Text = $"计算结果：{retval}";
       	btn.IsEnabled = true;
       }
       ```

- 通道工厂

  通过添加服务引用来生成客户端代理类，可以很简单的调用WCF服务，但这种方法要求WCF服务器必须公开元数据才能使用，**如果WCF服务器未公开元数据**，就要使用**通道工厂**。

  通道工厂，ChannelFactory类，是一个抽象类，不能实例化，因此应使用它的派生类——ChannelFactory<TChannel>。

  - 泛型参数TChannel指的是服务协定的接口类型。
  - 通道工厂公开CreateChannel方法，调用该方法后，返回TChannel类型参数所指定的服务协定的接口类型。使用CreateChannel方法返回的协定实例，就可以调用WCF服务了。
  - 尽管服务协定是接口类型，不能实例化，但运行库会自动生成一个代理实例，并以协定接口的类型返回，当代码通过协定接口调用WCF服务时，实际内部是由这个**代理实例**向服务器发送消息。

  通道工厂两种使用方法:

  - 直接调用静态版本的CreateChannel方法，得到服务协定的引用，然后调用WCF服务
  - 先调用ChannelFactory<TChannel>类的构造函数进行实例化，然后调用实例版本的CreateChannel方法，来获取服务协定的引用，然后调用WCF服务。

  **实例**：

  - 前期准备：使用ChannelFactory<TChannel>类调用WCF前

    - 必须知道服务终结点Uri，本例为：http://localhost:900/service
    - Binding对象，此对象负责处理服务器和客户端之间通信的传输协议相关的参数，参与通信的双方必须使用相同的协议和参数。本例为：BasicHttpBinding

  - 使用通道工厂来调用一个加法运算的WCF服务

    ```
    // 服务终结点URI
    Uri svepUri = new Uri("http://localhost:900/service");
    
    // 创建终结点地址包装对象
    EndpointAddress epaddr = new EndpointAddress(svepUri);
    
    // 创建用于与服务通信的绑定
    BasicHttpBinding binding = new BasicHttpBinding();
    
    // 引用服务协定
    ITestService svcontractInstance = ChannelFactory<ITestService>.CreateChannel(binding, epaddr);
    
    // 调用这个WCF服务
    int res = svcontractInstance.Add(3,17);
    Console.WriteLine("调用结果:{0}", res);
    
    // 关闭通道
    ((IClientChannel)svcontractInstance).Close();
    ```

  - 客户端重新声明了服务协定接口

    ```
    [ServiceContract(Namespace="demo-sv", Name="demo")]
    interface ITestService
    {
    	[OperationContract(Name="add", Action="add-opt")]
    	int Add(int x, int y);
    }
    ```

    由于服务器上服务协定接口的名字为IDemo，而客户端中重新定义的服务协定接口的名字为ITestService，名字不同是无法通信的。但是，如果服务器和客户端两边**设置相同的 Namespace和Name属性**，则可以进行通信。

  - 关闭通道代码

    ```
    // 关闭通道
    ((IClientChannel)svcontractInstance).Close();
    ```

    服务协定接口，被强制转换为 IClientChannel 接口，然后调用Close方法来关闭通道。

    还可以在客户端重新定义服务协定时，直接扩展IClientChannel接口：

    ```
    interface ITestService: IClientChannel
    {
    	int Add(int x, int y);
    }
    ```

    完整示例代码 \第1章\Example_3。



## 二. 消息与通道

通过对SOAP消息和通道层的了解，理解WCF的通信过程。

### 2.1. 消息基础

- 客户端：

  - 客户端向服务器发送请求，首先访问服务协定接口的某个方法（操作协定），将参数传递给方法
  - 运行时把这些输入参数序列化（XML序列化），并创建一条SOAP（SimpleObjectAccessProtocol）消息，再把输入参数序列化后的内容，写入SOAP的正文（Body）中
  - 通道层会使用特定的通信协议（如HTTP）把消息发送给服务器

- 服务器：

  - 通过通道层接收到客户端发来的SOAP消息，读出正文Body内容
  - 将正文内容反序列化，就可以得到客户端所传送的输入参数列表
  - 把这些参数传递给实现服务协定接口的服务类，即**调用服务方法**
  - 向客户端返回一条SOAP消息（单项通信除外）
    - 首先将服务方法的返回值序列化，然后创建一条SOAP消息，把序列化的返回值写入SOAP正文，经过通道层发送回客户端

- SOAP消息：一个XML文档，以Envolope为跟元素，包括

  - 正文（Body，或者叫主体）：Body元素下面可以用任何XML内容来充当消息正文
  - 消息头（Header）：消息头会放置一些附加信息。可以是任意XML内容

  ```xml
  <s:Envelope xmlns:s="http://www.w3.org/2003/05/soap-envelope">
  	<s:Header>
  		<Action s:mustUnderstand= "1" xmlns="http://schemas.microsoft.com/ws/2005/05/addressing/none">
  		my-act
  		</Action>
  	</s:Header>
  	<s:Body>
  		<Student xmlns="http://www.w3.org/2001/XMLSchema-instance" xmlns="http:/schemas.datacontract.org/2004/07">
  			<Age>22</Age>
  			<Name>小明</Name>
  		</Student>
  	</s:Body>
  </s:Envelope>
  ```

#### 1.1 创建消息实例

创建一条SOAP消息，需要用到Message类（System.ServiceModel.Channels）。Message类是抽象类，所以要调用CreateMessage方法创建Message实例。最常用的重载方法如下：

```c#
public static Message CreateMessage(MessageVersion version, string action, object body);
```

version：SOAP版本，可以从MessageVersion类的属性中获取

**action**：一个非null的字符串，为必需的，表示SOAP消息中的**Action标头**，这个标头是一个标准元素，通过这个值，服务调度程序才能找到客户端要调用的服务方法。

- 定义服务协定时，通过OperationContractAttribute的Action属性来指定一个有效的字符串值，用来唯一标识一个服务操作方法。例如：

  ```
  [ServiceContract(Namespace="sv-test")]
  interface IService
  {
  	[OperationContract(Action="compute")]
  	double Compute(double i);
  }
  ```

  Compute方法的Action值为compute，它与服务协定的命名空间合起来就是sv-test/compute。当客户端调用Compute操作时，它发送的SOAP消息的Header中应该包含一个Action元素，并与操作协定上的相同：

  ```xml
  <s:Envelope xmlns:s="http://www.w3.org/2003/05/soap-envelope">
  	<s:Header>
  		<Action s:mustUnderstand= "1" xmlns="http://schemas.microsoft.com/ws/2005/05/addressing/none">
  		sv-test/compute
  		</Action>
  	</s:Header>
  	<s:Body>
  		...
  	</s:Body>
  </s:Envelope>
  ```

  **WCF服务调度程序是通过`Action标头`来确认客户端到底要调用哪个服务操作的**。

示例：下面将创建一条SOAP消息，并以字符串作为消息正文

```
Message msgl = Message.CreateMessage(MessageVersion.Soap11, "test-action","这是一条SOAP消息。");
```

示例：下面以一个自定义类型作为消息的正文：

```
public class Employee
{
	public string Name{get;set;}
	public int Age{get;set;}
	public string ID{get;set;}
}

// 实例化
Employee emp = new Employee
{
	Name = "Mr. Liu",
	Age = 32,
	ID = "T-120005"
}

// 创建一个新的SOAP消息，并把Employee实例作为新消息的正文：
Message msg2 = Message.CreateMessage(MessageVersion.Soap12,"demo-opt",emp);
```

#### 1.2 使用消息头

调用CreateMessage方法创建新消息时，并不会创建自定义的消息头。要向消息实例添加消息头，应该访问Message类的Headers属性，它会返回一个对**MessageHeader类**的引用，然后调用它的Add方法来添加消息头。对于标准的消息头（如Action、To、MessageID等）可以直接通过MessageHeader类的公共属性来读写。

MessageHeader是抽象类，通过调用CreateHeader方法来得到一个MessageHeader实例。

示例：向SOAP消息添加消息头，然后从消息实例中分别读出消息头和消息正文。

#### 1.3 自定义消息正文

要实现自由编写XML内容，需要用到BodyWriter类，它是一个抽象类，开发人员可以实现它，以加入自定义的处理逻辑，具体，只要实现OnWriteBodyCotents抽象方法即可。这个方法带有一个XmlDictionaryWriter类型的参数，可以使用该参数来跟进需要去编写XML内容。

BodyWriter类有一个派生类——StreamBodyWriter，同样有一个抽象方法OnWriteBodyCotents，它接收一个Stream类型的参数，该方法可以直接把自定义的内容写入流中。

示例：

- 首先，从BodyWriter派生要给子类

  ```
  class CustomBodyWriter:BodyWriter
  {
  	IDictionary<string,string> m_data;
  	public CustomBodyWriter(IDictionary<sting,string> data):base(true)
  	{
  		m_data=data;
  	}
  	protected override void OnWriteBodyContents(XmlDictionaryWriter writer)
  	{
  		// 写入包装数据的元素
  		writer.WriterStartElement("student_info");
  		// 把字典中的key和value都写入XML元素
  		foreach(string k in m_data.Keys)
  		{
  			write.WriteElementString(k,m_data[k]);
  		}
  		//写入包装元素的结束标记
  		write.WriteEndElement();
  		//将内容写入基础流
  		write.Flush();
  	}
  }
  ```

  使用本类时，需要提供一个字典实例作为数据来源，在OnWriteBodyContents方法中，代码会将字典中每个项的Key和Value都写入XML元素中。

- 准备一些测试数据：

  ```
  IDictionary<string,string> dic = new Dictionary<string,string>
  {
  	["name"]="小赵",
  	["age"]="28",
  	["city"]="广州"
  }
  ```

  实例化CustomBodyWriter:

  ```
  CustomBodyWriter bw = new CustomBodyWriter(dic);
  ```

  创建消息实例，并将CustomBodyWriter实例传递给CreateMessage方法：

  ```
  Message msg = Message.CreateMessage(MessageVersion.Soap11,"test-data",bw);
  ```

- 结果

  ```
  <s:Envelope xmlns:s="http://www.w3.org/2003/05/soap-envelope">
  	<s:Header>
  		<Action s:mustUnderstand= "1" xmlns="http://schemas.microsoft.com/ws/2005/05/addressing/none">
  		test-data
  		</Action>
  	</s:Header>
  	<s:Body>
  		<student_info>
  			<name>小赵</name>
  			<age>28</age>
  			<city>广州</city>
  		</student_info>
  	</s:Body>
  </s:Envelope>
  ```

  如果isBuffered=false，则不会包含自定义的正文内容：

  ```
  <s:Envelope xmlns:s="http://www.w3.org/2003/05/soap-envelope">
  	<s:Header>
  		<Action s:mustUnderstand= "1" xmlns="http://schemas.microsoft.com/ws/2005/05/addressing/none">
  		test-data
  		</Action>
  	</s:Header>
  	<s:Body>... stream...</s:Body>
  </s:Envelope>
  ```

### 2.2. 使用内置通道

WCF也支持使用通道（Channel）来直接通信。

.NET类库公开了一系列接口，这些接口形成了WCF的"通道形状"。

按照通道的通信方式分：单向通道、请求/应答通道、双向通信。

按照通道的上下文状态分：无会话通道、带会话通道。

直接使用**通道层**来**通信**，通常只是收发消息，会话功能不明显，但是，当消息被调度到协定层，会话通信的作用就很明显了。

#### 2.2.1 常见的通道形状接口

![1570522310817](/1570522310817.png)

#### 2.2.2 通道侦听器

类似Socket实现机制

- 需要一个**IChannelListener来侦听客户端的连接**，与通道形状接口相似，侦听器接口也有内部实现类，可以直接使用
- **绑定**负责提供通信协议相关的信息。此处必须用到绑定。绑定公共基类Binding公开了**BuildChannelListener**方法，实例化绑定对象后，可以直接调用这个方法来**创建通道侦听器**。
- 之后，需要**调用AcceptChannel**方法来**接受客户端连接**(类似Socket的Accept)。
- 连接后会**返回一个通道对象的实例**，类型由BuildChannelListener方法的类型参数TChannel决定，它表示要用来通信的**通道类型**，比如IDuplexChannel。

#### 2.2.3 通道工厂

这里的通道工厂与上一章不同，本章讲述的内容基于通道层，指位于System.ServiceModel.Channels下的IChannelFactory接口，可以实现，也可以直接使用内部的实现类，来创建通道实例。

#### 2.2.4 示例：直接使用通道来通信

本例使用 `请求/应答模式`的通道接口（IRequestChannel与IReplyChannel），并配合BasicHttpBinding进行通信演示。

**服务器代码**：

```c#
// 服务器侦听连接的地址
Uri listUri = new Uri("http://localhost:900");

// 实例化绑定对象
BasicHttpBinding binding = new BasicHttpBinding();

// 创建通道监听器
IChannelListener<IReplyChannel> listener = null;
if(binding.CanBuildChannelListener<IReplyChannel>())
{
	listener = binding.BuildChannelListener<IReplayChannel>(listUri);
	
	listener.Open();
	
	while(true)
	{
		IReplyChannel reply = listener.AcceptChannel();
		// 打开通道
		reply.Open();
		// 接收客户端的消息
		RequestContext context = reply.ReceiveRequest();
		// 获取客户端发送的内容
		string msg = context.RequestMessage.GetBody<string>();
		Console.WriteLine($"来自客户端的消息：{msg}");
		// 回复消息
		Message replymsg = Message.CreateMessage(binding.MessageVersion, "test-reply", "已收到消息。");
		context.Reply(replymsg);
		// 关闭通道
		reply.Close();
		//
		Console.WriteLine("请按任意键继续，按ESC键退出。");
		ConsoleKeyInfo key = Console.ReadKey();
		if(key.Key == ConsoleKey.Escape)
			break;
	}
	
	// 关闭侦听器
	listener.Close();
}

```

用`CanBuildChannelListener()`检测是否支持创建通道监听器

如果可以，就调用`AcceptChannel()`方法来接受客户端的连接，然后就可以收发消息了

**客户端代码**：

```c#
Uri servUri = new Uri("http//localhost:900");
// 实例化绑定对象
BasicHttpBinding binding = new BasicHttpBinding();
// 创建通道工厂
IChannelFactory<IRequestChannel> reqfac = null;
if(binding.CanBuildChannelFactory<IRequestChannel>())
{
	reqfac = binding.BuildChannelFactory<IRequestChannel>();
}
// 创建通道
if(reqfac != null)
{
	// 打开通道工厂
	reqfac.Open();
	EndpointAddress epaddr = new EndpointAddress(servUri);
	IRequestChannel channel = reqfac.CreateChannel(epaddr);
	// 打开通道
	channel.Open();
	// 向服务器发送消息
	Message msg = Message.CreateMessage(binding.MessageVersion,"test-request","你好，我是客户端;
	Message replymsg = channel.Request(msg);
	// 输出服务器回应的消息
	string rmsg = replymsg.GetBody<string>();
	Console.WriteLine($"从服务器返回的消息：{rmsg}");
	// 关闭通道
	channel.Close();
}

Console.Read();
// 关闭通道工厂
reqfac.Close();
```

通道监听器、通道工厂、通道对象，由于这些接口都继承自一个公共接口——ICommunicationObject，因此，使用前必须调用Open()，使用后要调用Close()。

### 2.3 注意消息的状态

WCF通信过程中，Message类的State属性，用于描述当前小时实例的状态，各状态都封装在`MessageState枚举`中。

为了保证在一轮通信中消息只使用一次，WCF通道只允许State为Created的消息参与通信。如果消息被写入到流中，或被读取或复制过，都不能用于通信。

如果消息被复制或写入到流中，又希望发送此消息，要先将消息复制到MessageBuffer中缓存，在调用此类的CreateMessage()方法重新创建一条新消息，就可以了:

```c#
// 创建通道
EndpointAddress epaddr = new EndpointAddress(base_uri);
IRequestChannel channel = fac.CreateChannel(epaddr);
channel.Open();
// 发送消息
Message msg = Message.CreateMessage(myBinding.MessageVersion,"http://test","消息");
MessageBuffer bf = msg.CreateBufferedCopy(2048);	// 被复制过的消息，发送会报错
Message newMsg = bf.CreateMessage();				// 重新创建消息
channel.Request(newMsg);							// 就可以被发送
// 关闭通道
channel.Close();
```

发送被复制过的消息，发生异常

<img src="/1570697324530.png" alt="1570697324530" style="zoom: 80%;" />

## 三、协定

WCF开发中用到的协定有服务协定、操作协定、数据协定和消息协定。

### 3.1. 服务协定与操作协定

服务协定类似一个接口，操作协定就是接口中的一个方法。因此，服务协定包含若干个操作协定。

#### 3.1.1 服务协定的命名空间与名称

在声明服务协定时，可以指定**命名空间**Namespace和**协定名称**Name，这两个属性值都会应用于服务所公开的元数据（WSDL文档）中。

Namespace属性将作为WSDL文档中portType元素的**命名空间**，如不指定，默认值为**http://tempuri.org**

Name属性应用于WSDL文档中portType元素的**名称**。默认值为 **接口名**(即 **服务协定名称**)。

如下协定：

```
[ServiceContract]
interface IDemo
{
	[OperationContract]
	int Add(int x,int y);
}
```

用下面的方法获取协定的命名空间和名称：

```
ContractDescription ctdesc = ContractDescription.GetContract(typeof(IDemo));
string s = $"服务协定名称：{ctdesc.Name}\n";
s += $"服务协定的命名空间：{ctdes.Namespace}";
Console.Writeline(s);

屏幕输出：
服务协定名称：IDemo
服务协定的命名空间：http://tempuri.org/
```

在声明一个服务协定：显示设置命名空间和协定名称

```
[ServiceContract(Namespace="http://test.com",Name="MyTask")]
interface IDemo
{
	[OperationContract]
	int StartTask();
}
```

#### 3.1.2 操作协定的Action值

- Name属性

  OperationContractAttribute类也有一个Name属性，用于声明**操作协定名称**，默认是**方法名**。

- Action属性：

  客户端在调用服务时，会把要调用的操作方法的**Action值加入到SOAP消息的Action消息头中(作为Header元素的子级)**，服务器会根据消息头Action的内容来寻找客户端要调用的服务操作。Action属性是用来设置操作协定的标识的。

  服务器端**调度程序**通过传入消息的Action头与服务中各个操作方法的Action属性进行匹配，找到就调用，未找到，则发生错误。

  还可将Action属性设置为`*`，表示客户端传入的任意SOAP消息都与该操作匹配，都能调用此操作。

- ReplyAction属性：

  它表示SOAP消息中答复消息的Action头的内容。

可以使用以下代码来获取服务协定中所包含的操作协定的信息。

```
ContractDescription cd = ContractDescription.GetContract(typeof(ITest));
foreach(OperationDescription op in cd.Operations)
{
	Console.WriteLine($"操作名称：{op.Name}");
	Console.WriteLine("-----------------");
	foreach(MessageDescription msg in op.Messages)
	{
		Console.WriteLine($"Action = {msg.Action}");
	}
}
```

OperationDescription类用于描述每个操作协定相关的信息。

注意：要获取操作协定的Action值，应先访问其Messages集合。由于本例是双向通信，因此包含两条消息:

```
操作名称：Run
——————————————————
Action = http://tempuri.org/ITest/Run
Action = http://tempuri.org/ITest/RunResponse
```

**Action值是由服务协定命名空间与操作名称组成：服务协定命名空间/服务协定名/操作协定名(Response)**。

如果：设置Action和ReplyAction，如

```
[ServiceContract]
interface IDemo
{
	[OperationContract(Name="add", Action="add-opt", ReplyAction="reply-opt")]
	int StartTask();
}
```

则上面范例程序会显示如下

```
操作名称：add
——————————————————
Action = add-opt
Action = reply-opt
```

#### 3.1.3 直接把服务类声明为服务协定 

WCF开发过程，一般是先将一个接口类型声明为服务协定，再单独用一个类来实现服务协定，如下：

```
[ServiceContract]
interface ITaskManager
{
	[OperationContract]
	void StartRun();
}

class TaskManagerSvr:ITaskManager
{
	public void StartRun()
	{
		//...
	}
}
```

直接将服务类声明为服务协定：

```
[ServiceContract(Namespace="http://someone.net",Name="demo")]
class MyService
{
	[OperationContract(Name="add", Action="add",ReplayAction="addResponse")]
	public int Add(inta, int b)
	{
		return a+b;
	}
}
```

这样，就把定义接口和实现接口两个步骤合起来完成了，并直接让ServiceContractAttribute应用到MyService上。

客户端，由于无须具体的执行代码，可以使用接口类型重新声明服务协定。

```
[ServiceContract(Namespace="http://someone.net",Name="demo")]
public interface IService
{
	[OperationContract(Name="add",Action="add",ReplyAction="addResponse")]
	int Add(int a,int b);
}
```

有了OperationContractAttribute的约束，操作方法的名字可以与服务类不同，参数的名字也可以不一致，但是，方法的返回值类型必须一致，参数的个数和类型必须统一。

#### 3.1.4 约束参数的名字

通过服务协定和操作协定可以规范服务器和客户端之间的类型一致：

- 服务协定：命名空间和操作名字

- 操作协定：操作名字、Action、ReplyAction，操作方法的参数个数、类型和返回值类型匹配。

方法参数名字不匹配，也会造成WCF服务调用失败，例如：

- 服务端声明

  ```
  [ServiceContract(Namespace="my-sample",Name="num_sv")]
  public interface IService
  {
  	[OperationContract(Name="NumWork",Action="work-req",ReplyAction="work-resp")]
  	int Work(int x);
  }
  ```

  服务端实现接口

  ```
  class MyService:IService
  {
  	public int Work(int x)
  	{
  		return x*x;
  	}
  }
  ```

- 客户端

  ```
  [ServiceContract(Namespace="my-sample",Name="num_sv")]
  public interface IComputer
  {
  	[OperationContract(Name="NumWork",Action="work-req",ReplyAction="work-resp")]
  	int Run(int k);
  }
  ```

- 服务协定和操作协定匹配，尝试调用一下该服务

  ```c#
  IComputer channel = ChannelFactory<IComputer>.CreateChannel(binding, new EndpointAddress(uri));
  int result = channel.Run(10);
  Console.WriteLine("计算结果：{0}", result);
  ((IClientChannel)channel).Close();
  ```

  预期结果为100，但返回结果为0。

  错误原因是操作方法参数的名字不匹配。

  比较：

  ```
  // 服务端
  [ServiceContract(Namespace="my-sample",Name="num_sv")]
  public interface IService
  {
  	[OperationContract(Name="NumWork",Action="work-req",ReplyAction="work-resp")]
  	int Work(int x);
  }
  
  // 客户端
  [ServiceContract(Namespace="my-sample",Name="num_sv")]
  public interface IComputer
  {
  	[OperationContract(Name="NumWork",Action="work-req",ReplyAction="work-resp")]
  	int Run(int k);
  }
  ```

  由于序列化参数的名字为k，而x得不到值，所以，x为0，返回的结果为0x0=0。

  解决方案：

  ```
  // 服务端
  [ServiceContract(Namespace="my-sample",Name="num_sv")]
  public interface IService
  {
  	[OperationContract(Name="NumWork",Action="work-req",ReplyAction="work-resp")]
  	int Work([MessageParameter(Name="num")]int x);
  }
  
  // 客户端
  [ServiceContract(Namespace="my-sample",Name="num_sv")]
  public interface IComputer
  {
  	[OperationContract(Name="NumWork",Action="work-req",ReplyAction="work-resp")]
  	int Run([MessageParameter(Name="num")]int k);
  }
  ```

### 3.2. 数据协定

WCF应用程序通过序列化(通常是XML方式)技术来传递数据，对于.NET中的基本类型(如string、int、double、byte等，也包括DateTime类型)都可以自动完成序列化，但，对于自定义的**复杂类型，应该声明为数据协定**。

数据协定使得服务器与客户端共享相同的协定，确保双方在通信过程中都能识别自定义类型。

自定义复杂类型通常是开发者声明的类或者结构。

#### 3.2.1 数据协定与序列化

要让自定义类型成为**数据协定**，应该将`DataContractAttribute`应用于类型，把`DataMemberAttribute`应用到类型成员上（字段或属性）

**示例**：

- 新建一个控制台程序项目

- 为项目添加System.Runtime.Serialization程序集的引用

- 定义一个类，命名为Student：

  ```c#
  class Student
  {
  	internal string Name{get;set;}
  	internal int Age{get;set;}
  	internal DateTime Birthday{get;set;}
  }
  ```

- 在Student类上应用DataContractAttribute

  ```
  [DataContract]
  class Student
  {
  	...
  }
  ```

  默认情况下，数据协定的名字和类型名字相同，也可以明确指定数据协定的命名空间和名称：

  ```
  [DataContract(Namespace="http://demo",Name="student")]
  class Student
  {
  	...
  }
  ```

- 为类型的各个成员应用DataMemberAttribute

  ```
  [DataContract(Namespace="http://demo",Name="student")]
  class Student
  {
  	[DataMember(Name="name")]
  	internal string Name{get;set;}
  	[DataMember(Name="age")]
  	internal int Age{get;set;}
  	[DataMember(Name="birthday")]
  	internal DateTime Birthday{get;set;}
  }
  ```

  如果不设置Name属性，则序列化时就是使用类型成员的名字。

- 要将数据协定类型实例序列化，应使用DataContractSerializer类

  ```
  // 将序列化的内容写入内存流
  MemoryStream stream = new MemoryStream();
  DataContractSerializer dsz = new DataContractSerializer(typeof(Student));
  Student stuobj = new Student
  {
  	Name="小张",
  	Age=23,
  	Birthday=new DateTime(1988,7,12)
  }
  // 序列化
  dsz.WriteObject(stream, stuobj);
  ```

- 下列代码将刚才写进内存流中的XML文档显示出来

  ```
  stream.Position = 0L;
  XDocument xmldoc = XDocument.Load(stream);
  Console.WriteLine($"\n序列化后的XML文档：\n(xmldoc)");
  ```

  Student实例序列化后生成的XML文档如下：

  ```
  <student xmlns="http://demo" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  	<age>23</age>
  	<birthday>1988-07-12T00:00:00</birthday>
  	<name>小张</name>
  </student>
  ```

- 下列代码将进行反序列化，并填充新的Student实例

  ```
  // 反序列化
  stream.Position = 0L;
  Student scobj = (Student)dsz.ReadObject(stream);
  ```

#### 3.2.2 数据成员序列化的顺序

示例，先声明一个数据协定类型

```
[DataContract]
public class Product
{
	[DataMember]
	public Guid ID{get;set;}
	[DataMember]
	public string DescName{get;set;}
	[DataMember]
	public float Size{get;set;}
}
```

=>序列化后

```
<Product>
	<DescName>产品A</DescName>
	<ID>0a0c2f5c-150b-4511-b439-96736bf190b0</ID>
	<Size>5.33</Size>
</Product>
```

各元素是按首字母来排序的。

如果想设置规定排序的化，可以设置**Order属性**

```c#
[DataContract]
public class Product
{
	[DataMember(Order=3)]
	public Guid ID{get;set;}
	[DataMember(Order=8)]
	public string DescName{get;set;}
	[DataMember(Order=6)]
	public float Size{get;set;}
}
```

设置Order值后序列化生成的XML文档如下：

```
<Product>	
	<ID>0a0c2f5c-150b-4511-b439-96736bf190b0</ID>
	<Size>5.33</Size>
	<DescName>产品A</DescName>
</Product>
```

#### 3.2.3 必需成员`IsRequired`与可忽略成员`IgnoreDataMember` 

**必需成员**要求反序列化过程中必须找到指定的成员。一般情况下,如果XML数据中缺少某个成员的值,反序列化程序会为对象实例中的对应成员保留默认值(如果成员类型是int,默认为0;如果是string,则默认为null)。若是某个成员被标记为必需成员,反序列化的时候,如果找不到指定的成员的值,就会引发异常。

```
[DataContract(Name="product",Namespace="sample-data")]
class Product
{
	[DataMember(Name="id")]
	public int PID{get;set;}
	[DataMember(Name="name")]
	public string Name{get;set;}
	[DataMember(Name="color", IsRequired=true)]
	public int Color{get;set;}
}
```

下面是客户端定义的数据协定：

```
[DataContract(Name="product",Namespace="sample-data")]
class ProductInfo
{
	[DataMember(Name="id")]
	public int ID{get;set;}
	[DataMember(Name="name")]
	public string ProdName{get;set;}
}
```

把客户端上的product协定实例序列化：

```
Client.ProductInfo p1=new Client.ProductInfo
{
	ID=1000069,
	ProdName="测试产品"
};
DataContractSerializer sz = new DataContractSerializer(typeof(Client.ProductInfo));
sz.WriteObject(stream, p1);

```

生成XML如下：

```
<product>	
	<id>1000069</id>
	<name>产品A</name>
</product>
```

反序列化，反序列化的类型将使用服务器上定义的product协定：

```
DataContractSerializer dsz = new DataContractSerializer(typeof(Server.Product));
Server.Product p2 = (Server.Product)dsz.ReadObject(stream);
```

此时发生异常：

![1570550742602](/1570550742602.png) 

下面情形——在序列化过程中，**可**以**忽略**某些**成员** 。在数据协定的成员上应用`IgnoreDataMember` ，它们不参与序列化

```
[DataContract(Namespace="http://demo",Name="car")]
public class Car
{
	[DataMember(Name="speed")]
	public double Speed;
	[DataMember(Name="color")]
	public int Color;
	[IgnoreDataMember]
	public string Remarks;	
}
```

进行序列化：

```
Car car = new Car();
car.Speed=96.3d;
car.Color=3224;
car.Remark = "示例文本";

DataContractSerializer sz= new DataContractSerializer(typeof(Car));
sz.WriteObject(stream, car);
```

序列化后生成的XML文档：

```
<car>
	<color>3224</color>
	<speed>96.3</speed>
</car>
```

#### 3.2.4 将枚举类型声明为数据协定

如果数据协定的类型是"枚举"，其成员就不能应用DataMemberAttribute，而要改用EnumMemberAttribute。

下列代码声明一个Colors枚举；

```c#
[DataContract(Namespace="http://color",Name="color")]
enum Colors
{
	[EnumMember(Value="red")]
	R,
	[EnumMember(Value="green")]
	G,
	[EnumMember(Value="blue")]
	B
}
```

把Colors枚举的实例序列化：

```c#
Colors c= Colors.B;
DataContractSerializer sz= new DataContractSerializer(typeof(Colors));
sz.WriteObject(stream, c);
```

序列化后生成的XML如下：

```xml
<color xmlns="http://color">blue</color>
```

进行反序列化：

```c#
Colors c2=(Colors)sz.ReadObject(stream);
```

#### 3.2.5 已知类型

两个数据协定：

```
[DataContract]
public class Student
{
	[DataMember]
	public string Name{get;set;}
	[DataMember]
	public int Age{get;set;}
	[DataMember]
	public object Address{get;set;}
}

[DataContract]
public class AddressInfo
{
	[DataMember]
	public string Province{get;set;}
	[DataMember]
	public string City{get;set;}
	[DataMember]
	public string ZipCode{get;set;}
}
```

注意：Student类的Address属性是object类型，实例化后，Address属性会赋值一个AddressInfo实例对象。

```

```



### 3.3. 使用XML序列化

除了使用`DataContractSerializer类`将数据对象序列化为XML文档外，WCF还支持传统的XML序列化模型，可以使用XmlElementAttribute、XmlAttributeAttribute等特性来休息类型的成员。

```c#
public class AudioTrack
{
	[XmlAttribute(AttributeName="title")]
	public string Title {get;set;}
	[XmlAttribute(AttributeName="artist")]
	public string Artist {get;set;}
	[XmlAttribute(AttributeName="no")]
	public int TrackNo {get;set;}
	[XmlAttribute(AttributeName="album")]
	public string Album {get;set;}
}
```

此类没有定义为数据协定，而是**应用XmlAttributeAttribute**，该特性使得**类型成员被序列化成****XML的特性值**，而不是XML元素。

然后声明服务协定，AudioTrack类将作为服务操作的一个输入参数传递。

```
[ServiceContract, XmlSerializerFormat]
interface IDemo
{
	[OperationContract]
	void PostMusic(AudioTrack track);
}
```

由于WCF默认使用DataContractSerializer类进行序列化，因此需要再服务协定或操作协定上应用XmlSerializerFormatAttribute。

**传统XML序列化：数据协定只能作用于公共类型**。

XmlSerializerFormatAttribute类的属性——**Use**，应设置为Literal，而不是Encoded，来规划生成的XML内容。

### 3.4. 消息协定

- **数据协定**：面向自定义类型而声明的对象及其成员的约定。

- **消息协定**：是将类型成员映射到SOAP消息的各个部分中，约定哪些成员会被放到消息头，哪些成员会被放到消息正文中。

示例：声明一个Data类，包含四个属性，消息协定

```c#
[MessageContract]
public class Data
{
	[MessageHeader]
	public int ID{ get; set;}
	[MessageHeader]
	public string Token{ get; set;}
	
	[MessageBodyMember]
	public string Description{ get; set;}
	[MessageBodyMember]
	public long ByteLen{ get; set;}
}
```

消息协定允许开发者以面向对象的方式来安排SOAP消息的内容，从而使消息的结构更加灵活。

#### 3.4.1 消息协定的基本用法

示例：

实现步骤：

- 新建一个控制台项目

- 声明消息协定，名称为ProductItem

  ```
  [MessageContract]
  public class ProductItem
  {
  	[MessageBodyMember]
  	public string Name{ get; set;}	
  	[MessageBodyMember]
  	public string Color{ get; set;}
  	[MessageBodyMember]
  	public int PID{ get; set;}
  	[MessageHeader]
  	public DateTime PostTime{ get; set;}
  ```

- 声明服务协定，代码如下

  ```
  [ServiceContract]
  public interface IService
  {
  	[OperationContract]
  	void NewProduct(ProductItem item);
  }
  ```

- 定义服务类，实现服务协定

  ```
  class MyService:IService
  {
  	public void NewProduct(ProductItem item)
  	{
  		string str=$"{nameof(ProductItem.PostTime)}={item.PostTime:D}";
  		str += $"\n{nameof(ProductItem.PID)}={item.PoID}";
  		str += $"\n{nameof(ProductItem.Name)}={item.Name}";
  		str += $"\n{nameof(ProductItem.Color)}={item.Color}";
  		Console.WriteLine("收到来自客户端的数据：\n{0}",str);
  	}
  }
  ```

- 实例化服务主机，寄宿并运行服务

  ```
  ServiceHost host=new ServiceHost(typeof(MyService),new Uri("http://localhost:800"));
  // 打开服务
  host.Open();
  Console.WriteLine("服务已启动。");
  ...
  Console.ReadKey();
  host.Close();
  ```

- 调用服务

  ```
  EndpointAddress epaddress=new EndpointAddress("http://localhost:800");
  BasicHttpBinding binding=new BasicHttpBinding();
  IServic svobj= ChannelFactory<IService>.CreateChannel(binding,epaddress);
  productItem p=new ProductItem
  {
  	PID=100001,
  	Name="test product",
  	Color = "black",
  	PostTime = DateTime.Now
  };
  svobj.NewProduct(p);
  ```

  客户端提交的SOAP如下：

  ```
  <s:Envelop xmlns:s="...">
  	<s:Header>
  		<h:PostTime xmlns:h="http://tempuri.org/">2019-10-11T14:54:45.1842021+08:00</h:PostTime>
  	</s:Header>
  	<s:Body>
  		<ProductItem xmlns="http://tempuri.org/">
  			<Color>black</Color>
  			<Name>test product</Name>
  			<PID>100001</PID>
  		</ProductItem>
  	</s:Body>
  </s:Envelope>
  ```

  消息协定独占一条SOAP，而每一轮通信过程，客户端只能向服务器发送一条SOAP。

  消息协定的方法**不允许出现多个输入参数**，返回值，**是void**或者是**消息协定**。

#### 3.4.2 包装元素

当MessageContractAttribute的IsWrapped=true，则消息协定也可以设置独立的命名空间和包装元素名称。

数据协定是一个整体，只需要为类设置包装元素名称和命名空间即可。

示例：

## 四、终结点

一个终结点有三要素：

- 绑定(Binding)：用来描述通信协议相关参数
- 服务协定(Contract)
- 地址(Address)
- 可选要素：行为(Behavor)：添加附加行为开关（如：是否向客户端发送服务器上的错误）

### 4.1 绑定

绑定描述的是与通信相关的各个方面的信息，比如

- 使用的通信协议、
- 连接超时限制
- 传输数据的最大缓存限制、
- 传输过程使用的安全模式
- 。。。

每个终结点都必须指定一个绑定实例，否则WCF将不知道使用何种协议来通信。

**绑定类**的基类是`Binding`，一个绑定可以由数量不等的绑定元素组成，**绑定元素**的基类是`BindingElement`。

从Binding派生的类：

- 实现CreateBinding-Elements方法，以放回组成该绑定各个方面参数的绑定元素；
- 实现Scheme属性，它返回绑定所使用通信协议的Uri方案名称，如http或net.tcp

#### 4.1.1 系统提供的绑定

系统提供的绑定：

<img src="/1570861610316.png" alt="1570861610316" style="zoom:80%;" /> 

- 这些绑定可以直接用于终结点。
- 客户端和服务器上声明的绑定必须是匹配的
- 绑定的配置可以再程序代码中完成，也可以使用配置文件

#### 4.1.2 示例：输出系统绑定的具体信息

本示例主要以BasicHttpBinding、WSHttpBinding和NetTcpBinding三个绑定为例，演示如何获取绑定的详细信息。

```c#
Binding[] bindings =
{
	new BasicHttpBinding(),
	new NetTctBinding(),
	new WSHttpBinding()
};

foreach(Binding b in bindings)
{
	Console.WriteLine($"绑定类型：{b.GetType().Name}");
	Console.WriteLine($"Uri架构：{b.Scheme}://localhost:8080/service");
	
	Console.WriteLine("绑定对象中包含的绑定元素：")；
	var bdElements = b.CreateBindingElements();
	foreach(var ele in bdElements)
	{
		Console.WriteLine(ele.GetType().Name);
	}
	Console.WriteLine("---------------------------------------");
}
```

#### 4.1.3 自定义绑定

实现自定义绑定的方法

- 第一种直接从Binding类或其子类派生，编写自定义的绑定类
- 第二种是**使用CustomBinding**。

推荐第二种方式。

CustomBinding类允许使用若干数量的BindingElement对象进行初始化。

BindingElement是抽象类，是所有绑定元素的基类，从其派生的各种类型实例，都可以用于CustomBinding类初始化。

通过CustomBinding类，可以按照实际需要来"组装"绑定。

向CustomBinding对象添加绑定元素时，必须按一定顺序进行：

绑定元素的添加顺序如下：

![1570869923644](/1570869923644.png)

示例：演示CustomBinding类的用法。