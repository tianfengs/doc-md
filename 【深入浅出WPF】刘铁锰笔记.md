WPF是用xmal标记语言作为ui描述语言，通过模板的嵌套实现复杂的控件元素，控件就是事件和行为的载体

WPF使用MVVM模式

以数据作为驱动，驱动UI，而不是Windows以消息通过事件作为驱动，传递给程序，数据是核心，驱动UI。

UI表现的是内容，包括数据（变量）和算法（函数），因此模板分为控件模板和数据模板。

界面元素之间通过路由事件进行消息传递，

WPF系统与程序内容（业务逻辑）的边界是Binding，Binding把内部数据源源不断的从程序内部送出来，交由界面显示，又把界面搜集的数据传送回程序内部







### 第十一章 深入浅出化模板

WPF的Template分为两大类：

- ControlTemplate控件模板
- DataTemplate 数据模板



#### 数据模板DataTemplate

数据的外衣。常用的地方有3处：

- ContentControl 的 ContentTemplate 属性
- ItemsControl 的 ItremTemplate 属性
- GridViewColumn 的 CellTemplate 属性

举例区别DataTemplate和UserControl的区别，例子的需求是（Porsche保时捷）：

```
有一列汽车的数据，这列数据显示在一个ListBox里，要求ListBox的条目显示汽车的厂商图标和简要的参数，单击某个条目后在窗体的详细内容区域显示汽车的照片的详细参数。
```

把UserControl转换为DataTemplate：

- 把UserControl的芯剪切出来，用<DataTemplate>标签包装，再放进主窗体的资源词典里
- 用Binding在控件与数据间建立联系，去掉控件中大多数x:Name
- 有些属性值不能直接拿来用，比如厂商名或汽车名不能直接作为图片路径，要用Converter
  - 把Converter以资源的形式放在资源词典里
  - 为Converter准备一个静态属性，形成单件模式，在XMAL代码里使用{x:Static}标签扩展来访问

