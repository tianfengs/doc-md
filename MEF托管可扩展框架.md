---
typora-root-url: pics
typora-copy-images-to: pics
---

> 摘自CSDN 我学MEF系列： https://blog.csdn.net/u010476739/article/details/77692642

### 一、MEF概念

1. 概念：MEF，全称Managed Extensibility Framework，托管可扩展框架。

   它是用于创建可扩展的轻型应用程序库。应用程序开发人员可以利用该库发现并使用扩展，而无需进行配置。

   扩展开发人员还可以利用该库轻松的封装代码，避免生成脆弱的硬依赖。

   通过MEF，不仅可以在应用程序内重用扩展，还可以在应用程序之间重用扩展。

2. 特点：

   >EJB、CORBA、Eclipse 的 OSGI 实现以及 Java 端的 Spring 等等
   >
   >Microsoft 的平台上，.NET Framework 自身内部包含组件模型和 System.Addin。
   >
   >同时存在若干种开源解决方案，包括 SharpDevelop 的 SODA 体系结构和“控制反转”容器（如 Castle Windsor、Structure Map 以及模式和实践的 Unity）

   - 它解决了扩展性的问题
   - 它是轻量级的，使用时只需要引用一个dll库
   - 扩展性不是通过配置文件实现，而是使用**特性化编程模型**
   - 该框架是开源的，可在 codeplex（http://mef.codeplex.com/） 上下载源码
   - 它是微软开发的

   ```
   “MEF 是 .NET Framework 4 的组成部分，可用在任何使用 .NET Framework 的地方。 可以在客户端应用程序中使用 MEF（无论应用程序使用的是 Windows 窗体、WPF，还是任何其他技术），也可以在使用 ASP.NET 的服务器应用程序中使用 MEF”　　　　　　——MSDN
   ```

3. 工作原理

   MEF不同于显示注册可用组件的做法，在MEF中组件被视为**部件**。这些部件主要有“导入”(Import)和“导出”(Export)部件。此外，MEF提供了一个组合容器（ComposeContainer），容器会发现指定Catalog的导入导出部件，并按Contract（协定）、Metadata（元数据）等对导入导出部件进行组合。













