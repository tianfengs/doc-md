- Solution是最顶层，解决方案，对应一个客户需求

- Solution下面可以有很多Project

  - Project有三种类型：windows应用程序，控制台程序、类库

  - Project的编译结果叫做程序集Assembly，每个Project都会产生一个程序集

    - 源代码级别

      - Properties：资源文件

      - References：引用的类库集

        - 类库上双击，可以打开Object Browser，可以查看其包含的命名空间和方法目录树

      - App.xaml，App.xaml.cs对应应用程序本身，设置主窗体

      - MainWindow.xaml,MainWindow.xaml.cs，主窗口和窗口后台代码

        - xaml语言是声明性语言，标签-->对象
        - Attribute属性
          - Title，Height，Width,WindowStyle
          - xmlns，xmlns:x ,引入名称空间namespace，硬编码字符串，看起来像地址，是映射一系列名称空间
            - xmlns ：跟绘制界面相关presentation系列
            - xmlns：x 跟编译解析xaml相关xaml系列
        - 两个文件都是在MainWindow这个类，后台代码用partial保证在两个文件中创建类，前台代码用x:Class来限定
        - 用IL Disaassembler来查看程序集中间代码
        - Red Gate's .NET Reflector也可以用来查看程序集中间代码
