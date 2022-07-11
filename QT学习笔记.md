---
typora-root-url: pics
typora-copy-images-to: pics
---

# Qt教程，Qt5编程入门教程（非常详细）

>  http://c.biancheng.net/qt/ 

 Qt 是一个跨平台的 C++ 框架（C++库），目前最新的版本是 Qt5。Qt5 还包含了很多小版本，其中推荐 **Qt5.6** 或 **Qt5.9**，这两个版本是 LTS 版本（即长期支持版本），Bug较少，相对稳定。 

Qt 除了支持界面设计（GUI编程），还封装了与网络编程、多线程、数据库连接、视频音频等相关的功能。

这套 Qt 教程以 Qt 5.9 为基础来介绍 Qt 开发，配有精美的图片以及完整的示例程序，几乎涉及 Qt 编程的所有模块。

## 1. QT是什么

 [Qt](http://c.biancheng.net/qt/)（官方发音 [kju:t]，音同 cute）是一个跨平台的 [C++](http://c.biancheng.net/cplus/) 开发库，主要用来开发图形用户界面（Graphical User Interface，GUI）程序，也可以开发不带界面的命令行（Command User Interface，CUI）程序。  

 Qt 还存在 [Python](http://c.biancheng.net/python/)、Ruby、Perl 等脚本语言的绑定， 也就是说可以使用脚本语言开发基于 Qt 的程序。

Qt 支持的操作系统有很多，例如通用操作系统 Windows、[Linux](http://c.biancheng.net/linux_tutorial/)、Unix，智能手机系统 [Android](http://c.biancheng.net/android/)、iOS、WinPhone， 嵌入式系统 QNX、VxWorks 等等。 

### Qt 可以做什么？

 经常被当做一个 GUI 库，用来开发图形界面应用程序。 Qt 除了可以绘制漂亮的界面（包括控件、布局、交互），还包含很多其它功能，比如多线程、访问数据库、图像处理、音频视频处理、网络通信、文件操作等，这些 Qt 都已经内置了。 

总起来说，Qt 主要用于桌面程序开发和嵌入式开发。 （ Qt 虽然也支持手机操作系统，但是由于 Android 本身已经有 [Java](http://c.biancheng.net/java/) 和 Kotlin，iOS 本身已经有 Objective-C 和 Swift，所以 Qt 在移动端的市场份额几乎可以忽略。 ）



## 2. Qt和其它GUI库的对比

Windows 下的 GUI 解决方案比较多：

- 基于 [C++](http://c.biancheng.net/cplus/) 的有 [Qt](http://c.biancheng.net/qt/)、MFC、WTL、wxWidgets、DirectUI、Htmlayout；
- 基于 [C#](http://c.biancheng.net/csharp/) 的有 WinForm、WPF；
- 基于 [Java](http://c.biancheng.net/java/) 的有 AWT、[Swing](http://c.biancheng.net/swing/)；
- 基于 Pascal 的 有Delphi；
- 基于[Go语言](http://c.biancheng.net/golang/)的有 walk 和 electron；
- 还有国内初露头角的 aardio；
- Visual Basic 曾经很流行，现在逐渐失去了色彩；
- 如果你有 Web 开发经验，也可以基于 Webkit 或 Chromium 将网页转换为桌面程序。


没有哪一种方案能够独霸 Windows，使用比较多的编程语言是 C++、C#、Java。

用 Qt 来开发 Windows 桌面程序有以下优点：

- 简单易学：Qt 封装的很好，几行代码就可以开发出一个简单的客户端，不需要了解 Windows API。
- 资料丰富：资料丰富能够成倍降低学习成本，否则你只能去看源码，关于 DirectUI、Htmlayout、aardio 的资料就很少。
- 漂亮的界面：Qt 很容易做出漂亮的界面和炫酷的动画，而 MFC、WTL、wxWidgets 比较麻烦。
- 独立安装：Qt 程序最终会编译为本地代码，不需要其他库的支撑，而 Java 要安装虚拟机，C# 要安装 .NET Framework。
- 跨平台：如果你的程序需要运行在多个平台下，同时又希望降低开发成本，Qt 几乎是必备的。

**Linux 下**常用的 GUI 库有基于 C++ 的 Qt、GTK+、wxWidgets，以及基于 Java 的 AWT 和 Swing。其中最著名的就是 Qt 和 GTK+：KDE 桌面系统已经将 Qt 作为默认的 GUI 库，Gnome 桌面系统也将 GTK+ 作为默认的 GUI 库。 



## 3. 学习QML还是C++？

 [Qt](http://c.biancheng.net/qt/)4 时代的主流就是传统部件（或叫控件）编程，所用的语言一般是 **[C++](http://c.biancheng.net/cplus/)**。 

为了适应手机移动应用开发， Qt5 将 **QML 脚本编程**提到与传统 C++ 部件编程相同的高度，力推 QML 界面编程 。 QML 包含大量使用手机移动设备的功能模块，比如基本部件（QtQuick 模块）、GPS 定位、渲染特效、蓝牙、NFC、WebkKit 等等。 

QML 类似于网页设计的 HTML，是一种标记语言，我们可以借助 CSS 对它进行美化，也可以借助 [JavaScript](http://c.biancheng.net/js/) 进行交互。 

使用 QML 开发界面主要有以下几个优点：

- QML 非常灵活，可以做出非常炫酷的效果，例如 QQ、360、迅雷等都不在话下。
- QML 是标记语言，见名知意，非常容易编写和阅读，大大提高了开发和维护效率。
- QML 界面简洁大气，有很多动画，适合移动端。
- 不同平台下的 QML 使用相同的渲染机制，界面效果一致，不会随操作系统的不同而变化

**QML 只能用来进行界面设计和人机交互，也就是只能胜任 UI 部分，在底层仍然需要调用 C++ 编写的组件来完善功能，比如访问数据库、网络通信、多线程多进程、文件读写、图像处理、音频视频处理等都离不开 C++。** 

 现阶段新生的 QML 还不如传统的 C++ 部件编程那样拥有丰富的开发组件，尤其缺乏复杂的企业级应用程序所必须的树等控件。这就决定了至少现阶段，真正大型的**桌面程序仍然只能选择以 C++ 为主、QML 为辅的开发模式**。 

 C++ 是 Qt 的基础，无论如何都要掌握，本教程也只讲解传统的 C++ 部件编程，不讲解 QML。 



## 4. Qt下载（多种下载通道+所有版本）

### Qt 官方下载（非常慢）

Qt 官网有一个专门的资源下载网站，所有的开发环境和相关工具都可以从这里下载，具体地址是：http://download.qt.io/

| 目录                 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| archive              | 各种 Qt 开发工具安装包，新旧都有（可以下载 Qt 开发环境和源代码）。 |
| community_releases   | 社区定制的 Qt 库，Tizen 版 Qt 以及 Qt 附加源码包。           |
| development_releases | 开发版，有新的和旧的不稳定版本，在 Qt 开发过程中的非正式版本。 |
| **learning**         | **有学习 Qt 的文档教程和示范视频**。                         |
| ministro             | 迷你版，目前是针对 [Android](http://c.biancheng.net/android/) 的版本。 |
| official_releases    | 正式发布版，是与开发版相对的稳定版 Qt 库和开发工具（可以下载Qt开发环境和源代码）。 |
| online               | Qt 在线安装源。                                              |
| snapshots            | 预览版，最新的开发测试中的 Qt 库和开发工具。                 |

 archive 和 official_releases 两个目录都有最新的 Qt 开发环境安装包，我们以 archive 目录里的内容为例来说明。点击进入 archive 目录，会看到四个子目录： 

| 目录              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| vsaddin           | 这是 Qt 针对 Visual Studio 集成的插件，本教程基本不使用 Visual Studio ，所以不需要插件。 |
| qtcreator         | 这是 Qt 官方的集成开发工具，但是 qtcreator 本身是个空壳，它没有编译套件和 Qt 开发库。  除了老版本的 Qt 4 需要手动下载 qtcreator、编译套件、Qt 开发库进行搭配之外，一般用不到。对于我们教程压根不需要下载它，因为 Qt 5 有专门的大安装包，里面包含开发需要的东西，并且能自动配置好。 |
| **qt**            | **这是 Qt 开发环境的下载目录，我们刚说的 Qt 5 的大安装包就在这里面。** |
| online_installers | 在线安装器，国内用户不建议使用，在线安装是龟速，还经常断线。**我们教程采用的全部是离线的大安装包。** |

 我们再进入 qt 子目录 ，看到如下列表： 

 由于 Qt 5.9 是一个长期技术支持版本（Long Term Support，LTS），在未来几年里都将有更新支持，因此，本教程以 Qt 5.9 LTS 版本为例进行讲解，并且所有实例程序均使用 Qt 5.9 编译测试通过。 

 进入 5.9 目录，会看到各种子版本： 

 ![ qt 5.9 å­ç®å½](http://c.biancheng.net/uploads/allimg/190528/1-1Z52QI023222.gif) 

这里解释一下 Qt 的版本号，比如 5.9.8 是完整的 Qt 版本号，第一个数字 5 是大版本号（major），第二个数字 9 是小版本号（minor），第三个数字 8 是补丁号（patch）。 只要前面两个数字相同，Qt 的特性就是一致的，最后的数字是对该版本的补丁更新。也就是说本教程对 5.9.* 系列的 Qt 都是通用的，下载 5.9.* 任意一个版本都可以，这里我们以下载 5.9.0。 点击 5.9.0，进入子目录： 

 ![Qt 5.9.0 ä¸è½½é¡µé¢](http://c.biancheng.net/uploads/allimg/190528/1-1Z52QK0309D.gif) 

根据不同的操作系统，选择不同的安装包即可，不用管源码包，除非你想自己编译或者阅读源码。
我们以 Windows 安装包（qt-opensource-windows-x86-5.9.0.exe）讲解一下 Qt 安装包命名规则，其中：

- opensource 是指开源版本；
- windows 是指开发环境的操作系统；
- x86 是指 32 位系统；
- 5.9.0 是 Qt 版本号。

### Qt 国内镜像站下载（较快）

 ![å½åéåä¸è½½é¾æ¥](http://c.biancheng.net/uploads/allimg/190528/1-1Z52QP11N93.gif) 

可以清楚地看到，Qt 在国内的有三个镜像网站可以下载，点击这些地址中的一个就可以下载，从国内镜像网站下载速度快一些。

#### 国内镜像网站

这里给大家推荐几个国内著名的 Qt 镜像网站，主要是各个高校的：

- 中国科学技术大学：http://mirrors.ustc.edu.cn/qtproject/
- 清华大学：https://mirrors.tuna.tsinghua.edu.cn/qt/
- 北京理工大学：http://mirror.bit.edu.cn/qtproject/
- 中国互联网络信息中心：https://mirrors.cnnic.cn/qt/


国内镜像网站的结构和官方是类似的，我们在第一部分已经分析过了，这里不再赘述。

### 扩展阅读

对 Qt 版本更新感兴趣的读者请访问 Qt wiki 网站，地址为：https://wiki.qt.io/Main
https://www.qt.io/



## 5. 图解Qt安装（Windows平台）

 本节介绍 [Qt](http://c.biancheng.net/qt/) 5.9.0 在 Windows 平台下的安装，请提前下载好 Qt 5.9.0。  目前较高版本的 Qt 仅支持 Win7 及其以后的操作系统，不支持 Win XP；使用 Win XP 的读者请安装 Qt 5.5.1 之前的版本。 

 Qt 占用的存储空间很大，安装之前建议先准备好 8GB 以上的磁盘空间。对于目前 Qt 最新版开发环境，如果不安装源代码包，实际占用大约 5.5GB；如果选择安装源码包，大约占用 7.5GB。 

### 1) 注册和登录

Qt 在安装过程中会提示用户进行注册和登录，不用理会，跳过（Skip）即可，实际开发时不需要登录。

### 2) 安装路径和关联文件

 Qt 允许用户自定义安装路径，但是请注意，安装路径不能带空格、中文字符或者其它任何特殊字符。 

 ![æå®Qtå®è£è·¯å¾](http://c.biancheng.net/uploads/allimg/190529/1-1Z529133255346.gif) 

 另外，该界面还会询问是否关联特定的文件类型。如果关联（默认是关联的），特定后缀的文件（包括 .cpp 文件）默认使用 Qt 打开。我喜欢使用纯文本编辑器（例如 Sublime Text）来打开 [C++](http://c.biancheng.net/cplus/) 源文件，所以我取消了该选项，读者根据自己的实际情况定夺。 

### 3) 选择安装组件

Qt 安装过程中最关键的一步是组件的选择，请看下图：

 ![Qtç»ä»¶](http://c.biancheng.net/uploads/allimg/190529/1-1Z52915401NT.gif) 

 Qt 的安装组件分为两部分：一部分是“Qt 5.9”分类下的，该分类包含的是真正的 Qt 开发库组件；另一部分是“Tools”分类下的，该分类包含的是集成开发环境和编译工具。

| **“Qt 5.9”分类下的开发组件**                   |                                                              |
| ---------------------------------------------- | ------------------------------------------------------------ |
| **组件**                                       | **说明**                                                     |
| MinGW 5.3.0 32 bit                             | 编译器模块。MinGW 是 Minimalist GNU for Windows 的缩写，MinGW 是 Windows 平台上使用的 GNU 工具集导入库的集合。是本教程使用 MinGW 编译，所以必须安装。 |
| UWP ***                                        | UWP 是 Windows 10 中 Universal Windows Platform 的简称，有不同编译器类型的 UWP，属于 MSVC 编译器生成的 Qt 库。如果不是开发 UWP 应用程序，就不需要，直接忽略。 |
| MSVC ***                                       | 针对 Windows 平台上的 MSVC 编译器的 Qt 组件，如 msvc2015 32-bit 和 msvc2015 64-bit 等。安装该组件需要计算机上已经安装相应版本的 Visual Studio。如果你不使用 MSVC 编译器进行开发，就不用安装。本教程使用 MinGW 编译组件，所以不用安装 MSVC *** 组件。 |
| [Android](http://c.biancheng.net/android/) *** | 这是针对安卓应用开发的 Qt 库，如果读者有安卓开发这方面需求可以自己选择安装，一般情况下用不到。 |
| Sources                                        | Qt 的源代码包，除非你想阅读 Qt 的源码，否则不用安装。        |
| Qt ***                                         | Qt 的附加模块，大部分建议安装，这些附加模块括号里的 TP 是指 Technology Preview ，技术预览模块的意思，还处在功能测试阶段，不是正式版模块；附加模块括号里的 Deprecated 是指抛弃的旧模块，兼容旧代码使用的，一般用不到。这些附加模块读者可以选择部分或都勾选了安装，占用空间不大。  部分组件说明：Qt Charts 是二维图表模块，用于绘制柱状图、饼图、曲线图等常用二维图表。Qt Data Visualization 是三维数据图表模块，用于数据的三维显示，如散点的三维空间分布、三维曲面等。Qt Scritp（Deprecated）是脚本模块，已被抛弃，不建议安装。 |
| **“Tools”分类下的开发组件**                    |                                                              |
| **组件**                                       | **说明**                                                     |
| Qt Creator 4.3.0                               | 这是集成开发环境，强制安装的，以后所有的项目和代码都在 Qt Creator 里面新建和编辑。 |
| Qt Creator 4.3.0 CDB Debugger surpport         | 用于和 CDB 调试工具对接，默认安装，一般用于调试 VC 编译的 Qt 程序。 |
| MinGW 5.3.0                                    | 这是开源的编译器套件，这本教程必须用到的，需要读者勾选安装。 |
| Strawberry Perl 5.22.1.3                       | 用于编译 Qt 源代码的 Perl 开发环境，不需要安装。如果读者以后用到，也可以另外手动安装，在搜索引擎搜索 Strawberry Perl 关键词，去 Strawberry Perl 官网下载最新的安装包是一样用的。 |

 选择完了组件，根据向导一步一步操作就可以了。安装完成后，在 Windows“开始”菜单中会看到 Qt 5.9.0 程序组。 

 ![Qt 5.9.0æåå«çç¨åº](http://c.biancheng.net/uploads/allimg/190529/1-1Z529155424L0.gif) 

| 程序                                      | 说明                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| Qt Creator 4.6.2 (Enterprise)             | Qt 的集成开发环境，本教程就使用它来创建和管理 Qt 项目。      |
| Assistant（Qt 助手）                      | 用来查看帮助文档，已被集成在 Qt Creator 中。                 |
| Designer（Qt 设计师）                     | 图形界面可视化编辑工具，已被集成在 Qt Creator 中，在 Qt Creator 中编辑或创建界面文件时，就可以自动打开。 |
| Linguist（Qt 语言家）                     | 多国语言翻译支持工具，可以用来编辑语言资源文件，在开发多语言界面的应用程序时会用到。 |
| Qt 5.11.1 for Desktop (MinGW 5.3.0 32bit) | Qt 命令行工具，用来配置 Qt 开发环境（主要是设置 PATH 变量）。 |

## 6. 图解Qt安装（Linux平台）

 [Linux](http://c.biancheng.net/linux_tutorial/) 发行版虽然众多，但 [Qt](http://c.biancheng.net/qt/) 安装过程大同小异，本节以 CentOS 7 为例来演示 Qt 的安装。  Qt 5.9.0 Linux 安装包的名称为 qt-opensource-linux-x64-5.9.0.run。 

可以执行如下命令开启执行权限：

```shell
chmod +x /文件夹路径/qt-opensource-linux-x64-5.9.0.run
```

将“文件夹路径”替换为自己系统里该 run 文件的路径即可。然后可以直接点击运行该 run 文件，开始安装。

 注意以下是以普通用户权限安装的，安装位置是 /home/用户名/Qt5.9.0/，如果希望安装到其他的目录（如 /opt/ 目录），是需要 root 权限的。 

 Qt 安装过程中最关键的一步是组件的选择，请看下图： 

 ![Qt ç»ä»¶](http://c.biancheng.net/uploads/allimg/190531/1-1Z53115100C14.gif) 

| “Qt 5.9”分类下的开发库                         |                                                              |
| ---------------------------------------------- | ------------------------------------------------------------ |
| 开发库                                         | 说明                                                         |
| Desktop gcc 64-bit                             | 这是使用桌面版 [GCC](http://c.biancheng.net/gcc/) 64-bit 编译环境生成的 Qt 库，是 Qt 的核心，必须安装。  注意，虽然名字看起来像是 [GCC 编译器](http://c.biancheng.net/gcc/)套件，但实际上说的是 Qt 库的编译环境。大部分 [Linux 发行版](http://c.biancheng.net/view/710.html)都会预装 GCC 编译器套件，所以 Qt 安装包没必要附带它们。 |
| [Android](http://c.biancheng.net/android/) *** | 这是针对安卓应用开发的 Qt 库，如果读者有安卓开发这方面需求可以自己选择安装，一般情况下用不到。 |
| Sources                                        | Qt 的源代码包，除非你想阅读 Qt 的源码，否则不用安装。        |
| Qt ***                                         | Qt 的附加模块，大部分建议安装，这些附加模块括号里的 TP 是指 Technology Preview ，技术预览模块的意思，还处在功能测试阶段，不是正式版模块；附加模块括号里的 Deprecated 是指抛弃的旧模块，兼容旧代码使用的，一般用不到。这些附加模块读者可以选择部分或都勾选了安装，占用空间不大。  部分组件说明：Qt Charts 是二维图表模块，用于绘制柱状图、饼图、曲线图等常用二维图表。Qt Data Visualization 是三维数据图表模块，用于数据的三维显示，如散点的三维空间分布、三维曲面等。Qt Scritp（Deprecated）是脚本模块，已被抛弃，不建议安装。 |
| “Tools”分类下的集成开发环境                    |                                                              |
| 集成开发环境                                   | 说明                                                         |
| Qt Creator 4.3.0                               | 这是集成开发环境，强制安装的，以后所有的项目和代码都在 Qt Creator 里面新建和编辑。 |



## 7. Linux Qt cannot find -lGL错误完美解决方案（亲测有效）

[Qt](http://c.biancheng.net/qt/) 安装完成后如果直接编译或者运行项目，会出现“cannot find -lGL”错误，如下图所示：

 ![cannot find -lGL](http://c.biancheng.net/uploads/allimg/190531/1-1Z531142I3161.gif) 

 这是因为 Qt 找不到 OpenGL 的动态链接库（libGL.so）。 

Qt 默认在 **/usr/lib/ 目录**下查找动态链接库，但是很多 Linux 发行版将 OpenGL 链接库放在其它目录，例如我使用的是 CentOS 7，OpenGL 链接库位于 **/usr/lib64/ 目录**，而对于 Ubuntu，OpenGL 链接库位于 **/usr/lib/i386-linux-gnu/mesa/ 目录**。只要我们把 libGL.so 拷贝到 /usr/lib/ 目录，或者**在 /usr/lib/ 目录下为 libGL.so 创建一个链接**，就能解决问题。显然第二种办法更好。 

另外，Linux 发行版自带的 OpenGL 链接库在后缀中添加了版本号，例如 libGL.so.1、libGL.so.1.2.0、libGL.so.1.3.1 等，但是 Qt 在链接阶段查找的 OpenGL 链接库是不带版本号的。 

**总起来说，我们需要在 /usr/lib/ 目录下为 OpenGL 链接库创建一个链接，并去掉版本号** 。

如果你不知道当前 Linux 系统中 libGL.so 的具体路径，可以使用`locate libGL``find /usr -name libGL*``ln -s`

```shell
#查找 libGL 所在位置
[root@localhost ~]# locate libGL
/usr/lib64/libGL.so
/usr/lib64/libGL.so.1
/usr/lib64/libGL.so.1.2.0
/usr/share/doc/mesa-libGL-9.2.5
/usr/share/doc/mesa-libGL-9.2.5/COPYING

#创建链接
[root@localhost ~]# ln -s /usr/lib64/libGL.so.1 /usr/lib/libGL.so
```

Linux 系统中可能存在多个版本的 libGL.so，为任意一个版本创建链接即可。普通用户没有权限创建链接，所以我使用了 root 用户。
完成以上操作，再次启动 Qt，然后编译或者运行，就不会出现“cannot find -lGL”错误了。



## 8. 解密Qt安装目录的结构

### Qt 整体目录结构

不同版本 Qt 的安装目录结构大同小异，本节我们以 Qt 5.9.0 为例来说明，如下图所示。

 ![Qt å®è£ç®å½çç»æ](http://c.biancheng.net/uploads/allimg/190529/1-1Z52916344QD.gif) 

注意，~\5.9\ 和 ~\Tools\ 目录下都有 mingw53_32 目录（图中我用红色标出来了），但是两者是有区别的：

- ~\5.9\mingw53_32\ 目录包含的是 Qt 的类库文件，例如头文件、静态库、动态库等，这些类库文件使用 MinGW 工具集编译而成。
- ~\Tools\mingw53_32\ 目录包含的是 MinGW 工具集，例如编译器 g++、链接器 ld、make 工具、打包工具 ar 等。

QtCreator 是个例外，QtCreator 使用 MSVC2015 编译生成的，所以安装目录里有一个 vcredist 文件夹存储 VC 运行库安装文件。 

最后的 MaintenanceTool.exe ，对于离线安装包，它只能用于删除软件包，如果 Qt 开发环境是用在线安装方式装的，这个工具还可以管理开发环境组件和升级组件。

Qt 类库的帮助文件位于 Docs 文件夹里，需要用 Qt Assistant 工具才能查看。

Examples 里是示例代码，可以用 QtCreator 集成开发环境打开各个示例。 

### Qt 类库目录

下面我们再探究一下 Qt 类库目录（~\5.9\mingw53_32\）的结构，如下图所示。

 ![Qt ç±»åºç®å½](http://c.biancheng.net/uploads/allimg/190529/1-1Z5291A334220.gif) 

## 9. 认识一下Qt用到的开发工具

 Qt 官方只是开发了上层工具。下面我们分几个部分讲解 Qt 使用到的工具链。 

### GNU 工具集

 系统程序员 Richard M. Stallman (RMS) 在此环境下创立了与众不同的 [GNU 项目](https://www.gnu.org/) (**GNU's Not Unix**) ， 以及推进自由软件发展的 [Free Software Foundation (FSF) 自由软件基金会](https://www.fsf.org/)。 

 GUN 开发类 Unix 系统的项目失败了，但是它开发的一系列工具集却用到了后来的 Linux 内核上，两者结合形成了今天的各种 Linux 发行版  

在 GNU 工具集里面，开发时常见到的几个罗列如下（这些工具通常位于 Linux 或 Unix 系统里的 /usr/bin/ 目录）：

| 工具 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| gcc  | GNU C 语言编译器。                                           |
| g++  | GNU [C++](http://c.biancheng.net/cplus/) 语言编译器。        |
| ld   | GNU 链接器，将目标文件和库文件链接起来，创建可执行程序和动态链接库。 |
| ar   | 生成静态库 .a ，可以编辑和管理静态链接库。                   |
| make | 生成器，可以根据 makefile 文件自动编译链接生成可执行程序或库文件。 |
| gdb  | 调试器，用于调试可执行程序。                                 |
| ldd  | 查看可执行文件依赖的共享库（扩展名 .so，也叫动态链接库）。   |

### MinGW

原本 GNU 工具只在 Linux/Unix 系统里才有，随着 Windows 系统的广泛使用， 为了在 Windows 系统里可以使用 GNU 工具，诞生了 MinGW（Minimalist GNU for Windows） 项目，利用 MinGW 就可以生成 Windows 里面的 exe 程序和 dll 链接库。

需要注意的是，MinGW 与 Linux/Unix 系统里 GNU 工具集的有些区别：

- MinGW 里面工具带有扩展名 .exe， Linux/Unix 系统里工具通常都是没有扩展名的。
- MinGW 里面的生成器文件名为 **mingw32-make.exe**，Linux/Unix 系统里就叫 **make**。
- MinGW 在链接时是链接到 *.a 库引用文件，生成的可执行程序运行时依赖 ***.dll**，而 Linux/Unix 系统里链接时和运行时都是使用 ***.so** 。

另外 MinGW 里也没有 ldd 工具，因为 Windows 不使用 .so 共享库文件。如果要查看 Windows 里可执行文件的依赖库，需要使用微软自家的 **Dependency Walker 工具**。Windows 里面动态库扩展名为 .dll，**MinGW 可以通过 dlltool 来生成用于创建和使用动态链接库需要的文件**，如 .def 和 .lib。

 从 MinGW 分离出来了 MinGW-w64 项目，该项目同时支持生成 64 位和 32 位程序。Qt 的 MinGW 版本库就是使用 MinGW-w64 项目里面的工具集生成的。 

#### MSYS（Minimal SYStem）

 MSYS 对于熟悉 Unix/Linux 系统环境或者要尝试学习 Unix/Linux 系统的人都是一种便利。MSYS 和 MinGW 的安装升级都是通过其官方的 mingw-get 工具实现，二者是统一下载安装管理的。

对于 MinGW-w64 项目，它对应的小型系统环境叫 MSYS2（Minimal SYStem 2），MSYS2 是 MSYS 的衍生版，不仅支持 64 位系统和 32 位系统，还有自己的独特的软件包管理工具，它从 Arch Linux 系统里移植了 pacman 软件管理工具，所以装了 MSYS2 之后，可以直接通过 pacman 来下载安装软件，而且可以自动解决依赖关系、方便系统升级等。装了 MSYS2 之后，不需要自己去下载 MinGW-w64，可以直接用 pacman 命令安装编译链接工具和 git 工具等。

MinGW 项目主页（含 MSYS）： http://www.mingw.org/
MinGW-w64 项目主页： https://sourceforge.net/projects/mingw-w64/
MSYS2 项目主页： https://sourceforge.net/projects/msys2/ 

### CMake

CMake（Cross platform Make）是一个开源的跨平台自动化构建工具， 可以跨平台地生成各式各样的 makefile 或者 project 文件， 支持利用各种编译工具生成可执行程序或链接库。

CMake 自己不编译程序， 它相当于用自己的**构建脚本 CMakeLists.txt**，叫各种编译工具集去生成可执行程序或链接库。 

**一般用于编译程序的 makefile 文件比较复杂，自己去编写比较麻烦， 而利用 CMake ，就可以编写相对简单的 CMakeLists.txt ，由 CMake 根据 CMakeLists.txt 自动生成 makefile，然后就可以用 make 生成可执行程序或链接库**。 

 本教程里面是使用 Qt 官方的 qmake 工具生成 makefile 文件，没有用 CMake。这里之所以提 CMake，是因为整个 KDE 桌面环境的茫茫多程序都是用 CMake 脚本构建的，另外跨平台的程序/库如 Boost C++ Libraries、[OpenCV](http://c.biancheng.net/opencv/)、LLVM、Clang 等也都是用 CMake 脚本构建的。以后如果接触到这些东西，是需要了解 CMake 的。

CMake 项目主页：https://cmake.org/
KDE 项目主页：https://www.kde.org/ 

### Qt 工具集

Qt 官方的开发环境安装包里有自己专门的开发工具，之前用过 **qmake 命令**。

qmake 是 Qt 开发最核心的工具：

- 既可以生成 Qt 项目文件 .pro ，
- 也可以自动生成项目的 Makefile 文件。 

| 工具      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| qmake     | 核心的项目构建工具，**可以生成跨平台的 .pro 项目文件**，并能依据不同操作系统和编译工具**生成相应的 Makefile**，用于构建可执行程序或链接库。 |
| uic       | User Interface Compiler，**用户界面编译器**，Qt 使用 XML 语法格式的 .ui 文件定义用户界面，uic 根据 .ui 文件生成用于创建用户界面的 C++ 代码头文件，比如 ui_*****.h 。 |
| moc       | Meta-Object Compiler，**元对象编译器**，moc 处理 C++ 头文件的类定义里面的 Q_OBJECT 宏，它会生成源代码文件，比如 moc_*****.cpp ，其中包含相应类的元对象代码，元对象代码主要用于实现 Qt 信号/槽机制、运行时类型定义、动态属性系统。 |
| rcc       | Resource Compiler，**资源文件编译器**，负责在项目构建过程中编译 .qrc 资源文件，将资源嵌入到最终的 Qt 程序里。 |
| qtcreator | **集成开发环境**，包含项目生成管理、代码编辑、图形界面可视化编辑、 编译生成、程序调试、上下文帮助、版本控制系统集成等众多功能， 还支持手机和嵌入式设备的程序生成部署。 |
| assistant | **Qt 助手**，帮助文档浏览查询工具，Qt 库所有模块和开发工具的帮助文档、示例代码等都可以检索到，是 Qt 开发必备神器，也可用于自学 Qt。 |
| designer  | **Qt 设计师**，专门用于可视化编辑图形用户界面（所见即所得），生成 .ui 文件用于 Qt 项目。 |
| linguist  | **Qt 语言家**，代码里**用 tr() 宏包裹**的就是可翻译的字符串，开发人员可用 **lupdate 命令**生成项目的**待翻译字符串文件 .ts**，用 linguist 翻译多国语言 .ts ，翻译完成后用 lrelease 命令生成 .qm 文件，然后就可用于多国语言界面显示。 |
| qmlscene  | 在 Qt 4.x 里是用 qmlviewer **进行 QML 程序的原型设计和测试**，Qt 5 用 qmlscene 取代了旧的 qmlviewer。新的 qmlscene 另外还支持 Qt 5 中的新特性 scenegraph 。 |



## 10. Qt编程涉及的术语和名词

### Project

Project 的中文翻译是“项目”或者“工程”，这里的项目是指为实现某个相对独立功能的程序代码合集，这些代码不单单是放在一块，而是有相互之间的关联性，并且有专门负责管理该项目的项目文件，比如：

- Qt 使用 .pro 文件管理项目；
- VC++ 则使用 .vcproj 作为项目文件。


集成开发环境通常都是依据项目文件（.pro/.vcproj）管理和构建项目。

### Makefile

 简单的说，就是定义好 Makefile ，让程序员只需要去关注如何编写代码，而生成程序过程中的脏活累活都交给 make 程序。

现在 Makefile 通常都有工具自动生成，如 qmake 工具， 这样就大量减轻了程序员的负担。 

### Debug 和 Release

 Debug 即调试，Release 即发行。 

Debug 版本和 Release 版本使用的库文件不一样：

- Debug 版本程序通常链接的也是 Debug 版本的库文件，比如 libQt5Guid.a/Qt5Guid.dll，库文件的简短名（不含扩展名）都是以 d 结尾的，Debug 库通常都比较大 。
- Release 版本程序链接的通常就是 Release 版本的库文件，Release 版本库文件名字比 Debug 版本库文件少一个字母 d ，如 libQt5Gui.a/Qt5Gui.dll，而且 Release 版本库一般都比 Debug 版本小很多，运行效率也高很多。

### C++11 标准

 时代在变化，C++ 标准也在前进。C++ 正式公布标准有 C++98、C++03、C++11。最新的 C++11 标准是2011年8月12日公布的，在公布之前该标准原名为 C++0x 。**这是一次较大的修订和扩充，建议读者专门学一下**。

Qt 从 4.8 版本就开始用 C++11 新特性了。编译器里面开始支持 C++11 的版本是 MSVC 2010、[GCC](http://c.biancheng.net/gcc/) 4.5、Clang 3.1，这之后版本的编译器都在逐步完善对 C++11 的支持，现在新版本编译器对新标准的支持都比较全面了。 

Qt 官方在编译 Qt5 库的时候都是开启 C++11 特性的，如果我们要在自己项目代码启用新标准，需要在 .pro 文件里面添加一行：

```
CONFIG += c++11
```

如果是 Qt4 版本则是添加：

```
gcc:CXXFLAGS += -std=c++0x
```

MSVC 编译器默认开启 C++11 特性，GCC（g++命令）则需要自己添加选项 -std=c++0x ，上面 CXXFLAGS 就是为 [GCC 编译器](http://c.biancheng.net/gcc/)。

### Dynamic Link 和 Static 

#### 动态链接库

目标程序通常都不是独立个体，生成程序时都需要链接其他的库，要用到其他库的代码。对于多个程序同时运行而言，内存中就可能有同一个库的多个副本，占用了太多内存而干的活差不多。
为了优化内存运用效率，引入了动态链接库（Dynamic Link Library），或叫共享库（Shared Object）。使用动态链接库时，内存中只需要一份该库文件，其他程序要使用该库文件时，只要链接过来就行了。由于动态库文件外置，链接到动态库的目标程序相对比较小，因为剥离了大量库代码，而只需要一些链接指针。

#### 静态链接库

静态库就是将链接库的代码和自己编写的代码都编译链接到一块，链接到静态库的程序通常比较大，但好处是运行时依赖的库文件很少，因为目标程序自己内部集成了很多库代码。

#### 库文件后缀

[Linux](http://c.biancheng.net/linux_tutorial/)/Unix 系统里静态库扩展名一般是 **.a**，动态库扩展名一般是 **.so** 。Windows 系统里 VC 编译器用的静态库扩展名一般是 **.lib**，动态库扩展名一般是 **.dll** 。

MinGW 比较特殊，是将 GNU 工具集和链接库从 Linux/Unix 系统移植到 Windows 里， 有意思的情况就出现了，MinGW 使用的静态库扩展名为 .a ，而其动态库扩展名则为 .dll，.a 仅在生成目标程序过程中使用，.dll 则是在目标程序运行时使用。 

### Explicit Linking 和 Implicit Linking

Explicit Linking 即显式链接，Implicit Linking 即隐式链接，这两种都是动态链接库的使用方式。

**动态链接库**通常都有其**导出函数列表**， 告知其他可执行程序可以使用它的哪些函数。可执行程序使用这些导出函数有两种方式：一是在运行时使用主动加载动态库的函数，Linux 里比如用 dlopen 函数打开并加载动态库，Windows 里一般用 LoadLibrary 打开并加载动态库，只有当程序代码执行到这些函数时，其参数里的动态库才会被加载，这就是**显式链接**。**显式链接方式是在运行时加载动态库，其程序启动时并不检查这些动态库是否存在。** 

 隐式链接是最为常见的，所有的编译环境默认都是采用隐式链接的方式使用动态库。隐式链接会在链接生成可执行程序时就确立依赖关系，在该程序启动时，操作系统自动会检查它依赖的动态库，并一一加载到该程序的内存空间，程序员就不需要操心什么时候加载动态库了。比如 VC 编译环境，链接时使用动态库对应的 .lib 文件（包含动态库的导出函数声明，但没有实际功能代码），在 .exe 程序运行前系统会检查依赖的 .dll，如果找不到某个动态库就会出现类似下图对话框： 

 ![](http://c.biancheng.net/uploads/allimg/190530/1-1Z530100032603.gif) 

 MinGW 是将动态库的导出函数声明放在了 .a 文件里，程序运行依赖的动态库也是 .dll 。

请注意，VC 链接器使用的 .lib 文件分两类，一种是完整的静态库，体积比较大，另一种是动态库的导出声明，体积比较小。MinGW 链接器使用的 .a 文件也是类似的，Qt 官方库都是按照动态库发布的，静态库只有自己编译才会有。 



## 11. Qt Creator的初步使用

### Qt Creator 的设置

 单击 Qt Creator 菜单栏的 `Tools→Options` 菜单项会打开选项设置对话框（如图 2 所示）。对话框的左侧是可设置的内容分组，单击后右侧出现具体的设置界面。 

 ![Options ç Build&Run è®¾ç½®é¡µé¢](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q12949432.gif) 

常用的设置包括以下几点：

1. Environment（环境） 设置：在 Interface 页面可以设置语言和主题，本教程全部以中文界面的 Qt Creator 进行讲解，所以语言选择为 Chinese（China）；为了使界面抓图更清晰，设置主题为 Flat Light。更改语言和主题后需要重新启动 Qt Creator 才会生效。
2. Text Editor（文本编辑器）设置：在此界面可以设置文本编辑器的字体，设置各种类型文字的字体颜色，如关键字、数字、字符串、注释等字体颜色，也可以选择不同的配色主题。编辑器缺省字体的大小为 9，可以修改得大一些。
3. Build & Run（构建和运行）设置：图 2 显示的是 Build & Run 的设置界面，它有以下几个页面。
   - Kits（构建套件）页面显示 Qt Creator 可用的编译工具。
   - Qt Versions 页面显示安装的 Qt 版本。
   - Compliers（编译器）页面显示系统里可用的 C 和 [C++](http://c.biancheng.net/cplus/) 编译器，由于安装了 MinGW 和 Visual Studio 2015，Qt Creator 会自动检测出这些编译器。
   - Debuggers 页面显示 Qt Creator 自动检测到的调试器，有 GNU gdb for MinGW 调试器和 Windows 的 CDB 调试器。



## 12. 编写第一个Qt程序

### 新建一个项目

 ![æ°å»ºæä»¶æé¡¹ç®å¯¹è¯æ¡](http://c.biancheng.net/uploads/allimg/181228/2-1Q22QH622Q7.gif) 

Qt Creator 可以创建多种项目，在最左侧的列表框中单击“Application”，中间的列表框中列出了可以创建的应用程序的模板，各类应用程序如下：

- Qt Widgets Application，支持**桌面平台的有图形用户界面**（Graphic User Interface，GUI） 界面的应用程序。GUI 的设计完全基于 C++ 语言，采用 Qt 提供的一套 C++ 类库。
- Qt Console Application，**控制台应用程序，无 GUI 界面**，一般用于学习 C/C++ 语言，只需要简单的输入输出操作时可创建此类项目。
- Qt Quick Application，**创建可部署的 Qt Quick 2 应用程序**。Qt Quick 是 Qt 支持的一套 GUI 开发架构，其界面设计**采用 QML 语言**，**程序架构采用 C++ 语言**。利用 Qt Quick 可以设计非常炫的用户界面，**一般用于移动设备或嵌入式设备上无边框的应用程序的设计**。
- Qt Quick Controls 2 Application，创建基于 Qt Quick Controls 2 组件的可部署的 Qt Quick 2 应用程序。Qt Quick Controls 2 组件只有 Qt 5.7 及以后版本才有。
- Qt Canvas 3D Application，创建 Qt Canvas 3D QML 项目，也是**基于 QML 语言**的界面设计，**支持 3D 画布**。

 ![æ°å»ºé¡¹ç®æ³å°ç¬¬ 3 æ­¥ï¼éæ©çé¢åºç±»](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q51641419.gif) 

在此界面中选择需要创建界面的基类（base class）。有 3 种基类可以选择：

1. QMainWindow 是主窗口类，主窗口具有主菜单栏、工具栏和状态栏，类似于一般的应用程序的主窗口；
2. QWidget 是所有具有可视界面类的基类，选择 QWidget 创建的界面对各种界面组件都可以 支持；
3. QDialog 是对话框类，可建立一个基于对话框的界面；

勾选“创建界面”复选框。这个选项如果勾选，就会由 Qt Creator 创建用户界面文件，否则，需要自己编程手工创建界面。初始学习，为了了解 Qt Creator 的设计功能，勾选此选项。 

### 项目的文件组成和管理

 ![ é¡¹ç®ç®¡çä¸æä»¶ç¼è¾çé¢](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q51Hcc.gif) 

 左侧有上下两个子窗口，上方的目录树显示了项目内文件的组织结构，显示当 前**项目为 Demo**。**项目的名称**构成目录树的一个**根节点**，Qt Creator 可以打开多个项目，**但是只有一个活动项目**，活动项目的项目名称节点用粗体字体表示。 

在项目名称节点下面，分组管理着项目内的各种源文件，几个文件及分组分别为以下几项：

- **Demo.pro 是项目管理文件**，包括一些对项目的设置项。
- **Headers 分组**，该节点下是项目内的所有**头文件（.h）**，图中所示项目有一个头文件 mainwindow.h，是主窗口类的头文件。
- **Sources 分组**：该节点下是项目内的所有 **C++源文件（.cpp）**，图中所示项目有两个 C++ 源文件，mainwindow.cpp 是主窗口类的实现文件，与 mainwindow.h 文件对应。main.cpp 是主函数文件，也是应用程序的入口。
- **Forms 分组**：该节点下是项目内的所有**界面文件（.ui）**。图 5 中所示项目有一个界面文件mainwindow.ui，是主窗口的界面文件。界面文件是文本文件，**使用 XML 语言描述**界面的组成。

双击文件目录树中的文件mainwindow.ui，出现如图 6 所示的窗体设计界面：  这个界面实际上是 Qt Creator 中集成的 **Qt Designer**。 

### 项目的编译、调试与运行

 单击主窗口左侧工具栏上的“项目”按钮，出现如图所示的项目编译设置界面。 

 ![é¡¹ç®ç¼è¯å¨éæ©åè®¾ç½®çé¢](http://c.biancheng.net/uploads/allimg/181228/2-1Q22QHK4337.gif) 

 每个编译器又有 Build 和 Run 两个设置界面。在 Build 设置界面上，有一个“Shadow build” 复选框。如果勾选此项，编译后将在项目的同级目录下建立一个编译后的文件目录，目录名称包含编译器信息，这种方式一般用于使用不同编译器创建不同版本的可执行文件。如果不勾选此项，编译后将在项目的目录下建立“Debug”和“Release”子目录用于存放编译后的文件。 

 在设计完 mainwindow.ui 文件，并设置好编译工具之后，就可以对项目进行编译、调试或运行。主窗口左侧工具栏下方有 4 个按钮，其功能见表 :

| 图标                                                         | 作用                                                         | 快捷键 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| ![img](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q52043426.gif) | 弹出菜单选择编译工具和编译模式，如 Debug或 Release模式       |        |
| ![img](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q52144152.gif) | 直接运行程序，如果修改后未编译，会先进行编译。即使在程序中设置了断点，此方式运行的程序也无法调试。 | Ctrl+R |
| ![img](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q52211343.gif) | 项目需要以Debug模式编译，点此按钮开始调试运行，可以在程序中设置断点。若是以 Release模式编译，点此按钮也无法进行调试。 | F5     |
| ![img](http://c.biancheng.net/uploads/allimg/181228/2-1Q22Q52230b6.gif) | 编译当前项目                                                 | Ctrl+B |


首先对项目进行编译，没有错误后，再运行程序。

在 Qt Creator 中也可以对程序设置断点进行调试，但是必须以 Debug 模式编译，并以“Start Debugging”（快捷键 F5）方式运行程序。



## 13. VS中使用Qt方法详解

要在 Visual Studio 中使用 Qt，需要安装一个 Visual Studio 的 Qt 插件，这个插件程序由 Qt 公司提供。目前最新的 Visual Studio Qt 插件是`Visual Studio Add-in 2.3.0 for Qt5 MSVC 2017`，可以从 Qt 官网下载并安装。

![image-20191118162856907](/image-20191118162856907.png)

安装完成后，在 Visual Studio 的主菜单栏上增加了一个菜单组“Qt VS Tools”，在新建项目向导里增加了可创建 Qt 项目的项目模板。  

 在 Visual Studio 2015 里创建一个 Qt GUI 应用程序项目。创建项目时选择项目模板的对话框如图 1 所示，选择创建 Qt GUI Application 项目，根据向导提示完成项目 Demo 的创建。 

![2-1Q22QA535555](/2-1Q22QA535555.png)

 按照向导缺省设置创建完项目后，Visual Studio 管理项目的全部文件，有一个 Demo.ui 的 窗体文件，双击此文件，会自动使用 Qt Designer 打开窗体文件进行界面设计，如同在 Qt Creator 里设计窗体一样。 

 在首次使用 Visual Studio 编译 Qt 项目之前，必须先进行一些设置，否则会提示没有设置 Qt 版本，无法编译项目。  首先要设置 Qt 版本。单击 Visual Studio 菜单项`Qt VS Tools→Qt Options`，出现如图 2 所示的对话框。Qt Versions 页面显示了可以使用的 Qt 版本（这是已经设置好的界面），在未设置 之前，框里是空白的。单击“Add”按钮出现如图 3 所示的添加 Qt 版本对话框。 

 ![img](http://c.biancheng.net/uploads/allimg/181228/2-1Q22QA633G2.gif) 

 单击“Path”文本框后面的按钮，在出现的目录选择对话框里选择 Qt 5.9.1 安装目录下的 MSVC 编译器目录，如`D:\Qt\Qt5.9.1\5.9.1\msvc2015_64`。选择目录后，Version name 编辑框里会自动出现版本名称，可以修改此名称为意义更明显的名字，如“msvc2015-64bit”。 然后，再单击 Visual Studio 菜单项`Qt VS Tools→Qt Project Settings`，为项目设置 Qt 版本，出现如图 4 所示的对话框。在此对话框的 Properties 分页下的列表框里，在 Version 下拉列表框中选择某个 Qt 版本。 

 ![img](http://c.biancheng.net/uploads/allimg/181228/2-1Q22QAG5R9.gif) 

完成这两项设置后，再进行编译就没有问题了。项目的运行、调试等就都是 Visual Studio 的操作了，这里不再赘述。



## 14. Qt项目管理文件（.pro）及其作用详解

 在 [Qt](http://c.biancheng.net/qt/) Creator 中新建一个 Widget Application 项目 samp2_1，在选择窗口基类的页面选择 QWidget 作为窗体基类，并选中“Generate form”复选框。创建后的项目文件目录树如图示。

 ![img](http://c.biancheng.net/uploads/allimg/181229/2-1Q22Z95914431.gif) 

这个项目包含以下一些文件：

- 项目管理文件 samp2_1**.pro**，**存储项目设置的文件**。
- **主程序**入口文件 **main.cpp**，实现 main()函数的程序文件。
- **窗体界面文件** widget**.ui**，一个 XML 格式存储的窗体上的元件及 其布局的文件。
- widget**.h** 是所设计的**窗体类的头文件**，widget.cpp 是 widget.h 里 定义类的实现文件。[C++](http://c.biancheng.net/cplus/) 中，任何窗体或界面组件都是用类封装的，一个类一般有一个头文件（.h 文件）和一个源程序文件（.cpp 文件）。

###  项目管理文件（.pro文件） 

 如本项目中的 samp2_1.pro。 下面是 samp2_1.pro 文件的内容。 

```properties
QT       += core gui
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
TARGET = samp2_1
TEMPLATE = app
SOURCES += \
        main.cpp \
        widget.cpp
HEADERS += \
        widget.h
FORMS += \
        widget.ui
```

-  “Qt += core gui”表示项目中加入 core gui 模块。core gui 是 Qt 用于 GUI 设计的类库模块，如果创建的是控制台（Console）应用程序，就不需要添加 core gui。 

  - Qt 类库以模块的形式组织各种功能的类，根据项目涉及的功能需求，在项目中添加适当的类库模块支持。例如，如果项目中使用到了涉及数据库操作的类就需要用到 sql 模块，在 pro 文件中需要增加如下一行：

    ```properties
    Qt +=sql
    ```

-  条件执行语句，表示当 Qt 主版本大于 4 时，才加入 widgets 模块。 

  ```
  greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
  ```

-  “TARGET = samp2_1”表示生成的目标可执行文件的名称，即编译后生成的可执行文件是 samp2_1.exe。 

-  “TEMPLATE = app”表示项目使用的模板是 app，是一般的应用程序。 

-  后面的 SOURCES、HEADERS、FORMS 记录了项目中包含的源程序文件、头文件和窗体文件（.ui 文件）的名称。   这些文件列表 项目管理文件里的条目会自动修改。 



## 15. Qt项目界面文件（.ui）及其作用（超详细）

 [Qt](http://c.biancheng.net/qt/) 项目中，后缀为“.ui”的文件是可视化设计的窗体的定义文件 。

 ![éæå¨ Qt Creatorä¸­ç UI è®¾è®¡å¨](http://c.biancheng.net/uploads/allimg/181229/2-1Q22910293MB.gif) 

 本教程后面将称这个集成在 Qt Creator 中的 Qt Designer 为“**UI 设计器**”，以便与独立运行的 **Qt Designer** 区别开来。 

UI 设计器有以下一些功能区域：

- **组件面板**：窗口左侧是界面设计组件面板，分为多个组，如Layouts、Buttons、Display Widgets等，界面设计的常见组件都可以在组件面板里找到。
- 中间主要区域是**待设计的窗体**。如果要将某个组件放置到窗体上时，从组件面板上拖放一个组件到窗体上即可。例如，先放一个 Label 和一个 Push Button 到窗体上。
- **Signals 和 Slots 编辑器**与 **Action 编辑器**是位于待设计窗体下方的两个编辑器。Signals 和Slots 编辑器用于可视化地进行信号与槽的关联，Action 编辑器用于可视化设计 Action。
- **布局和界面设计工具栏**：窗口上方的一个工具栏，工具栏上的按钮主要实现布局和界面设计。
- **对象浏览器（Object Inspector）**：窗口右上方是 Object Inspector，用树状视图显示窗体上各组件之间的布局包含关系，视图有两列，显示每个组件的对象名称（ObjectName）和类名称。
- **属性编辑器（Property Editor）**：窗口右下方是属性编辑器，是界面设计时最常用到的编辑器。属性编辑器显示某个选中的组件或窗体的各种属性及其取值，可以在属性编辑器里修改这些属性的值。

| 文件名      | 功能                                                         |
| ----------- | ------------------------------------------------------------ |
| widget.h    | **定义窗体类的头文件**，定义了类Widget                       |
| widget.cpp  | Widget 类的功能实现源程序文件                                |
| widget.ui   | **窗体界面文件**，由UI设计器自动生成，存储了窗体上各个组件的属性设置和布局 |
| ui_widget.h | **编译后**，根据窗体上的组件及其属性、信号与槽的关联等**自动生成的一个类的定义文件**，类的名称是Ui_Widget |

下面分别分析各个文件的内容及其功能，以及它们是如何联系在一起工作，实现界面的创建与显示的。

### widget.h 文件

**widget.h** 文件是窗体类的头文件。在创建项目时，选择窗体基类是 QWidget，在 widget.h 中定义了一个继承自 QWidget 的类 Widget。 

```c++
// widget.h 文件
#ifndef WIDGET_H
#define WIDGET_H

#include <QWidget>

namespace Ui {								// 1. namespace 声明
class Widget;								//   不是本文件里定义的类 Widget，而是
}											//   ui_widget.h 文件里定义的类，用于描述界面组件的

class Widget : public QWidget	// 2. 主体部分是一个继承于 QWidget 的类 Widget 的定义，
{    							//    也就是本实例的窗体类。
    Q_OBJECT	// 2.1 在 Widget 类中使用了宏 Q_OBJECT，这是使用 Qt 的信号与槽（signal 和 slot）
        		//     机制的类都必须加入的一个宏
public:
    explicit Widget(QWidget *parent = 0);	// 2.2 在 public 部分定义了 Widget 类的    
    ~Widget();								//     构造函数和析构函数。

private:			// 2.3 在 private 部分又定义了一个指针。
    Ui::Widget *ui;	// 这个指针是用前面声明的 namespace Ui 里的 Widget 类定义的，所以指针ui是
    				// 指向可视化设计的界面，后面会看到要访问界面上的组件，都需要通过这个指针 ui。
};

#endif // WIDGET_H
```

### widget.cpp 文件

 widget.cpp 文件是类 Widget 的实现代码，下面是 widget.cpp 文件的内容 

```c++
#include "widget.h"		// 1. 这里是自动加入的：是Qt编译生成的与UI文件widget.ui对应的类定义文件
#include "ui_widget.h"

Widget::Widget(QWidget *parent) :
    QWidget(parent),	// 2. 构造函数头部
    ui(new Ui::Widget) 	// 执行父类 QWidget 的构造函数，创建一个 Ui::Widget 类的对象 ui。
        				// 这个 ui 就是 Widget 的 private 部分定义的指针变量 ui。
{
    // 执行Ui::Widget类的setupUi()函数，实现窗口的生成与各种属性的设置、信号与槽的关联
    ui->setupUi(this);
}	

Widget::~Widget()
{
    delete ui;		// 3. 析构函数只是简单地删除用 new 创建的指针 ui。
}
```

- 在 ui_widget.h 文件里有一个 namespace 名称为 Ui，里面有一个类 Widget 是用于描述可视化设计的窗体，且与 widget.h 里定义的类同名 
- 在 Widget 类里访问 Ui::Widget 类的成员变量或函数需要通过 Widget 类里的 ui 指针，如同构造函数里执行 ui->setupUi( this) 函数那样。 

### widget.ui 文件

 widget.ui 是窗体界面定义文件，是一个 XML 文件，定义了窗口上的所有组件的属性设置、布局，及其信号与槽函数的关联等。 

### ui_widget.h 文件

 ui_widget.h 是在对 widget.ui 文件编译后生成的一个文件，ui_widget.h 会出现在编译后的目录下，或与 widget.ui 同目录（与项目的 shadow build 编译设置有关）。 

注意，ui_widget.h 是对 widget.ui 文件编译后自动生成的，widget.ui 又是通过 UI 设计器可视化设计生成的。所以，**对 ui_widget.h 手工进行修改没有什么意义** 。

```c++
/********************************************************************************
** Form generated from reading UI file 'widget.ui'
**
** Created by: Qt User Interface Compiler version 5.9.1
**
** WARNING! All changes made in this file will be lost when recompiling UI file!
********************************************************************************/

#ifndef UI_WIDGET_H
#define UI_WIDGET_H

#include <QtCore/QVariant>
#include <QtWidgets/QAction>
#include <QtWidgets/QApplication>
#include <QtWidgets/QButtonGroup>
#include <QtWidgets/QHeaderView>
#include <QtWidgets/QLabel>
#include <QtWidgets/QPushButton>
#include <QtWidgets/QWidget>

QT_BEGIN_NAMESPACE

class Ui_Widget
{
public:
    QLabel *label;
    QPushButton *btnClose;

    void setupUi(QWidget *Widget)
    {
        if (Widget->objectName().isEmpty())
            Widget->setObjectName(QStringLiteral("Widget"));
        Widget->resize(336, 216);
        label = new QLabel(Widget);
        label->setObjectName(QStringLiteral("label"));
        label->setGeometry(QRect(100, 70, 141, 61));
        QFont font;
        font.setPointSize(12);
        font.setBold(true);
        font.setWeight(75);
        label->setFont(font);
        btnClose = new QPushButton(Widget);
        btnClose->setObjectName(QStringLiteral("btnClose"));
        btnClose->setGeometry(QRect(210, 150, 75, 23));

        retranslateUi(Widget);
        QObject::connect(btnClose, SIGNAL(clicked()), Widget, SLOT(close()));

        QMetaObject::connectSlotsByName(Widget);
    } // setupUi

    void retranslateUi(QWidget *Widget)
    {
        Widget->setWindowTitle(QApplication::translate("Widget", "My First Demo", Q_NULLPTR));
        label->setText(QApplication::translate("Widget", "Hello\357\274\214World", Q_NULLPTR));
        btnClose->setText(QApplication::translate("Widget", "Close", Q_NULLPTR));
    } // retranslateUi

};

namespace Ui {
    class Widget: public Ui_Widget {};
} // namespace Ui

QT_END_NAMESPACE

#endif // UI_WIDGET_H
```

它主要做了以下的一些工作：

1. 定义了一个类 Ui_Widget，用于封装可视化设计的界面。

2.  自动生成了界面各个组件的类成员变量定义。 自动生成的定义是： 

   ```
   QLabel *LabDemo;
   QPushButton *btnClose;
   ```

3.  **定义了 setupUi() 函数**，这个函数用于创建各个界面组件，并设置其位置、大小、文字内容、字体等属性，设置信号与槽的关联。 

   setupUi() 调用了**函数 retranslateUi(Widget)**，用来设置界面各组件的文字内容属性，如标签的文字、按键的文字、窗体的标题等。将界面上的文字设置的内容独立出来作为一个函数 retranslateUi()，在设计多语言界面时会用到这个函数。 

    setupUi() 函数的第三部分是设置信号与槽的关联， 

   ```c++
   QObject::connect(btnClose, SIGNAL(clicked()), Widget, SLOT(close()));
   QMetaObject::connectSlotsByName(Widget);
   ```

    第1 行是调用 connect() 函数，将在 UI 设计器里设置的信号与槽的关联转换为语句。 

    第 2 行是设置槽函数的关联方式，用于将 UI 设计器自动生成的组件信号的槽函数与组件信号相关联。 

4.  定义 namespace Ui，并定义一个从Ui_Widget 继承的类Widget 

   ```
   namespace Ui {
       class Widget: public Ui_Widget {};
   }
   ```

   提示：ui_widget.h 文件里实现界面功能的类是 Ui_Widget。再定义一个类 Widget 从 Ui_Widget 继承而来，并定义在 namespace Ui 里，这样 Ui:: Widget 与 widget.h 里的类 Widget 同名，但是用 namespace 区分开来。所以，界面的 Ui:: Widget 类与文件 widget.h 里定义的 Widget 类实际上是两个类，但是 Qt 的处理让用户感觉不到 Ui:: Widget 类的存在，只需要知道在 Widget 类里用 ui 指针可以访问可视化设计的界面组件就可以了。 

## 16. Qt项目中main主函数及其作用

```c++
// main.cpp 文件
#include "widget.h"
#include <QApplication>
int main(int argc, char *argv[])
{
    QApplication a(argc, argv); //定义并创建应用程序
    Widget w; //定义并创建窗口
    w.show(); //显示窗口
    return a.exec(); //应用程序运行
}
```

## 17. Qt界面布局管理详解

#### 字体样式设置

窗体在[设计模式](http://c.biancheng.net/design_pattern/)


![QcheckBox的Go to slot对话框](http://c.biancheng.net/uploads/allimg/181229/2-1Q229141404493.gif)
图 9 QcheckBox的Go to slot对话框


该对话框列出了 QCheckBox 类的所有信号，第一个是 clicked()，第二个是带一个布尔类型参数的 clicked(bool)。

选择 clicked(bool)，然后单击“OK”按钮，在 QWDialog 的类定义中，会在 private slots 部分自动增加一个槽函数声明，函数名是根据发射对象及其信号名称自动命名的。

```
void on_chkBoxUnder_clicked(bool checked);
```

同时，在 qwdialog.cpp 文件中自动添加了函数 on_chkBoxUnder_clicked(bool) 的框架，在此函数中添加如下的代码，实现文本框字体下划线的控制。
以同样的方法为 Italic 和 Bold 两个 CheckBox设计槽函数，编译后运行，发现已经实现了修改字体的下划线、斜体、粗体属性的功能，说明信号与槽函数已经关联了。

查看 QWDialog 的构造函数，构造函数只有简单的一条语句。
这里没有发现用 connect() 函数进行几个 CheckBox 的信号与槽函数关联的操作。这些功能是如何实现的呢？

```
QMetaObject::connectSlotsByName(QWDialog);
```

秘密就在于这条语句。connectSlotsByName(QWDialog) 函数将搜索 QWDialog 界面上的所有组件，将信号与槽函数匹配的信号和槽关联起来，它假设槽函数的名称是：

```
void on_<object name>_<signal name>(<signal parameters>);
```

例如，通过 UI 设计器的操作，**为 chkBoxUnder 自动生成的槽函数是**：

```
void on_chkBoxUnder_clicked(bool checked);
```

它就正好是 chkBoxUnder 的信号 clicked(bool) 的槽函数。那么，connectSlotsByName() 就会将此信号和槽函数关联起来，**如同执行了下面的这样一条语句**：

```
connect(chkBoxUnder, SIGNAL(clicked (bool)),
this, SLOT (on_chkBoxUnder_clicked (bool));
```

这就是用 UI 设计器可视化设计某个组件的信号响应槽函数，而不用手工去将其关联起来的原因，都是在界面类的构造函数里调用 setupUi() 自动完成了关联。

## 18. Qt信号与槽机制详解

信号与槽（Signal & Slot）是 编程的基础，也是 Qt 的一大创新。因为有了信号与槽的编程机制，在 Qt 中处理界面各个组件的交互操作时变得更加直观和简单。
信号（Signal）就是在特定情况下被发射的事件，例如PushButton 最常见的信号就是鼠标单击时发射的 clicked() 信号，一个 ComboBox 最常见的信号是选择的列表项变化时发射的 CurrentIndexChanged() 信号。  

槽（Slot）就是对信号响应的函数。槽就是一个函数，与一般的[C++](http://c.biancheng.net/cplus/)函数是一样的，可以定义在类的任何部分（public、private 或 protected），可以具有任何参数，也可以被直接调用。槽函数与一般的函数不同的是：槽函数可以与一个信号关联，当信号被发射时，关联的槽函数被自动执行。 

 信号与槽关联是用 QObject::connect() 函数实现的，其基本格式是： 

```
QObject::connect(sender, SIGNAL(signal()), receiver, SLOT(slot()));
或
connect(sender, SIGNAL(signal()), receiver, SLOT(slot()));
```

  SIGNAL 和 SLOT 是 Qt 的宏，用于指明信号和槽，并将它们的参数转换为相应的字符串。例如， 

```
QObject::connect(btnClose, SIGNAL(clicked()), Widget, SLOT(close()));
```

 关于信号与槽的使用，有以下一些规则需要注意：

- **一个信号可以连接多个槽**，例如：

  ```
  connect(spinNum, SIGNAL(valueChanged(int)), this, SLOT(addFun(int));
  connect(spinNum, SIGNAL(valueChanged(int)), this, SLOT(updateStatus(int));
  ```

  这是当一个对象 spinNum 的数值发生变化时，所在窗体有两个槽进行响应，一个 addFun()用于计算，一个 updateStatus() 用于更新状态。

   当一个信号与多个槽函数关联时，槽函数按照建立连接时的顺序依次执行。 

   当信号和槽函数带有参数时，在 **connect()函数里，要写明参数的类型**，但可以不写参数名称。 

-  **多个信号可以连接同一个槽**， 

-  一个信号可以连接另外一个信号，例如： 

  ```
  connect(spinNum, SIGNAL(valueChanged(int)), this, SIGNAL (refreshInfo(int));
  ```

   这样，当一个信号发射时，也会发射另外一个信号，实现某些特殊的功能 

-  严格的情况下，信号与槽的参数个数和类型需要一致，至少**信号的参数不能少于槽的参数**。如果不匹配，会出现编译错误或运行错误。 

-  在使用信号与槽的类中，必须**在类的定义中加入宏 Q_OBJECT** 。

- **单线程顺序执行**： 当一个信号被发射时，与其关联的槽函数通常被立即执行，就像正常调用一个函数一样。只有当信号关联的所有槽函数执行完毕后，才会执行发射信号处后面的代码。 

## 19. Qt纯代码设计UI实例分析

## 20. Qt Creator使用技巧

## 21. Qt元对象和属性系统详解

### Qt 的元对象系统

Qt 的元对象系统（Meta-Object System）提供了对象之间通信的信号与槽机制、运行时类型信息和动态属性系统。 

元对象系统由以下三个基础组成：

1. **QObject 类**是所有使用**元对象系统的类的基类**。
2. 在一个类的 private 部分声明 **Q_OBJECT宏**，使得类可以使用元对象的特性，如动态属性、信号与槽。
3. **MOC（元对象编译器）**为每个 QObject 的子类提供必要的代码来实现元对象系统的特性。

 构建项目时，**MOC 工具**读取 C++ 源文件，当它发现类的定义里**有 Q_OBJECT 宏时**，它就会为这个类生成另外一个**包含有元对象支持代码的 C++ 源文件**，这个生成的源文件连**同类的实现文件一起被编译和连接**。 

 元对象还提供如下一些功能： 

-  信号与槽机制 

-  QObject::metaObject() 函数返回类关联的元对象 

-  元对象类 QMetaObject 包含了访问元对象的一些接口函数 

   元对象类 QMetaObject 包含了访问元对象的一些接口函数 

  ```
  QObject *obj = new QPushButton;
  obj->metaObject()->className (); //返回"QPushButton"
  ```

- QMetaObject::newInstance() 函数创建类的一个新的实例 

- QObject::inherits(const char *className) 函数判断一个对象实例是否是名称为 className 的类或 QObject 的子类的实例。例如： 

  ```
  QTimer *timer = new QTimer; // QTimer 是 QObject 的子类
  timer->inherits ("QTimer"); // 返回 true
  timer->inherits ("QObject");  // 返回 true
  timer->inherits ("QAbstractButton");//返回 false,不是 QAbstractButton 的子类
  ```

-  QObject::tr() 和 QObject::trUtf8() 函数可翻译字符串，用于多语言界面设计，后续章会专门介绍多语言界面设计。 

-  QObject::setProperty() 和 QObject::property() 函数用于通过属性名称动态设置和获取属性值 

对于 QObject 及其子类，还可以使用 qobject_cast() 函数进行**动态投射（dynamic cast）**。  例如，假设 QMyWidget 是 QWidget 的子类并且在类定义中声明了 Q_OBJECT 宏。创建实例使用下面的语句： 

```c++
QObject *obj = new QMyWidget;
```

 变量 obj 定义为 QObject 指针，但它实际指向 QMyWidget 类，所以可以正确投射为 QWidget，即： 

```
QWidget *widget = qobject_cast<QWidget *>(obj);
```

 从 QObject 到 QWidget 的投射是成功的，因为 obj 实际是 QMyWidget 类，是 QWidget 的子类。也可以将其成功投射为 QMyWidget，即： 

```
QMyWidget *myWidget = qobject_cast<QMyWidget *>(obj);
```

 **使用动态投射，使得程序可以在运行时对不同的对象做不同的处理**。 

### 属性系统

#### 属性定义

 Qt 提供一个 **Q_PROPERTY() 宏**可以定义属性，它也是基于元对象系统实现的。Qt 的属性系统与 C++ 编译器无关，可以用任何标准的 C++ 编译器编译定义了属性的 Qt C++ 程序。 

 在 QObject 的子类中，用宏 Q_PROPERTY() 定义属性，其使用格式如下： 

```
Q_PROPERTY(type name (READ getFunction [WRITE setFunction] | MEMBER meznberName [(READ getFunction | WRITE setFunction)])
    [RESET resetFunction]
    [NOTIFY notifySignal]
    [REVISION int]
    [DESIGNABLE bool]
    [SCRIPTABLE bool]
    [STORED bool]
    [USER bool]
    [CONSTANT]
    [FINAL])
```

 Q_PROPERTY 宏定义一个**返回值类型为 type**，**名称为 name** 的属性，用 **READ、WRITE 关键字定义属性的读取、写入函数**，还有其他的一些关键字定义属性的一些操作特性。属性的类型可以是 QVariant 支持的任何类型，也可以用户自定义类型。 

Q_PROPERTY 宏定义属性的一些主要关键字的意义如下：

- **READ** 指定一个**读取属性值的函数**，**没有 MEMBER 关键字时必须设置 READ**。
- **WRITE** 指定一个**设定属性值的函数**，**只读属性没有 WRITE 设置**。
- **MEMBER 指定一个成员变量与属性关联**，成为可读可写的属性，无需再设置 READ 和 WRITE。
- RESET 是可选的，用于指定一个**设置属性缺省值的函数**。
- NOTIFY 是可选的，用于**设置一个信号**，当**属性值变化时发射此信号**。
- DESIGNABLE 表示**属性是否在 Qt Designer 里可见**，缺省为 true。
- CONSTANT 表示**属性值是一个常数**，对于一个对象实例，READ 指定的函数返回值是常数，但是每个实例的返回值可以不一样。具有 CONSTANT 关键字的属性不能有 WRITE 和 NOTIFY 关键字。
- FINAL 表示**所定义的属性不能被子类重载**。

 一些例子如下： 

```
Q_PROPERTY(bool focus READ hasFocus)
Q_PROPERTY(bool enabled READ isEnabled WRITE setEnabled)
Q_PROPERTY(QCursor cursor READ cursor WRITE setCursor RESET unsetCursor)
```

#### 属性的使用

 不管是否用 READ 和 WRITE 定义了接口函数，只要知道属性名称，就可以通过 **QObject::property()** 读取属性值，并通过 **QObject::setProperty()** 设置属性值。例如： 

```
QPushButton *button = new QPushButton;
QObject *object = button；
object->setProperty("flat", true);
bool isFlat= object->property ("flat");
```

#### 动态属性

 QObject::setProperty() 函数可以在运行时为类定义一个新的属性，称之为**动态属性**。**动态属性是针对类的实例定义的**。 

例如，在数据表编辑界面上，一些字段是必填字段，就可以在初始化界面时为这些字段的关联显示组件定义一个新的 required 属性，并设置值为“true”，如：

```
editName->setProperty("required", "true");
comboSex->setProperty("required", "true");
checkAgree->setProperty("required", "true");
```

 然后，可以应用下面的**样式定义???**将这种必填字段的背景颜色设置为亮绿色。 

```
*[required="true"]{background-color:lime}
```

#### 类的附加信息

 属性系统还有一个**宏 Q_CLASSINFO()**，可以为**类的元对象**定义“名称——值”信息，如： 

```
class QMyClass:public QObject {
    Q_OBJECT
    Q_CLASSINFO("author", "Wang")
    Q_CLASSINFO ("company", "UPC")
    Q_CLASSINFO("version ", "3.0.1")
  public:
    ...
};
```

 用 Q_CLASSINFO() 宏定义附加类信息后，可以通过元对象的一些函数获取类的附加信息，如 **classlnfo(int) 获取**某个附加信息，函数原型定义如下： 

```
QMetaClassInfo QMetaObject::classInfo(int index) const
```

 返回值是 QMetaClassInfo 类型，有 name() 和 value() 两个函数，可获得类附加信息的名称和值。 

## 22. Qt全局变量、函数和宏定义详解

 <QtGlobal> 头文件包含了 Qt 类库的一些全局定义，包括**基本数据类型**、**函数**和**宏**，一般的 Qt 类的头文件都会包含该文件，所以**不用显式包含这个头文件**也可以使用其中的定义。 

### 全局变量定义

| Qt 数据类型 | 等效定义               | 字节数 |
| ----------- | ---------------------- | ------ |
| qint8       | signed char            | 1      |
| qint16      | signed short           | 2      |
| qint32      | signed int             | 4      |
| qint64      | long long int          | 8      |
| qlonglong   | long long int          | 8      |
| quint8      | unsigned char          | 1      |
| quint16     | unsigned short         | 2      |
| quint32     | unsigned int           | 4      |
| quint64     | unsigned long long int | 8      |
| qulonglong  | unsigned long long int | 8      |
| uchar       | unsigned char          | 1      |
| ushort      | unsigned short         | 2      |
| uint        | unsigned int           | 4      |
| ulong       | unsigned long          | 8      |
| qreal       | double                 | 8      |
| qfloat16    |                        | 2      |

其中 qreal 缺省是 8 字节 double 类型浮点数，如果 Qt 使用 选项进行配置，就是 4 字节 float 类型的浮点数。

### 全局函数定义

头文件包含一些常用函数的定义，这些函数**多以模板类型作为参数**，**返回相应的模板类型**，模板类型可以用任何其他类型替换。

| 函数                                                       | 功能                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| T qAbs(const T &value)                                     | 返回变量 value 的**绝对值**                                  |
| const T &qBound(const T &min, const T&value, const T &max) | 返回 value **限定在 min 至 max 范围之内的値**                |
| bool qFuzzyComparc(doublc p1, double p2)                   | 若 p1 和 p2 **近似相等**，返回 true                          |
| bool qFuzzyIsNulI(double d)                                | 如果参数 d **约等于 0**，返回 true                           |
| double qInf(()                                             | 返回**无穷大的数**                                           |
| bool qIsFinite(double d)                                   | 若 d 是一个**有限的数**，返回 true                           |
| bool qIsInf(double d)                                      | 若 d 是一个**无限大的数**，返回 true                         |
| bool qIsNaN(double d)                                      | 若 d **不是一个数**，返回 true                               |
| constT&qMax(const T&value1, const T&value2)                | 返回 value1 和 value2 中**较大的值**                         |
| const T &qMin(const T&value1, const T&value2)              | 返回 value1 和 value2 中**较小的值**                         |
| qint64 qRound64(double value)                              | 将 value **近似为最接近的 qint64 整数**                      |
| int qRound(double value)                                   | 将 value **近似为最接近的 int 整数**                         |
| int qrand()                                                | 标准 [C++](http://c.biancheng.net/cplus/) 中 **rand() 函数的线程安全型版本，返回 0 至 RAND_MAX 之间的伪随机数** |
| void qsrand(uint seed)                                     | 标准 C++ 中 **srand() 函数的线程安全型版本，使用种子 seed 对伪随机数字序列初始化** |

还有一些基础的**数学运算函数在 <QtMath> 头文件中定义**，比如三角运算函数、弧度与角度之间的转换函数等。

### 全局宏定义

 <QtGlobal>中文件中定义了很多宏，以下一些是比较常用的： 

-  QT_VERSION：这个宏展开为数值形式 0xMMNNPP (MM = major, NN = minor, PP = patch) 表示 Qt 编译器版本，例如 Qt 编译器版本为 Qt 5.9.1，则 QT_VERSION 为 0x050901。 

  ```
  #if QT_VERSION >= 0x040100
    QIcon icon = style()->standardIcon(QStyle::SP_TrashIcon);
  #else
    QPixmap pixmap = style()->standardPixmap(QStyle::SP_TrashIcon);
    Qlcon icon(pixmap);
  #endif
  ```

-  QT_VERSION_CHECK：这个宏展开为 Qt 版本号的一个整数表示，例如： 

  ```
  #if (QT_VERSION >= QT_VERSION_CHECK(5, 0, 0))
  #include <QtWidgets>
  #else
  #include <QtGui>
  #endif
  ```

-  QT_VERSION_STR：这个宏展开为 Qt 版本号的字符串，如“5.9.0”。 

-  Q_BYTE_ORDER、Q_BIG_ENDIAN 和 Q_LITTLE_ENDIAN：Q_BYTE_ORDER 表示系统内存中数据的字节序，Q_BIG_ENDIAN 表示大端字节序，Q_LITTLE_ ENDIAN 表示小端字节序。在需要判断系统字节序时会用到，例如： 

  ```
  #if Q_BYTE_ORDER == Q_LITTLE_ENDIAN
    ...
  #endif
  ```

- Q_DECL_IMPORT 和 Q_DECL_EXPORT：在使用或设计共享库时，用于导入或导出库的内容，后续章节有其使用实例。

- Q_DECL_OVERRIDE：在类定义中，用于重载一个虚函数，例如在某个类中重载虚函数 paintEvem()，可以定义如下：

  ```
  void paintEvent(QPaintEvent*) Q_DECL_OVERRIDE;
  ```

   使用 Q_DECL_OVERRIDE 宏后，如果重载的虚函数没有进行任何重载操作，编译器将会报错。 

-  Q_DECL_FINAL：这个宏将一个虚函数定义为最终级别，不能再被重载，或定义一个类不能再被继承，示例如下： 

  ```
  Class QRect Q_DECL_FINAL { // QRect 不能再被继承
      // ...
  };
  ```

-  Q_UNUSED(name)：这个宏用于在函数中定义不在函数体里使用的参数，示例如下 

  ```
  void MainWindow::on_imageSaved(int id, const QString &fileName)
  {
      Q_UNUSED(id);
      LabInfo->setText ("图片保存为："+ fileName);
  }
  ```

   在这个函数里，id 参数没有使用。如果不用 QJJNUSED(id) 定义，编译器会出现参数未使用的警告。 

-  **foreach(variable, container)**：foreach 用于容器类的遍历，例如： 

  ```
  foreach (const QString &codecName, recorder->supportedAudioCodecs())
      ui->comboCodec->addItem(codecName);
  ```

-  forever：forever用于构造一个无限循环，例如： 

  ```
  forever {
      ...
  }
  ```

-  qDebug(const char * message,…）：在debugger窗体显示信息，如果编译器设置了 Qt_NO_DEBUG_OUTPUT，则不作任何输出，例如： 

  ```
  qDebug("Items in list: %d", myList.size());
  ```

类似的宏还有 qWarning、qCritical、qFatal、qInfo 等，也是用于在 debugger 窗体显示信息。 



## 23. Qt顺序容器类和关联容器类详解

Qt 提供了多个基于模板的容器类，这些容器类可以用于存储指定类型的数据项，例如常用的字符串列表类 QStringList 就是从容器类 QLiSt<QString> 继承的，实现对字符串列表的添加、存储、删除等操作。 

 Qt 的容器类比标准模板库（[STL](http://c.biancheng.net/stl/)）中的容器类更**轻巧、安全和易于使用**。这些容器类是**隐式共享**和**可重入**的，而且它们**进行了速度和存储优化**，因此可以减少可执行文件的大小。此外，它们还是**线程安全的**，也就是说它们作为只读容器时可被多个线程访问。 

 例如: 用 QList<T> 定义一个字符串列表的容器，其定义方法是： 

```c++
QList<QString> aList;
```

 这样定义了一个 QList 容器类的变量 aList，它的数据项是 QString，所以 aList 可以用于处理字符串列表，例如： 

```c++
aList.append("Monday");
aList.append("Tuesday");
aList.append("Wednesday");
QString str=aList[0];
```

 Qt 的容器类分为**顺序容器**和**关联容器**。 

### 顺序容器类

 Qt 的顺序容器类有 QList、QLinkedList、QVector、QStack 和 QQueue。 

#### QList

 QList 用于**添加、插入、替换、移动、删除**数据项的函数有：insert()、replace()、removeAt()、move()、swap()、append()、prepend()、removeFirst() 和 removeLast() 等。 

 下标**索引方式**访问数据项，如同数组一样，**也提供 at() 函数**，例如： 

```c++
QList<QString> list;
list << "one" << "two" << "three";
QString str1=list[1]; //str1=="two"
QString str0=list.at(0); //str0=="one"
```

  isEmpty()  , size() 函数 

 QList 是 Qt 中最常用的容器类，很多函数的参数传递都是采用 QList 容器类，例如 QAudioDeviceInfo 的静态函数 availableDevices() 的函数原型是： 

```
QList<QAudioDeviceInfo> QAudioDeviceInfo::availableDevices(QAudio::Mode mode)
```

#### QLinkedList

QLinkedList<T> 是**链式列表**，数据项不是用连续的内存存储的，它基于迭代器访问数据项，并且插入和删除数据项的操作时间相同。
除了**不提供基于下标索引**的数据项访问外，QLinkedList 的其他接口函数与 QList 基本相同。

#### QVector

QVector<T> 提供动态数组的功能，以下标索引访问数据。
QVector 的函数接口**与 QList 几乎完全相同**，QVector<T> 的**性能比 QList<T> 更高**，因为 QVector<P> 的数据项是连续存储的。

#### QStack

QStack<T> 是提供类似于**堆栈**的后入先出（**LIFO）操作的容器类**，**push() 和 pop()** 是主要的接口函数。例如：

```c++
QStack<int> stack;
stack.push(10);
stack.push(20);
stack.push(30);
while (!stack.isEmpty())
    cout << stack.pop() << endl;

//输出：30，20，10
```

#### QQueue

QQueue<T> 是提供类似于**队列**先入先出**（FIFO）操作**的容器类。**enqueue() 和 dequeue()** 是主要操作函数。例如：

```
QQueue<int> queue;
queue.enqueue (10);
queue.enqueue(20);
queue.enqueue (30);
while (!queue.isEmpty())
    cout << queue.dequeue() << endl;
    
//输出：10，20，30
```

### 关联容器类

 关联容器类 QMap、QMultiMap、QHash、QMultiHash 和 QSet。 

 QMultiMap 和 QMultiHash 支持一个键关联多个值，QHash 和 QMultiHash 类使用**散列函数**进行查找，查找速度更快。 

#### QSet

QSet 是基于散列表的集合模板类，**它存储数据的顺序是不定的**，**查找值的速度非常快**。 QSet<T> 内部就是用 QHash 实现的。
定义 QSet<T> 容器和输入数据的实例代码如下：

```
QSet<QString> set;
set << "dog" << "cat" << "tiger";
```

 测试一个值是否包含于这个集合，用 contains() 函数，示例如下： 

```
if (!set.contains("cat"))
    ...
```

#### QMap

QMap<Key, T> 提供一个字典（关联数组)，一个键映射到一个值。**QMap 存储数据是按照键的顺序**，如果不在乎存储顺序，使用 QHash 会更快。
定义 QMap<QString，int> 类型变量和赋值的示例代码如下：

```
QMap<QString, int> map;
map["one"] = 1;
map["two"] = 2;
map["three"] = 3;
```

 也可以使用 insert() 函数赋值，或 remove() 移除一个键值对，示例如下： 

```
map.insert("four", 4);
map.remove("two");
```

 要查找一个值，使用**运算符“[]”**或 **value() 函数**，示例如下： 

```
int num1 = map["one"];
int num2 = map.value("two");
```

 如果在映射表中没有找到指定的键，会返回一个缺省构造值，例如，如果值的类型是字符串，会返回一个空的字符串。 

 在使用 value() 函数查找键值时，还可以指定一个**缺省的返回值**，示例如下： 

```
timeout = map.value("TIMEOUT",30);
```

#### QMultiMap

QMultiMap 是 QMap 的子类，是用于处理多值映射的便利类。

 **多值映射**就是一个键可以对应多个值。QMap 正常情况下不允许多值映射，除非使用 QMap::insertMulti() 添加键值对。  **QMultiMap 是 QMap 的子类**，所以 QMap 的大多数函数在 QMultiMap 都是可用的，但是有几个特殊的，**QMultiMap::insert()** 等效于 QMap::insertMulti() , **QMultiMap::replace()** 等效于 QMap::insert()。 

```
QMultiMap<QString, int> map1, map2, map3;
map1.insert("plenty", 100);
mapl.insert("plenty", 2000); // map1.size() == 2
map2.insert("plenty", 5000); // map2.size() == 1
map3 = map1 + map2; // map3.size() == 3
```

 QMultiMap 不提供“[]”操作符，使用 value() 函数访问最新插入的键的单个值。如果要获取一个键对应的所有值，使用 **values() 函数**，**返回**值是 **QList<T> 类型**。 

```
QList<int> values = map.values("plenty");
for (int i = 0; i < values.size(); ++i)
    cout << values.at(i) << endl;
```

#### QHash

 QHash 是**基于散列表来实现字典功能的模板类**，QHash<Key，T> 存储的键值对具有非常快的查找速度。 

QHash 与 QMap 的功能和用法相似，区别在于以下几点：

1. **QHash** 比 QMap 的查找速度**快**；
2. 在 QMap 上遍历时，数据项是按照键排序的，而 **QHash** 的数据项**是任意顺序**的；
3. QMap 的键必须提供“<”运算符，QHash 的键必须提供“==”运算符和一个名称为 qHash() 的全局散列函数。

#### QMultiHash

QMultiHash 是 QHash 的子类，是用于处理多值映射的便利类，其用法与 QMultiMap 类似。



## 24. Qt迭代器（Java类型和STL类型）详解

 迭代器为访问容器类里的数据项提供了统一的方法，[Qt](http://c.biancheng.net/qt/) 有两种迭代器类：[Java](http://c.biancheng.net/java/) 类型的迭代器和 [STL](http://c.biancheng.net/stl/) 类型的迭代器。两者比较，**Java 类型的迭代器更易于使用，且提供一些高级功能**，而 **STL 类型的迭代器效率更高** 。

### Java 类型迭代器

对于每个容器类，有两个 Java 类型迭代器：一个用于只读操作，一个用于读写操作，Java 类型的容器类见表 ：

| 容器类                            | 只读迭代器             | 读写迭代器                    |
| --------------------------------- | ---------------------- | ----------------------------- |
| QList<T>, QQueue<T>               | QListItcrator<T>       | QMutableListItcrator<T>       |
| QLinkedList<T>                    | QLinkedListIterator<T> | QMutableLinkedListIterator<T> |
| QVector<T>, QStack<T>             | QVectorllcrator<T>     | QMutableVectorIterator<T>     |
| QSet<T>                           | QSetItcrator<T>        | QMutableSetItcrator<T>        |
| QMap<Key, T>, QMultiMap<Key, T>   | QMapIterator<Key, T>   | QMutableMapIterator<Key, T>   |
| QHash<Key, T>, QMultiHash<Key, T> | QHashIterator<Key, T>  | QMutablcHashlterator<Key, T>  |


QMap 和 QHash 等关联容器类的迭代器用法相冋，QList 和 QLinkedList、QSet 等容器类的用法相同，所以下面只以 QMap 和 QList 为例介绍迭代器的用法。

#### 顺序容器类的迭代器的使用

 下面是遍历访问一个 QList<QString> 容器的所有数据项的典型代码： 

```c++
QList<QString> list;
list << "A" << "B" << "C" << "D";
QListIterator<QString> i (list);	// i 用于对 list 作只读遍历。
while (i.hasNext())
    qDebug () << i.next ();
```

 也可以反向遍历，示例代码如下： 

```c++
QListIterator<QString> i (list);
i.toBack();
while (i.hasPrevious())
    qDebug() << i.previous();
```

| 函数名                  | 功能                                           |
| ----------------------- | ---------------------------------------------- |
| void toFront()          | 迭代器移动到列表的最前面（第一个数据项之前)    |
| void toBack()           | 迭代器移动到列表的最后面（最后一个数据项之后） |
| bool hasNext()          | 如果迭代器不是位于列表最后位罝，返回true       |
| const T& next()         | 返回下一个数据项，并且迭代器后移一个位置       |
| const T& peekNext()     | 返回下一个数据项，但是不移动迭代器位置         |
| bool hasPrevious()      | 如果迭代器不是位于列表的最前面，返回true       |
| const T& previous()     | 返回前一个数据项，并且迭代器前移一个位置       |
| const T& peekPrevious() | 返回前一个数椐项，但是不移动迭代器指针         |

 **QListIterator 是只读访问**容器内数据项的迭代器，若要在遍历过程中**对容器的数据进行修改**， 需要使用 **QMutableListlterator**。例如下面的示例代码为删除容器中数据为奇数的项： 

```c++
QList<int> list;
list <<1<<2<<3<<4<<5;
QMutableListIterator<int> i (list);
while (i.hasNext()) {
    if (i.next() % 2 != 0)
        i.remove();
}
```

 **remove() 函数移除 next() 函数刚刚跳过的一个数据项，不会使迭代器失效。setValue() 函数可以修改刚刚跳过去的数据项的值**。 

#### 关联容器类的迭代器的使用

对于关联容器类 QMap<Key T>，使用 QMapIterator 和 QMutableMapIterator 迭代器类，它们具有表 3 所示的所有函数，主要是增加了 key() 和 value() 函数用于获取刚刚跳过的数据项的键和值。

下面的代码将删除键（城市名称）里以“City”结尾的数据项：

```c++
QMap<QString, QString> map;
map.insert("Paris", "France");
map.insert("New York", "USA");
map.insert("Mexico City", "USA");
map.insert("Moscow", "Russia");
...
QMutableMapIterator<QString, QString> i(map);
while (i.hasNext ()) {
    if (i.next().key().endsWith("City"))
        i.remove();
}
```

 如果是在多值容器里遍历，可以用 **findNext() 或 findPrevious()** 查找下一个或上一个值，如下面的代码将删除上一示例代码中 map 里值为“USA”的所有数据项： 

```c++
QMutableMapIterator<QString, QString> i(map);、
while (i.findNext("USA"))
    i.remove();
```

### STL类型迭代器

STL 迭代器与 Qt 和 STL 的原生算法兼容，并且进行了速度优化。

| 容器类                           | 只读迭代器                          | 读写迭代器               |
| -------------------------------- | ----------------------------------- | ------------------------ |
| QList<T>, QQueue<T>              | QList<T>::const iterator            | QList<T>::iterator       |
| QLinkedList<T>                   | Q1. i nked List<1>: :const_iterator | QLinkedList<T>::iterator |
| QVector<T>, QStack<T>            | QVector<T>::const_ilerator          | QVector<T>::iterator     |
| QSet<T>                          | QSet<T>::const_iterator             | QSet<T>::iterator        |
| QMap<Key, P> QMultiMap<Kcy, T>   | QMap<Key, T>::const_iterator        | QMap<Key, T>:: iterator  |
| QHash<Key, T> QMultiHash<Key, T> | QHash<Key, T>: :const_iterator      | QHash<Key, T>::iterator  |


对于每一个容器类，都有两个 STL 类型迭代器：一个用于只读访问，一个用于读写访问。无需修改数据时一定使用只读迭代器，因为它们速度更快。

 在定义只读迭代器和读写迭代器时的区别，它们使用了不同的关健字，**const_iterator** 定义**只读迭代器**，**iterator** 定义**读写迭代器**。此外，还可以使用 **const_reverse_iterator** 和 **reverse_iterator** 定义相应的**反向迭代器**。 

#### 顺序容器类的迭代器的用法

下面的示例代码将 QList<QString> list 里的数据项逐项输出：

```c++
QList<QString> list;
list << "A" << "B" << "C" << "D";
QList<QString>::const_iterator i;
for (i = list.constBegin(); i != list.constEnd(); ++i)
    qDebug() << *i;
```

 反向读写迭代器，并将上面示例代码中 list 的数据项都改为小写，代码如下： 

```
QList<QString>::reverse_iterator i;
for (i = list.rbegin(); i != list.rend(); ++i)
    *i = i->toLower();
}
```

#### 关联容器类的迭代器的用法

对于关联容器类 QMap 和 QHash，迭代器的操作符返回数据项的值。如果想返回键，使用 key() 函数。对应的，用 value() 函数返回一个项的值。

```
QMap<int, int> map;
...
QMap<int, int>::const_iterator i;
for (i = map.constBegin(); i != map.constEnd(); ++i)
    qDebug () << i.key () << ':' << i.value ();
```

 Qt API 包含很多返回值为 QList 或 QStringList 的函数，要遍历这些返回的容器，必须先复制。由于 Qt 使用了**隐式共享**，这样的复制并无多大开销。 

> qDebug() 用于在控制台输出调试信息 . **类似c++的cout函数** 

####  隐式共享

是对象的管理方法。一个对象被隐式共享，只是传递该对象的一个指针给使用者，而不实际复制对象数据，只有在使用者修改数据时，才实质复制共享对象给使用者。如在上面的代码中，splitter->sizes() 返回的是一个 QList<int>M 表对象 sizes，但是实际上代码并不将 splitter->sizes() 表示的列表内容完全复制给变量 sizes，只是传递给它一个指针，只有当 sizes 发生数据修改时，才会将共享对象的数据复制给 sizes，这样避免了不必要的复制，减少了资源占用。 

正确代码

```
const QList<int> sizes = splitter->sizes();
QList<int>::const_iterator i;
for (i = sizes.begin (); i != sizes.end(); ++i)
    ...
```

错误代码

```
QList<int>::const_iterator i;
for (i = splitter->sizes().begin(); i != splitter->sizes().end(); ++i)
```



## 25. Qt foreach关键字用法（无师自通）

 [Qt](http://c.biancheng.net/qt/) 提供一个关键字 foreach (实际是 <QtGlobal> 里定义的一个宏）用于方便地访问容器里所有数据项。

foreach 关键字用于遍历容路中所有的项，使用 foreach 的句法是： 

```
foreach (variable, container)
```

例如：

```
QLinkedList<QString> list;
...
QString str;
foreach (str, list)
    qDebug() << str;
```

 用于迭代的变量也可以在 foreach 语句里定义，foreach 语句也可以使用花括号，可以使用 break 退出迭代，示例代码如下： 

```
QLinkedList<QString> list;
...
foreach (const QString &str, list) {
    if (str.isEmpty())
        break;
    qDebug() << str;
}
```

 对于 QMap 和 QHash，foreach 会自动访问“键-值”对里的值，所以无需调用 values()。如果需要访问键则可以调用 keys()，示例代码如下： 

```
 QMap<QString, int> map;
...
foreach (const QString &str, map.keys())
    qDebug() << str << ':' << map.value(str);
```

 对于多值映射，可以使用两重 foreach 语句，示例代码如下: 

```
QMultiMap<QString, int> map;
...
foreach (const QString &str, map.uniqueKeys()) {
    foreach (int i, map.values(str))
        qDebug() << str << ':' << i;
}
```

 注意，foreach 关徤字遍历一个容器变量是创建了容器的一个副本，所以不能修改原来容器变量的数据项。 



## 26. Qt类库模块划分详解

[Qt](http://c.biancheng.net/qt/) 类库里大量的类根据功能分为各种模块，这些模块又分为以下几大类：

- Qt 基本模块（Qt Essentials)：提供了 Qt 在所有平台上的基本功能。
- Qt 附加模块（Qt Add-Ons)：实现一些特定功能的提供附加价值的模块。
- 增值模块（Value-AddModules)：单独发布的提供额外价值的模块或工具。
- 技术预览模块（Technology Preview Modules）：一些处于开发阶段，但是可以作为技术预览使用的模块。

> 提示：Qt官网的“All Modules”页面可以查看所有这些模块的信息。

#### Qt基本模块

| 模块                  | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| Qt Core               | 其他模块都用到的核心非图形类                                 |
| Qt GUI                | 设计 GUI 界面的基础类，包括 OpenGL                           |
| Qt Multimedia         | 音频、视频、摄像头和广播功能的类                             |
| Qt Multimedia Widgets | 实现多媒体功能的界面组件类                                   |
| Qt Network            | 使网络编程更简单和轻便的类                                   |
| Qt QML                | 用于 QML 和 [JavaScript](http://c.biancheng.net/js/) 语言的类 |
| Qt Quick              | 用于构建具有定制用户界面的动态应用程序的声明框架             |
| Qt Quick Controls     | 创建桌面样式用户界面，基于 Qt Quick 的用户界面控件           |
| Qt Quick Dialogs      | 用于 Qt Quick 的系统对话框类型                               |
| Qt Quick Layouts      | 用于 Qt Quick 2 界面元素的布局项                             |
| Qt SQL                | 使用 SQL 用于数据库操作的类                                  |
| Qt Test               | 用于应用程序和库进行单元测试的类                             |
| Qt Widgets            | 用于构建 GUI 界面的 [C++](http://c.biancheng.net/cplus/) 图形组件类 |

 Qt Core 模块是 Qt 类库的核心，所有其他模块都依赖于此模块，如果使用 qmake 构建项目， 则 Qt Core 模块是自动被加入项目的。

Qt GUI 模块提供了用于开发 GUI 应用程序的必要的类，使用 qmake 构建应用程序时，Qt GUI 模块是自动被加入项目的。如果项目中不使用 GUI 功能，则需要在项目配置文件中加入如下的一行： 

```
QT -= gui
```

 其他的模块一般不会被自动加入到项目，如果需要在项目中使用某个模块，则可以在项目配置中添加此模块。例如，如果需要在项目中使用 Qt Multimedia 和 Qt Multimedia Widgets 模块，需要在项目配置文件中加入如下的语句： 

```
QT += multimedia multimediawidgets
```

 需要在项目中使用 Qt SQL 模块，就在项目配置文件中加入如下的语句： 

```
QT += sql
```

#### Qt附加模块

 Qt 附加模块可以实现一些特定目的。这些模块可能只在某些开发平台上有，或只能用于某些操作系统，或只是为了向后兼容。用户安装时可以选择性地安装这些附加模块。 

| 模块                                                 | 描述                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| Active Qt                                            | 用于开发使用 ActiveX 和 COM 的 Windows 应用程序              |
| Qt 3D                                                | 支持 2D 和 3D 渲染，提供用于开发近实时仿真系统的功能         |
| Qt [Android](http://c.biancheng.net/android/) Extras | 提供 Android 平台相关的 API                                  |
| Qt Bluetooth                                         | 提供访问蓝牙硬件的功能                                       |
| Qt Concurrent                                        | 提供一些类，无需使用底层的线程控制就可以编写多线程程序       |
| Qt D-Bus                                             | 使进程间通过 D-Bus 协议通信的一些类                          |
| Qt Gamepad                                           | 使 Qt 应用程序支持游戏手柄硬件的使用                         |
| Qt Image Formats                                     | 支持附加图片格式的插件，包括 TIFF、MNG、TGA、WBMP            |
| Qt Mac Extras                                        | 提供 macOS 平台相关的 API                                    |
| Qt NFC                                               | 提供访问 NFC (近场通信）硬件的功能                           |
| Qt Positioning                                       | 提供一些类，用于通过 GPS 卫星、WiFi 等定位                   |
| Qt Print Support                                     | 提供一些用于打印控制的类                                     |
| Qt Purchasing                                        | 提供一些类，在 Qt 应用程序内实现应用内购买的功能             |
| Qt Sensors                                           | 提供访问传感器硬件的功能，以识别运动和手势                   |
| Qt Serial Bus                                        | 访问串行工业总线的功能，目前只支持 CAN 和 Modbus 协议        |
| Qt SVG                                               | 提供显示 SVG 图片文件的类                                    |
| Qt WebChannd                                         | 用于实现服务器端（QML 或 C++ 应用程序）与客户端（HTML/[Java](http://c.biancheng.net/java/)Script 或 QML 应用程序）之间的 P2P 通信 |
| Qt WebEngine                                         | 提供类和函数，实现在应用程序中嵌入网页内容                   |
| Qt WebSocket                                         | 提供兼容于 RFC 6455 的 WebSocket 通信，是实现客户端程序与远端主机进行双向通信的基于 Web 的协议 |
| Qt Windows Extras                                    | 提供 Windows 平台相关的 API                                  |
| Qt XML                                               | **该模块不再维护了**，应使用 QtCore 中的 QXmlStreamReader 和 QXmlStream Writer Qt XML Patterns 提供对 XPath、XQuery、XSLT 和 XML 等的支持 |
| Qt Charts                                            | 用于数据显示的二维图表组件                                   |
| Qt Data Visualization                                | 用于 3D 数据可视化显示的界面组件                             |
| Qt Virtual Keyboard                                  | 实现不同输入法的虚拟键盘框架                                 |

#### 增值模块

除了随 Qt 5 发布的上述这些模块，还有一些模块（见表 3)是单独发布的，这些模块只在商业版许可的 Qt 里才有。

| 特性                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| Qt for Device Creation | 高效、易用、全集成的**嵌入式设备应用程序开发工具**，包括很多其他增值特性 |
| Qt Quick Compiler      | 编译.qml 源文件生成二进制应用程序的**编译器**，提高载入时间和代码的安全性 |

#### 技术预览模块

技术预览模块就是一些还处于开发和测试阶段的模块，一般技术预览模块经过几个版本的发布后会变成正式的模块。表 4 是 Qt 5.9 中的技术预览模块。

| 模块                     | 描述                                              |
| ------------------------ | ------------------------------------------------- |
| Qt Network Authorization | 基于 OAuth 协议，为应用程序提供网络账号验证的功能 |
| Qt Speech                | 提供文字转语音（text-to-speech）功能支持          |
| Qt Remote Objects        | 进程间或设备间通信，共享 QObject 的 API           |

#### Qt 工具

Qt 工具（见表 5)在所有支持的平台上都可以使用，用于帮助应用程序的开发和设计。

| 工具        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| Qt Designer | 用于扩展 Qt Designer 的类                                    |
| Qt Help     | 在应用程序中集成在线文档的类，实现类似于 Qt Assistant 的功能 |
| QtUI Tools  | 操作 Qt Designer 生成的窗体的类                              |

## 27. Qt字符串与数字之间的转换方法详解

 QString 类从字符串转换为整数的函数有： 

```
int toInt(bool * ok = Q_NULLPTR, int base = 10) const
long toLong (bool * ok = Q_NULLPTR, int base = 10) const
short toShort (bool * ok = Q_NULLPTR, int base = 10) const
uint toUInt (bool *ok = Q_NULLPTR, int base = 10) const
ulong toULong (bool *ok = Q_NULLPTR, int base = 10) const
```

 QString 将字符串转换为浮点数的函数有： 

```
double toDouble(bool *ok = Q_NULLPTR) const
float toFloat (bool * ok = Q_NULLPTR) const
```

 在得到单价和数量后做相乘运算，得到计算结果变量 total，再将此数值显示在编辑框 editTotal 中。由于计算结果是浮点数，希望显示两位小数，下面 4 行语句都可以实现这个功能： 

```c++
str=QString::number(total,'f',2);
str=QString::asprintf ("%.2f", total);
str=str.setNum(total,'f',2);
str=str.sprintf ("%.2f，total);
```

可以使用 QString 的**静态函数 number() 和 asprintf()**，也可以使用其**公共函数 setNum() 和 sprintf()**。 QString 的 sprintf() 函数与 C 语言里的 sprintf() 函数的格式是一样的，而 setNum() 和 number() 函数使用另外一种格式定义，而且 setNum 和 number 有多个重载函数定义，可以处理各种类型的整数和浮点数 

 在处理整数时还可以指定进制，例如将一个整数直接转换为十六进制或二进制字符串。 

 以下是读取十进制数转换为二进制和十六进制字符串的按钮的槽函数代码： 

```c++
void Widget::on_btnDec_clicked()
{   //读取十进制数，转换为其他进制
    QString str=ui->editDec->text();
    int val=str.toInt;//缺省为十进制
    // str=QString::number(val, 16);//转换为十六进制的字符串
    str=str.setNum (val, 16); //十六进制
    str=str.toUpper();
    ui->editHex->setText(str);
    str=str.setNum (val, 2) ; //二进制
    // str=QString::number(val,2);
    ui->editBin->setText(str);
}
```

 将一个整数转换为不同进制的字符串，可以使用 QString 的函数 setNum() 或静态函数 number()，它们的函数原型是： 

```
Qstring &setNum (int n, int base = 10)
QString number (int n, int base = 10)
```

 其中 n 是待转换的整数，base 是使用的进制，缺省为十进制，也可以指定为十六进制和二进制。下面是读取二进制字符串，然后转换为十进制和十六进制显示的按钮的槽函数代码。 

```c++
void Widget::on_btnBin_clicked()
{   //读取二进制数，转换为其他进制的数
    QString str=ui->editBin->text () ; //读取二进制字符串
    bool ok;
    int val=str.tolnt (&ok, 2) ; //以二进制数读入
    str=QString::number (val, 10) ;//数字显示为十进制字符串
    ui->editDec->setText(str);
    str=str.setNum (val, 16) ; //显示为十六进制
    str=str.toUpper();
    ui->editHex->setText(str);
}
```

## 28. Qt QString类及常用函数功能详解

 QString 存储字符串釆用的是 Unicode 码，**每一个字符是一个 16 位的 QChar**，而不是 8 位的 char，所以 QString 处理中文字符没有问题，而且**一个汉字算作是一个字符**。 

#### append() 和 prepend()

append() 在字符串的后面添加字符串，prepend() 在字符串的前面添加字符串

#### toUpper() 和 toLower()

toUpper() 将字符串内的字母全部转换为大写形式，toLower() 将字母全部转换为小写形式

#### count()、size() 和 length()

count()、size() 和 length() 都返回字符串的字符个数，这 3 个函数是相同的，但是要注意，字符串中如果有汉字，**一个汉字算一个字符**.

#### trimmed() 和 simplified()

trimmed() 去掉字符串首尾的空格，simplified() 不仅去掉首尾的空格，中间连续的空格也用一个空格替换。

#### indexOf () 和 lastIndexOf ()

indexOf() 函数的原型为：

```
int indexOf (const QString &str, int from = 0 , Qt::CaseSensitivity cs = Qt::CaseSensitive) const
```

 其功能是在自身字符串内查找参数字符串 str 出现的位置，参数 from 是幵始查找的位置，Qt::CaseSensitivity cs 参数指定是否区分大小写。 

 lastIndexOf() 函数则是查找某个字符串最后出现的位置。 

```
QString str1="G:\Qt5Book\QT5.9Study\qw.cpp";
N=str1.indexOf("5.9");  // N=13
N=str1.lastIndexOf("\\"); //N=21
```

 提示，"\" 是转义字符，如果要查找 "\"，需要输入 "\\"。 

#### isNull() 和 isEmpty()

两个函数都判读字符串是否为空，但是稍有差别。如果一个空字符串，只有“\0”，isNull() 返回 false，而 isEmpty() 返回 true；只有未赋值的字符串，isNull() 才返回 true。

QString 只要赋值，就在字符串的末尾自动加上 "\0"，所以，如果只是要判断字符串内容是否为空，常用 isEmpty()。

#### contains()

判断字符串内是否包含某个字符串，可指定是否区分大小写。

```
QString str1="G:\Qt5Book\QT5.9Study\qw.cpp";
N=str1.contains (".cpp", Qt::CaseInsensitive) ; // N=true,不区分大小写
N=str1.contains (".CPP", Qt::CaseSensitive) ;  // N=false,区分大小写
```

#### endsWith() 和 startsWith()

startsWith() 判断是否以某个字符串幵头，endsWith() 判断是否以某个字符串结束。

#### left() 和 right()

left 表示从字符串中取左边多少个字符，right 表示从字符串中取右边多少个字符。注意，一个汉字被当作一个字符。

#### section()

section() 函数的原型为：

```
QString section (const QString &sep, int start, int end = -1, SectionFlags flags = SectionDefault) const
```

 其功能是从字符串中提取以 sep 作为分隔符，从 start 端到 end 端的字符串。 

```c++
QString str2, str1="学生姓名,男,1984-3-4,汉族,山东";
str2=str1.section (",",0,0); // str2="学生姓名"， 第 1 段的编号为 0
str2=str1.section (",",1,1}; // str2="男"
str2=str1.section (",",0,1}; // str2="学生姓名，男"
str2=str1.section (",",4,4); // str2="山东"
```

## 29. Qt QSpinBox使用详解

 **QSpinBox 用于整数的显示和输入**，一般显示十进制数，也可以显示二进制、十六进制的数，而且可以在显示框中增加前缀或后缀。

**QDoubleSpinBox 用于浮点数的显示和输入**，可以设置显示小数位数，也可以设置显示的前缀和后缀

| 属性名称           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| prefix             | 数字显示的前缀，例如“$”                                      |
| suffix             | 数字显示的后缀，例如“kg”                                     |
| minimum            | 数值范围的最小值，如 0                                       |
| maximum            | 数值范围的最大值，如 255                                     |
| singlestep         | 单击右侧上下调整按钮时的单步改变值，如设置为 1，或 0.1       |
| value              | 当前显示的值                                                 |
| displaylntegerBase | QSpinBox 特有属性，显示整数使用的进制，例如 2 就表示二进制   |
| decimals           | QDoubleSpinBox 特有属性，显示数值的小数位数，例如 2 就显示两位小数 |

提示一个属性在类的接口中一般有一个读取函数和一个设置函数，如 QDoubleSpinBox 的 decimals 属性，读取属性值的函数为 int decimals()，设置属性值的函数为 void setDecimals(int prec)。

## 30. Qt数值输入组件和显示组件用法详解

 ![img](http://c.biancheng.net/uploads/allimg/190102/2-1Z102154221a2.gif) 

在这个实例中，用到如下一些组件：

- QSlider：**滑动条**，通过滑动来设置数值，可用于数值输入。实例中使用 4 个滑动条输入红、绿、蓝三色和 Alpha 值，然后合成颜色，作为一个 QTextEdit 组件的底色。
- QScrollBar：**卷滚条**，与 QSlider 功能类似，还可以用于卷滚区域。
- QProgressBar：**进度条**，一般用于显示任务进度，可用于数值的百分比显示。实例程序中滑动一个Slider，获取其值并更新ScrollBar和ProgressBar。
- QDial：**表盘式数值输入组件**，通过转动表针获得输入值。
- QLCDNumber：**模仿 LCD 数字的显示组件**，可以显示整数或浮点数，显示整数时可以不同进制显示。实例程序中转动表盘，获得的值显示在 LCD 组件中。单击“LCD显示进制”的 RadioButton 时，设置 LCD 的显示进制。



## 31. Qt获取当前时间（超详细）

时间日期是经常遇到的数据类型，[Qt](http://c.biancheng.net/qt/)

1. QTime：时间数据类型，仅表示时间，如15:23:13。
2. QDate：日期数据类型，仅表示日期，如2017-4-5。
3. QDateTime：日期时间数据类型，表示日期和时间，如2017-03-23 08:12:43。


Qt 中有专门用于日期、时间编辑和显示的界面组件，介绍如下：

1. QTimeEdit：编辑和显示时间的组件。
2. QDateEdit：编辑和显示日期的组件。
3. QDateTimeEdit：编辑和显示日期时间的组件。
4. QCalendarWidget： 一个用日历形式选择日期的组件。

 ![å®ä¾ samp4_5 è¿è¡æ¶çé¢](http://c.biancheng.net/uploads/allimg/190102/2-1Z102163P2K8.gif) 

## 32. Qt定时器及实现（详解）

 定时器是用来处理周期性事件的一种对象，类似于硬件定时器。例如设置一个定时器的定时周期为 1000 毫秒，那么每 1000 毫秒就会发射定时器的 **timeout() 信号**，在信号关联的槽函数里就可以做相应的处理。 

 [Qt](http://c.biancheng.net/qt/) 中的定时器类是 QTimer。**QTimer** 不是一个可见的界面组件，在 UI 设计器的组件面板里找不到它。图 1 中的实例程序实现了一个计时器的功能，就是计算定时器开始到停止持续的时间长度，计时器是 QTime 类。

QTimer 主要的**属性是** **interval**，是定时中断的周期，单位毫秒。QTimer 主要的**信号是 timeout()**，在定时中断时发射此信号，要想在定时中断里做出响应，这就需要编写 timeout() 信号的槽函数。下面是窗口类中增加的定义（省略了其他不相关的定义)： 

```c++
class Dialog : public QDialog
{
private:
    QTimer *fTimer; //定时器
    QTime fTimeCounter;//计时器
private slots:
    void on_timer_timeout () ; //定时溢出处理槽函数
};

Dialog::Dialog(QWidget *parent) : QDialog(parent), ui(new Ui::Dialog)
{
    ui->setupUi(this);
    fTimer=new QTimer(this);
    fTimer->stop();
    fTimer->setInterval (1000) ;//设置定时周期，单位：毫秒
    connect(fTimer,SIGNAL(timeout()),this,SLOT(on_timer_timeout()));
}

void Dialog::on_timer_timeout()
{
    //定时器中断响
    QTime curTime=QTime::currentTime(); //获取当前时间
    ui->LCDHour->display (curTime.hour()); //显示小时
    ui->LCDMin->display (curTime.minute()); //显示分钟
    ui->LCDSec->display(curTime.second()); //显示秒
    int va=ui->progressBar->value();
    va++;
    if (va>100)
        va=0;
    ui->progressBar->setValue(va);
}

void Dialog::on_btnStart_clicked()
{
    fTimer->start () ;//定时器开始工作
    fTimeCounter.start () ; //计时器开始工作
    ui->btnStart->setEnabled(false);
    ui->btnStop->setEnabled(true);
    ui->btnSetIntv->setEnabled(false);
}

void Dialog::on_btnStop_clicked()
{
    fTimer->stop () ; //定时器停止
    int tmMsec=fTimeCounter.elapsed() ;//毫秒数
    int ms=tmMsec%1000;
    int sec=tmMsec/1000;
    QString str=QString::asprintf ("流逝时间：％d 秒，％d 毫秒", sec, ms);
    ui->LabElapsTime->setText(str);
    ui->btnStart->setEnabled(true);
    ui->btnStop->setEnabled(false);
    ui->btnSetIntv->setEnabled(true);
}
```

## 33. Qt QComboBox下拉框及用法（详解版）

## 34. Qt QPlainTextEdit用法详解

- QPlainTextEdit 提供 cut()、copy()、paste()、undo()、redo()、clear()、selectAll() 等标准编辑功能的槽函数，

- QPlainTextEdit 还提供一个标准的右键快捷菜单。 

- 逐行读取文字内容

  ```
  void Widget::on_btnToComboBox_clicked()
  {
      //plainTextEdit的内容逐行添加为comboBox的项
      QTextDocument* doc=ui->plainTextEdit->document () ; //文本对象
      int cnt=doc->blockCount () ;//回车符是一个 block
      QIcon icon(M:/images/icons/aim.ico");
      ui->comboBox->clear();
      for (int i=0; i<cnt;i++)
      {
          QTextBlock textLine=doc->findBlockByNumber (i) ; // 文本中的一段
          QString str=textLine.text();
          ui->comboBox->addItem(icon,str);
      }
  }
  ```

  

## 35. Qt QListWidget和QToolButton用法详解（实例分析）

 [Qt](http://c.biancheng.net/qt/) 中用于项（Item）处理的组件有两类，一类是 Item Views，包括 QListView、QTreeView、 QTableView、QColumnView 等；另一类是 Item Widgets，包括 QListWidget、QTreeWidget 和 QTable Widget。 

```
Item Views 基于模型/视图（Model/View)结构，视图（View)与模型数据（Model Data)关联实现数据的显示和编辑，模型/视图结构的使用在后续章节中详细介绍。	
```

 Item Widgets **是直接将数据存储在每一个项里**，例如，QListWidget 的每一行是一个项，QTreeWidget 的每个节点是一个项，QTableWidget 的每一个单元格是一个项。一个项存储了文字、文字的格式、自定义数据等。

Item Widgets 是 GUI 设计中常用的组件，本节通过实例 samp4_7 先介绍 QListWidget 以及其他一些组件的用法，实例运行时界面如图  

本实例不仅介绍 QListWidget 的使用，还包括如下一些功能的实现：

- 使用 QTabWidget 设计多页界面，工作区右侧就是一个具有 3 个页面的 TabWidget 组件。
- 使用 QToolBox 设计多组工具箱，工作区左侧是一个有 3 个组的 ToolBox 组件。
- 使用分隔条（QSplitter）设计可以左右分割的界面，工作区的 ToolBox 和 TabWidget 之间有一个 splitter，运行时可以分割调整两个组件的大小。
- 创建 Actions，用 Actions 设计主工具栏，用 Action 关联 QToolButton 按钮。
- 使用 QToolButton 按钮，设置与 Action 关联，设计具有下拉菜单功能的 ToolButton 按钮，在主工具栏上添加具有下拉菜单的 Tooffiutton 按钮。
- 使用 QListWidget，演示如何创建和添加项，为项设置图标和复选框，如何遍历列表进行选择。
- QListWidget 的主要信号 currentItemChanged() 的功能，编写响应槽函数。
- 为 QListWidget 组件利用己设计的 Actions 创建自定义快捷菜单。

## 38. Qt Model/View（模型/视图）结构（无师自通）

**Model/View（模型/视图）结构**是 [Qt](http://c.biancheng.net/qt/) 中用界面组件显示与编辑数据的一种结构:

- **视图（View）**是显示和编辑数据的**界面组件**
- **模型（Model）**是视图与原始数据之间的**接口**。 

将界面组件与所编辑的数据分离开来，又通过数据源的方式连接起来，是处理界面与数据的一种较好的方式。Qt 使用 Model/View 结构来处理这种关系，Model/View 的基本结构如图所示:

 ![Model/Viewåºæ¬ç»æ](http://c.biancheng.net/uploads/allimg/190103/2-1Z10316062G22.gif)  

- **数据（Data）**是实际的**数据(数据源)**，如数据库的一个数据表或SQL查询结果，内存中的一个 StringList，或磁盘文件结构等。
- 视图或**视图组件（View）**是屏幕上的**界面组件**，视图从数据模型获得每个数据项的模型索引（model index），通过模型索引获取数据，然后为界面组件提供显示数据。Qt 提供一些现成的数据视图组件，如 QListView、QTreeView 和 QTableView 等。
- 模型或**数据模型（Model）**与实际数据通信，并为视图组件提供**数据接口**。它从原始数据提取需要的内容，用于视图组件进行显示和编辑。Qt 中有一些预定义的数据模型，如 QStringListModel 可作为 StringList 的数据模型，QSqlTableModel 可以作为数据库中一个数据表的数据模型。

**代理（Delegate）功能**，可以**让用户定制数据的界面**显示和编辑方式。在标准的视图组件中，代理功能显示一个数据，当数据被编辑时，代理通过模型索引与数据模型通信，并为编辑数据提供一个编辑器，一般是一个 QLineEdit 组件。 

模型、视图和代理之间使用信号和槽通信。 

- 当源数据发生变化时，数据模型发射信号通知视图组件；
- 当用户在界面上操作数据时，视图组件发射信号表示这些操作信息；
- 当编辑数据时，代理发射信号告知数据模型和视图组件编辑器的状态。 

### 数据模型

所有的基于**项数据**的**数据模型（Model）**都是基于 **QAbstractltemModel 类**的，这个类定义了视图组件和代理**存取数据的接口**。数据无需存储在数据模型里，数据可以是其他类、文件、数据库或任何数据源。

 Qt 中与数据模型相关的几个主要的类的层次结构如图所示： 

 ![Qtä¸­æ¨¡åç±»çå±æ¬¡ç»æ](http://c.biancheng.net/uploads/allimg/190103/2-1Z103160KBJ.gif) 

| Model 类                 | 用途                                                         |
| ------------------------ | ------------------------------------------------------------ |
| QStringListModel         | 用于处理**字符串列表数据**的数据模型类                       |
| QStandardltemModel       | **标准的基于项数据**的数据模型类，每个项数据可以是任何数据类型 |
| QFileSy stemModel        | 计算机上**文件系统的数据**模型类                             |
| QSortFilterProxyModel    | **与其他数据模型结合**，提供**排序和过滤功能的数据**模型类   |
| QSqlQueryModel           | 用于**数据库SQL查询结果的数据**模型类                        |
| QSqlTableModel           | 用于**数据库的一个数据表的数据**模型类                       |
| QSqlRelationalTableModel | 用于**关系型数据表的数据**模型类                             |

数据库相关的 3 个模型类将在介绍数据库编程时专门说明。如果这些现有的模型类无法满足需求，用户可以从 QAbstractltemModel、QAbstractListModel 或 QAbstractTableModel 继承，生成自己定制的数据模型类。

### 视图组件——(调用setModel() 函数，便利lei)

视图组件（View）就是显示数据模型的数据的界面组件，Qt 提供的视图组件如下：

- QListView：用于显示单列的列表数据，适用于一维数据的操作。
- QTreeView：用于显示树状结构数据，适用于树状结构数据的操作。
- QTableView：用于显示表格状数据，适用于二维表格型数据的操作。
- QColumnView：用多个QListView显示树状层次结构，树状结构的一层用一个QListView显示。
- QHeaderView：提供行表头或列表头的视图组件，如QTableView的行表头和列表头。

视图组件在显示数据时，只需**调用视图类的 setModel() 函数**，为视图组件设置一个数据模型就可以实现视图组件与数据模型之间的关联，在视图组件上的修改将自动保存到关联的数据模型里，一个数据模型可以同时在多个视图组件里显示数据。 

QListWidget、QTreeWidget 和 QtableWidget 3个可用于数据编辑的组件。这 3 个类称为**便利类**（convenience classes），它们分别是 3 个视图类的子类，其层次关系如图所示：

 ![è§å¾ç¸å³ç±»çå±æ¬¡ç»æå¾](http://c.biancheng.net/uploads/allimg/190103/2-1Z103160934132.gif) 

**视图组件类的数据采用单独的数据模型**，视图组件不存储数据。

**便利类**则为组件的每个节点或单元格创建一个项（item），**用项存储数据、格式设置等**。

所以，便利类没有数据模型，它实际上是用项的方式集成了数据模型的功能，这样就将界面与数据绑定了。

所以，便利类缺乏对大型数据源进行灵活处理的能力，适用于小型数据的显示和编辑。  

### 代理（Delegate）

代理就是在视图组件上为编辑数据提供编辑器，如在表格组件中编辑一个单元格的数据时，缺省是使用一个 QLineEdit 编辑框。代理负责从数据模型获取相应的数据，然后显示在编辑器里，修改数据后，又将其保存到数据模型中。

QAbstractltemDelegate 是所有代理类的基类，作为抽象类，它不能直接使用。它的一个子类 **QStyledltemDelegate**，**是 Qt 的视图组件缺省使用的代理类**。 

- 如只允许输入整型数，使用一个 QSpinBox 作为代理组件更恰当，
- 从列表中选择数据时使用一个 QComboBox 作为代理组件更好。

这时，就可以从 QStyledltemDelegate 继承创建自定义代理类。 

### Model/View结构的一些概念

在 Model/View 结构中，数据模型为视图组件和代理提供存取数据的标准接口。在 Qt 中，所有的数据模型类都从 QAbstractltemModel 继承而来，不管底层的[数据结构](http://c.biancheng.net/data_structure/) 是如何组织数据的，QAbstractltemModel 的子类都以表格的层次结构表示数据，视图组件通过这种规则来存取模型中的数据，但是表现给用户的形式不一样。  

<center>
	<img src="/2-1Z1031610455L.gif"  alt="图5"/>
    <br>
	<div style="display:inline-block; padding: 2px; border-bottom: 1px solid ##d9d9d9;
                font-weight:bold;">
		图5 数据模型中的几种表现模式
	</div>
</center> 


 不管数据模型的表现形式是怎么样的，数据模型中存储数据的**基本单元都是项（item）**，每个项有**一个行号、一个列号，还有一个父项索引**。 

#### 模型索引（model index）

为了保证数据的表示与数据存取方式隔离，数据模型中引入了模型索引的概念。通过数据模型存取的每个数据都有一个模型索引，视图组件和代理都通过模型索引来获取数据。

#### 行号和列号

数据模型的基本形式是用行和列定义的表格数据。要获得一个模型索引，必须提供 3 个参数：行号、列号、父项的模型索引。例如，对于如**图 5** 中的表格数据模型中的 3 个数据项 A、B、C，获取其模型索引的代码是： 

```
QModelIndex indexA = model->index(0, 0, QModelIndex());
QModelIndex indexB = model->index(1, 1, QModelIndex());
QModelIndex indexC = model->index(2, 1, QModelIndex());
```

 在创建模型索引的函数中需要传递行号、列号和父项的模型索引。对于列表和表格模式的数据模型，顶层节点总是用 QModelIndex() 表示。 

#### 父项

对于图中的树状数据模型，节点 A 和节点 C 的父节点是顶层节点，获取模型索引的代码是：

```
QModelIndex indexA = model->index(0, 0, QModelIndex());
QModelIndex indexC = model->index(2, 1, QModelIndex());
```

 节点 B 的父节点是节点 A，节点 B 的模型索引由下面的代码生成： 

```c++
QModelIndex indexB = model->index(1, 0, indexA); // 没有列，列设为0
```

#### 项的角色

在为数据模型的一个项设置数据时，可以赋予其不同项的角色的数据。

例如，数据模型类 QStandardItemModel 的项数据类是 QStandardItem，其设置数据的函数是：

```
void QStandardItem::setData(const QVariant &value, int role= Qt::UserRole + 1)
```

其中，value 是需要设置的数据，role 是设置数据的角色。一个项可以有**不同角色**的数据，用于**不同的场合**。

role 是 `Qt::ItemDataRole` 枚举类型，有多种取值，如 `Qt::DisplayRole` 角色是在视图组件中显示的字符串，`Qt::ToolTipRole` 是鼠标提示消息，`Qt::UserRole` 可以自定义数据。项的标准角色是 `Qt::DisplayRole`。 

 在获取一个项的数据时也需要指定角色，以获取不同角色的数据： 

```
QVariant QStandardItem::data(int role = Qt::UserRole + 1) const
```

 在下图中，项的 **DisplayRole** 数据是显示的字符串，**DecorationRole** 是用于装饰显示的属性，**ToolTipRole** 定义了鼠标提示信息。 

 ![ä¸åè§è²æ°æ®çè¡¨ç°å½¢å¼](http://c.biancheng.net/uploads/allimg/190103/2-1Z103161340927.gif) 

## 39. Qt QFileSystemModel功能及用法详解

 **QFileSystemModel** 提供了一个可用于访问本机文件系统的**数据模型**。 

QFileSystemModel **和视图组件 QTreeView 结合使用**，可以用目录树的形式显示本机上的文件系统，如同 Widnows 的资源管理器一样。 

QFileSystemModel 提供的接口函数，可以创建目录、删除目录、重命名目录，可以获得文件名称、目录名称、文件大小等参数，还可以获得文件的详细信息。 

## 40. Qt QStringListModel用法详解

 QStringListModel 用于处理字符串列表的数据模型，它可以作为 QListView 的数据模型，在界面上显示和编辑字符串列表。 

 QStringListModel 的 **setStringList() 函数**可以初始化数据模型的字符串列表的内容，**stringList() 函数**返回数据模型内的字符串列表，在关联的 ListView 组件里编辑修改数据后，数据都会及时更新到数据模型内的字符串列表里。

QStringListModel 提供编辑和修改字符串列表数据的函数，如 **insertRows()、removeRows()、setData()** 等，这些操作直接影响数据模型内部的字符串列表，并且修改后的数据会自动在关联的 ListView 组件里刷新显示。

实例 samp5_2 采用 **QStringListModel 作为数据模型**，**QListView 组件作为视图组件**，演示了 QStringListModel 和 QListView 构成 Model/View 结构编辑字符串列表的功能，程序运行时界面如图 1 所示。 

 ![img](http://c.biancheng.net/uploads/allimg/190103/2-1Z1031G344937.gif) 

前面章节中，我们采用 QListWidget 设计过一个列表编辑器（samp4_7），对比这两个实例，可以发现如下两点：

- 在 Model/View 结构中，数据模型与视图组件是分离的，可以直接操作数据模型以修改数据，在视图组件中做的修改也会自动保存到数据模型里。
- 在使用 QListWidget 的例子中，每个列表项是一个 QListWidgetltem 类型的变量，保存了项的各种数据，数据和显示界面是一体的，对数据的修改操作就是对项关联的变量的修改。

所以，这是 Model/View 结构与便利组件之间的主要区别。

## 41. Qt QStandardItemModel用法（超级详细）

**QStandardItemModel** 是标准的以项数据（item data）为基础的标准数据模型类，通常与 **QTableView** 组合成 Model/View 结构，实现通用的二维数据的管理功能。 

本节介绍 QStandardltemModel 的使用，主要用到以下 3 个类：

1. QStandardItemModel：基于**项数据**的标准数据模型，**可以处理二维数据**。维护一个二维的项数据数组，**每个项是一个 QStandardltem 类的变量**，用于存储项的数据、字体格式、对齐方式等。
2. **QTableView**：**二维数据表视图组件**，有多个行和多个列，每个基本显示单元是一个单元格，通过 setModel() 函数设置一个 QStandardItemModel 类的数据模型之后，一个单元格显示 QStandardItemModel 数据模型中的一个项。
3. **QItemSelectionModel**：一个用于**跟踪视图组件的单元格选择状态**的类，当在 QTableView 选择某个单元格，或多个单元格时，通过 QItemSelectionModel 可以**获得**选中的单元格的**模型索引(model index)**，为单元格的选择操作提供方便。

这几个类之间的关系是：**QTableView 是界面视图组件**，其关联的**数据模型是 QStandardItemModel**，关联的**项选择模型是 QItemSelectionModel**，QStandardItemModel 的**数据管理的基本单元是 QStandardItem**。  

运行时界面如图 1 所示 ：

 ![img](http://c.biancheng.net/uploads/allimg/190104/2-1Z104101554448.gif) 

## 42. Qt 5种标准对话框及使用方法详解

