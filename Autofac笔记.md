---
typora-root-url: pics
typora-copy-images-to: pics
---

> 本文笔记摘录自：Autofac 中文文档! https://autofaccn.readthedocs.io/zh/latest/ 

# 注册组件

## 一、 注册概念

通过创建`ContainerBuilder`来注册`组件`，并告诉容器哪些`组件`暴露了哪些`服务`。

-  **组件** 可以通过 **反射** (注册指定的.NET类或开放结构的泛型)创建;  
-  通过提供现成的 **实例** (你已创建的一个对象实例)创建;  
-  通过 lambda **表达式** (一个执行实例化对象的匿名方法)来创建. 

 每个组件暴露一个或多个 **服务** ,他们使用 `ContainerBuilder` 上的 `As()` 方法连接起来. 

```c#
// Create the builder with which components/services are registered.
var builder = new ContainerBuilder();	// 创建一个容器对象

// Register types that expose interfaces...
builder.RegisterType<ConsoleLogger>().As<ILogger>();	// 注册一个类型，来暴露接口

// Register instances of objects you create...
var output = new StringWriter();
builder.RegisterInstance(output).As<TextWriter>();		// 注册一个对象实例

// Register expressions that execute to create objects...
builder.Register(c => new ConfigReader("mysection")).As<IConfigReader>();//注册一个表达式，创建对象

// Build the container to finalize registrations
// and prepare for object resolution.
var container = builder.Build();		// Build 容器来最终注册，准备进行对象解析

// Now you can resolve services using Autofac. For example,//开始解析服务
// this line will execute the lambda expression registered 
// to the IConfigReader service.	//这一行运行lambda表达式解析IConfigReader服务
using(var scope = container.BeginLifetimeScope())
{
  var reader = scope.Resolve<IConfigReader>();
}
```

### 1. 反射组件

#### 1.1 通过注册类型

通过反射生成的组件通常是由类型注册的：

```c#
var builder = new ContainerBuilder();
builder.RegisterType<ConsoleLogger>();
builder.RegisterType(typeof(ConfigReader));
```

 当使用基于反射的组件时, **Autofac 自动为你的类从容器中寻找匹配拥有最多参数的构造方法**. 

 **基于反射的组件有个重要的需要注意的地方:** 

- 任何通过 `RegisterType` 注册的组件必须是个具体的类型. 
-  组件可以暴露抽象类和接口作为 `服务` 

#### 1.2 指定构造函数

 可以使用 `UsingConstructor` 方法和构造方法中一系列代表参数类型的类型来 **手动指定一个构造函数** , 通过这种方式使用和覆盖注册组件自动选择的构造函数: 

```c#
builder.RegisterType<MyComponent>()
       .UsingConstructor(typeof(ILogger), typeof(IConfigReader));
```

### 2. 实例组件

 提前生成一个对象的实例并将它加入容器以供注册组件时使用. 可以通过使用 `RegisterInstance` 方法: 

```
var output = new StringWriter();
builder.RegisterInstance(output).As<TextWriter>();
```

可以注册静态单例实例：

```
builder.RegisterInstance(MySingleton.Instance).ExternallyOwned();
```

 通过某一实例暴露的**默认服务**是该**实例的实体类**.  

### 3. Lambda表达式

Autofac可以接收一个委托或者lambda表达式，用作组件创建者：

```
builder.Register(c => new A(c.Resolve<B>()));
```

参数`c`是组件上下文（`IComponentContext`对象），组件在其中被创建。

- **使用这个参数而不是一个闭包来访问容器非常重要** 这样可以保证 [对象精确的释放](https://autofaccn.readthedocs.io/zh/latest/lifetime/disposal.html) 并且可以很好的支持嵌套容器. 
- 使用该上下文参数可以满足其他依赖的成功引入 - 例如, `A` 需要一个构造方法参数 `B` ,而 `B` 可能还有其他的依赖关系. 
-  表达式创建的组件提供的默认服务是表达式的推断返回类型. 

 下面的一些示例使用反射组件很难满足需求但是用lambda表达式可以很好地解决问题. 

#### 3.1 复杂参数

 构造方法参数不会总是简单的常量.  

```
builder.Register(c => new UserSession(DateTime.Now.AddMinutes(25)));
```

 #### 3.2 参数注入

 Autofac提供了 [一流的方法可用来完成参数注入](https://autofaccn.readthedocs.io/zh/latest/register/prop-method-injection.html), 你可以使用表达式和属性初始化来填充参数: 

```
builder.Register(c => new A(){ MyB = c.ResolveOptional<B>() });
```

 `ResolveOptional` 方法会尝试解析, 但如果服务没有注册, 不会抛出错误. (如果服务成功注册但是无法成功解析, 你依然会得到一个错误.) 这是 [解析服务](https://autofaccn.readthedocs.io/zh/latest/resolve/index.html) 的一种方式. 

 **在大多数情况下不推荐使用参数注入.** 使用其他的例如 [空对象模式](http://en.wikipedia.org/wiki/Null_Object_pattern), 重载构造方法或参数默认值这些方式, 用构造方法注入可以创建出可选依赖的更清爽, "不可变" 组件. 

#### 3.3 通过参数值选择具体的实现

 创建分离组件的一大好处是具体的类型可以是多种多样的. 指定具体的类型通常可以在运行时完成, 而不仅仅是配置时期: 

```c#
builder.Register<CreditCard>(
  (c, p) =>
    {
      var accountId = p.Named<string>("accountId");
      if (accountId.StartsWith("9"))
      {
        return new GoldCard(accountId);
      }
      else
      {
        return new StandardCard(accountId);
      }
    });
```

示例中, `CreditCard` 通过两种类实现, `GoldCard` 和 `StandardCard` - 哪个类会被实例化取决于运行时期间提供的account ID.

示例中 [创建方法的参数](https://autofaccn.readthedocs.io/zh/latest/resolve/parameters.html) 通过第二个可选的参数 `p` 传入.

解析时可以这样:

```c#
var card = container.Resolve<CreditCard>(new NamedParameter("accountId", "12345"));
```

### 4. 开放泛型组件

 Autofac支持开放泛型. 使用 `RegisterGeneric()` 方法: 

```c#
builder.RegisterGeneric(typeof(NHibernateRepository<>))
       .As(typeof(IRepository<>))
       .InstancePerLifetimeScope();
```

解析：

```c#
// Autofac will return an NHibernateRepository<Task>
var tasks = container.Resolve<IRepository<Task>>();
```

### 5. 服务 vs. 组件

 注册 [组件](https://autofaccn.readthedocs.io/zh/latest/glossary.html) 时, 我们得告诉Autofac, 组件暴露了哪些 [服务](https://autofaccn.readthedocs.io/zh/latest/glossary.html) . 默认地, 类型注册时大部分情况下暴露它们自身: 

```c#
// This exposes the service "CallLogger"
builder.RegisterType<CallLogger>();
```

