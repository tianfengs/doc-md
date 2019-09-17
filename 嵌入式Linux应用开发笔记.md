---
typora-root-url: pics
typora-copy-images-to: pics
---



## 第1篇

### 第2章 嵌入式开发环境构建

1. 硬件环境构建

   - 主机与目标板结合的**交叉开发模式**

     三个步骤：

     - 在主机上编译Bootloader，然后通过JTAG少如开发板

       应具备串口传输、网络传输、烧写Flash功能

     - 在主机上编译嵌入式Linux内核，通过BootLoader少如单板或直接启动

       内核应支持网络文件系统NFS

     - 在主机上编译各类应用程序，单板启动内核后通过NFS运行他们，经过验证后再少如单板

     烧写、启动Bootloader后，就可以通过它的各类命令来下载、烧写、运行程序了。

     一般通过串口来进行输入/输出。

     所以 交叉开发模式中，主机与目标板通常需要3种连接：JTAG、串口、网络

     ![1564140825554](/交叉开发模式主机与开发板连接方式.png)

     硬件要求：

     - 有一个25针的并口接口，用来接JTAG
     - 9针的RS-232串行接口
     - 支持网络
     - 至少20G硬盘

     目标板要求：

     - S3C2410或S3C2440
     
     - 64MB SDRAM
     
     - 1MB NOR Flash
     
     - 64MB NAND Flash
     
     - 两个网卡(10MB/100MB)
     
     - 5个串口（内置3个，外扩2个）

     - 音频输入输出
     
     - 2.5吋IDE接口
     
     - 标准SD/MMC卡座
     
     - 4个GPIO按键/4个GPIO按键
     
     - 外接
     $$
     I^2C
     $$
        接口的实时时钟（RTC）芯片
     
       将主机与目标板通过JTAG、串口线（接单板上的串口0）、网线（接单板上的网卡0）连接起来，将各类设备连接到目标板上去即可。

2. 软件环境构建

   - 主机linux操作系统的安装

     - 在Windows上安装虚拟机

       Ubuntu 7.10

       http://relenses.ubuntu.com/7.10

       分区：1G交换分区swap，5G根分区root，15G工作分区work（存放 本书光盘，在此分区上编辑、编译、调试软件）

     - 主机Linux操作系统上网络服务的配置与启动
     
       配置启动FTP、SSH和NFS三个服务
     
       IP假定：192.168.1.57，网关：192.168.1.2
       - 虚拟机使用网桥连接，如使用NAT，则保证主客系统不在一个网段中
     
       - 安装配置三个服务
     
         - 下载安装apt-get工具
     
           - 修改/etc/apt/sources.list，将其中注释掉的网址打开
     
           - 更新可用的程序列表
     
             ```shell
             $ sudo apt-get update
             ```
     
             它只更新内部数据库，确定哪些程序已经安装，哪些没安装
     
         - 安装、配置、启动服务
     
           用sudo命令使用root权限，例如
     
           ```shell
           # sudo vi /etc/exports
           ```
     
           - 安装ftp服务
     
             ```shell
             $ sudo apt-get install vsftpd
             ```
     
             进入/etc/vsftpd.conf，将下面两行的#去掉，允许本地登录
     
             ```shell
             #local_enable=YES	允许本地登录
             #write_enable=YES	允许上传文件
             ```
     
             重启ftp server
     
             ```shell
             $ sudo /etc/init.d/vsftpd restart
             ```
     
           - 安装ssh服务
     
             ```shell
             $ sudo apt-get install openssh-server
             默认配置文件为/etc/ssh/sshd_config
             ```
     
           - 安装NFS服务
     
             ```shell
             $ sudo apt-get install nfs-kernel-server portmap
             ```
     
             配置文件为/etc/exports，增加通过网络文件系统访问/work/nfs_root目录的能力
     
             ```shell
             /work/nfs_root *(rw,sync,no_root_squash)
             ```
     
             重启NFS服务
     
             ```shell
             $ sudo /etc/init.d/nfs-kernel-server restart
             ```
     
     - 在主机Linux中安装基本的开发环境
     
       ```shell
       安装基本的开发环境：比如c库，g++编译器等
       $ sudo apt-get install build-essential
       
       安装bison语法、flex词法分析器:
       $ sudo apt-get install bison flex
       
       安装C函数库的man手册
       $ sudo apt-get install manpages-dev
       ```
     
     - 光盘结构
     
       hardware硬件实验、system系统移植、driver and test驱动和测试程序、GUI，tools工具、scratchbox、nfs root网络系统文件、debug调试工具
     
     ![1564199976660](/1564199976660.png)
     
     ![1564200028133](/1564200028133.png)
     
      hardware目录：用make命令编译.bin文件，变成二进制程序，烧入开发板的NAND Flash中即可运行
     
     U-Boot、Linux内核、文件系统（busybox），三部分构成了基本的、最小的嵌入式Linux系统，它们的代码在system目录下
     
     本书介绍两种GUI系统：qtopia、X window。
     
     ​	X Window下四个子目录：
     
     ```shell
     -  X目录为Xorg代码，提供X server
     -  matchbox是一个基于X的、用于嵌入式系统的小型GUI环境，包括：窗口管理器、面板、桌面、共享功能程序库和一些小面板应用程序
     -  GTK下是gtk+ 库，gtk+是一个用于创建图形用户界面的多平台工具，包含很多控件
     -  apps目录，包括几个基于X、GTK+的应用程序
     ```
     
     tools目录下是运行于主机上的工具，主要是交叉编译工具，使用create_crosstools目录中的软件编译出来的交叉编译工具链：
     
     - arm-linux-gcc-3.4.5-glibc-2.3.6.tar.bz2 可以在主机上直接运行
     - scratchbox-arm-linux-gcc-3.4.5-glibc-2.3.6.tar.bz2是当主机上启动scratchbox后，在其中运行的。
     
     scratchbox是一个交叉编译工具包，他的目的是使嵌入式Linux开发变得像开发PC Linux软件一样容易。在移植X Window时将用到它。
     
     nfs_root目录下的四个子目录：最小的根文件系统fs_mini，使用mdev机制的根文件系统fs_min_mdev，含有qtopia图形程序的根文件系统fs_qtopia，含有X window图形程序的根文件系统fs_xwindow。在开发阶段，单板上的内核启动后可以通过NFS挂接它们中的某一个，然后执行其中的程序；开发完成后，将所用的整个子目录制作为映像文件，烧入开发板。

##### 安装光盘：

安装光盘前，授权当前book用户拥有写/work目录权限

```shell
$ sudo chown book:book /work -R
```

将光盘所有内容复制到/work目录中，有以下2种方法：

- 在windows下，通过ftp客户端登录Linux，然后上传文件；
- 如果运行Linux，则在VMware中挂接光盘后，linux中会自动弹出光盘目录，复制到/work即可

##### 安装交叉编译工具链：

- 约定：
  - 在主机上执行命令提示符为"$"；
  - 在主机中启动scratchbox，然后再scratchbox里执行命令，提示符为">"
  - 在单板上执行命令，提示符为"#"

- 使用制作好的工具链

  - 使用以下命令解压得到gcc-3.4.5-glibc-2.3.6目录

    ```shell
    $ cd /work/tools
    $ tar xjf arm-linux-gcc-3.4.5-glibc-2.3.6.tar.bz2
    ```

  - 在环境变量PATH中增加路径

    ```shell
    $ export PATH=$PATH:/work/tools/gcc-3.4.5-glibc-2.3.6/bin
    
    在环境设置/etc/environment中修改PATH值，使每次重启后都不需要手动设置：
    PATH="/usr/local/sbin:/usr/local/bin:/usr/bin:/sbin:/bin:/usr/games: /work/tools/gcc-3.4.5-glibc-2.3.6/bin"
    ```

  - 安装ncurses

    ncurses是一个能提供快捷键定义、屏幕绘制、基于文本终端的图形互动功能的动态库。如果没安装它，在执行"make menuconfig"命令配置程序时会出错。安装：

    ```shell
    $ cd /work/tools/
    $ tar xzf ncurses.tar.gz
    $ cd ncurses-5.6
    $ ./configure --with-shared --prefix=/use
    $ make
    $ make install
    ```

- 自己制作工具链

  自己编译工具链。

  - 如果基于gcc和glibc来制作，可以使用crosstool来进行编译；
  - 如果基于gcc和uClibc来制作，可以使用buildroot来进行编译。

  crosstool官方网站为 http://kegel.com/crosstool/ 可参看其中的crosstool-howto.html选择、配置、编译工具链。

  下面使用/work/tools/create_crosstools目录下的crosstool-0.43.tar.gz工具来编译工具链。它运行时，会自动从网上下载源码，然后编译。也可以自己下载源码，再运行crosstool。本书源码已经放在src_gcc_glibc目录下。

  - 修改crosstool脚本

    ```shell
    解压缩：
    $ tar xzf crosstool-0.43.tar.gz
    ```

    其中glibc-2.3.6-version-info.h_err.patch是补丁文件，它修改glibc-2.3.6/csu/Makefile里的一个错误——导致自动生成的version-info.h文件编译出错。将它复制到crosstool的补丁目录下：

    ```shell
    $ cp glibc-2.3.6-version-info.h_err.patch crosstool-0.43/patches/glibc-2.3.6/
    ```

    执行crosstool-0.43目录下的demo-arm-softfloat.sh脚本进行编译

    执行demo-arm-softfloat.sh脚本后，它将根据arm-softfloat.dat、gcc-3.4.5-glibc-2.3.6.dat这两个文件中定义的环境变量调用all.sh脚本进行编译。gcc-3.4.5-glibc-2.3.6.dat文件指明了要下载或使用的文件。

    需要修改demo-arm-softfloat.sh、arm-softfloat.dat、all.sh这3个文件。

    - 修改demo-arm-softfloat.sh

      ```xml-dtd
      07 TARBALLS_DIR=/work/tools/create_crosstools/src_gcc_glibc 源码存放位置
      08 RESULT_TOP=/work/tools									编译结果存放位置
      ```

    - 修改arm-softfloat.dat。表示编译出来的工具样式为arm-linux-gcc、arm-linux-ld等

      ```xml-dtd
      02 TARGET=arm-softfloat-linux-gun
      改为：
      02 TARGET=arm-linux
      ```

    - 修改all.sh。把执行demo-arm-softfloat.sh脚本的结果存放在/work/tools/gcc-3.4.5-glibc-2.3.6

      ```xml-dtd
      70 PREFIX=${PREFIX-$PREFIX_TOP/$TOOLCOMBO/$TARGET}
      改为
      70 PREFIX=${PREFIX-$PREFIX_TOP/$TOOLCOMBO}
      ```

    - 编译安装工具链：

      ```shell
      $ cd crosstool-0.43/
      $ ./demo-arm-softfloat.sh
      ```

      编译2、3个小时后，/work/tools/gcc-3.4.5-glibc-2.3.6目录里，交叉编译器、库、头文件都包含在里面。设置PATH环境变量即可使用。使用下面命令评测：

      ```shell
      $ arm-linux-gcc -v
      ```





### 第3章 嵌入式编程的基本知识

1. 交叉编译工具选项

   PC上的编译工具链为gcc、ld、objcopy、objdump等，它们编译出来的程序在x86平台上运行。要编译出能在ARM平台上运行的程序，需使用交叉编译工具arm-linux-gcc、arm-linux-ld等。

   - arm-linux-gcc选项

     c/c++文件要经过预处理preprocessing、编译compilation、汇编assembly、连接linking

     - 预处理（preprocessing）：以"#"开头的命令被称为预处理命令。包括
     
       ```c++
       #include	包含命令
       #define		宏定义命令
       #if,#ifdef	条件编译命令
       ```
     
       预处理就是将包含的文件插入源文件中、将宏定义展开、根据条件编译命令选择要使用的命令，最后将这些代码输出到```.i``` 文件中。预处理将用到```arm-linux-gcc```工具。
     
     - 编译（compilation），就是把c/c++代码（例如```.i```文件）翻译成汇编代码，使用到的工具为```ccl```。
     
     - 汇编（assembly），就是把汇编代码，翻译成复合一定格式的机器代码，在Linux上一般为"ELF"目标文件（OBJ文件），用到的工具为```arm-linux-as```。“反汇编”是指将机器代码转换为汇编代码，调试程序时常用到。
     
     - 连接（linking），就是讲OBJ文件和系统库的OBJ文件、库文件连接起来，最终生成可以在特定平台运行的个执行文件，用到的工具为```arm-linux-ld```。
     
     - 输入文件列表：
     
       | 后缀名        | 语言种类           | 后期操作             |
       | ------------- | ------------------ | -------------------- |
       | .c            | C源程序            | 预处理、编译、汇编   |
       | .C，.cc，.cxx | C++源程序          | 预处理、编译、汇编   |
       | .m            | Objective-C源程序  | 预处理、编译、汇编   |
       | .i            | 预处理后的C文件    | 编译、汇编           |
       | .ii           | 预处理后的C++文件  | 编译、汇编           |
       | .s，.S        | 汇编语言源程序     | 预处理、汇编         |
       | .h            | 预处理文件，库文件 | 通常不出现在命令行上 |
     
     - 传递给连接器（linker）的文件
     
       | .o   | 目标文件（Object file，OBJ文件） |
       | ---- | -------------------------------- |
       | .a   | 归档库文件（Archive file）       |
     
   - 举例：Hello，world C程序
   
     ```c
     //Hello，world C程序
     
     /* File: hello.c */
     #include <stdio.h>
     int main(int argc, char *argv[])
     {
     	printf("Hello World!\n");
     	return 0;
     }
     
     // 使用arm-linux-gcc直接生成hello，包含以上四个步骤
     $ arm-linux-gcc -o hello hello.c
     // 想观看编译细节，加入-v选项
     $ arm-linux-gcc -v -o hello hello.c
     ```
   
   - arm-linux-gcc选项
   
     - -c。预处理、编译和汇编源文件，但不作连接，生成OBJ文件，即 .o 文件
     - -S。编译后停止，不进行汇编，生成 .s 文件，替换原文件的 .c ， .i 等
     - -E。预处理后停止，不进行编译。忽略任何不需要预处理的文件
     - -o file。指定输出文件为 file。
     - -v。显示制作arm-linux-gcc工具自身时的配置命令，显示编译驱动程序、预处理器、编译器的版本号。
   
   - 举例：arm-linux-gcc编译代码
   
     ```c
     /* 项目名称：subfunc*/
     File1： main.c
     #include <stdio.h>
     #include "sub.h"
     int main(int argc, char *argv[])
     {
     	int i;
     	printf("Main fun!\n");
     	sub_fun();
     	return 0;
     }
     
     File2: sub.h
     void sub_fun(void);
     
     File3: sub.c
     void sub_fun(void)
     {
     	printf("Sub fun!\n");
     }
     
     使用arm-linux-gcc、arm-linux-ld类似工具与gcc、ld进行编译，使其可直接在PC机上运行
     $ gcc -c -o main.o main.c
     $ gcc -c -o sub.o sub.c
     $ gcc -o test main.o sub.o
     ```
       ```

         ```xml
         使用Code::Blocks，增加子程序文件时，要在项目文件 subfunc.cbp文件中增加sub.c文件编译的设置：
         <?xml version="1.0" encoding="UTF-8" standalone="yes" ?>
         <CodeBlocks_project_file>
            <FileVersion major="1" minor="6" />
            <Project>
                <Option title="subfunc" />
                <Option pch_mode="2" />
                <Option compiler="gcc" />
                <Build>
                    <Target title="Debug">
                        <Option output="bin/Debug/subfunc" prefix_auto="1" extension_auto="1" />
                        <Option object_output="obj/Debug/" />
                        <Option type="1" />
                        <Option compiler="gcc" />
                        <Compiler>
                            <Add option="-g" />	<!-- 以操作系统的本地格式（stabs，COFF或DWARF）
                                                    产生调试信息，GDB能够使用这些调试信息 -->
                        </Compiler>
                    </Target>
                    <Target title="Release">
                        <Option output="bin/Release/subfunc" prefix_auto="1" 
                                 extension_auto="1" />
                        <Option object_output="obj/Release/" />
                        <Option type="1" />
                        <Option compiler="gcc" />
                        <Compiler>
                            <Add option="-O2" />	<!-- 除空间和速度优化外，执行所有优化。如不进行循
                                              环展开（loop unrolling）和函数内嵌（inlining）。 -->
                        </Compiler>
                        <Linker>
                            <Add option="-s" />	<!-- 连接汇编后生成的二进制文件时，把符号表等信息
                            					从可执行文件中删去，生成文件就不能用gdb来调试了 -->
                        </Linker>
                    </Target>
                </Build>
                <Compiler>
                    <Add option="-Wall" />		<!-- 编译时，增加所有警告提示 -->
                </Compiler>        
                <Unit filename="main.c">
                    <Option compilerVar="CC" />
                </Unit>
                 <!-- start 此处增加 sub.c 子类文件编译的 文件名，否则不进行此文件编译，在ln时，会报错 -->
                <Unit filename="sub.c">
                    <Option compilerVar="CC" />
                </Unit>
                 <!-- end -->
                <Extensions>
                    <code_completion />
                    <envvars />
                    <debugger />
                    <lib_finder disable_auto="1" />
                </Extensions>
            </Project>
         </CodeBlocks_project_file>
       ```
   
   - 警告选项（Warning Option）
   
     "-Wall"选项，打开所有警告信息，如：没有指定类的声明、在声明之前就使用的函数、未使用的局部变量等。
   
     ```shell
     $ gcc -Wall -c main.c
     警告提示如下：
     main.c: In function 'main'':
     main.c:6: warning: unused variable 'i''
     ```
   
   - 调试选项（Debugging Option）
   
     -g：以操作系统的本地格式（stabs、COFF、XCOFF或DWARF)产生调试信息，GDB能够使用这些信息。
   
   - 优化选项（Optimization Option）
   
     - -O或-O1
   
       优化。不使用此选项，可以减少编译开销，特别是大函数将占用较长时间。此优化会减少目标码的大小和执行时间。如果指定此选项，“fthread-jumps"和"-fdefer-pop"选项将被打开。在delay slot机器上，"-fdelayed-branch"将被打开。等等，其它选项也可能被打开。
   
     - -O2
   
       除空间和速度优化外，执行所有优化。如不进行循环展开（loop unrolling）和函数内嵌（inlining）。
   
     - -O3
   
       除-O2以外，还打开了"-finline-functions"选项。（man gcc里可以看到解释）这个选项会只能选择整合简单函数到调用它函数中，以增加效率，但会是代码体积增大。
   
     - -O0
   
       不优化。
   
     ```shell
     例如：
     $ gcc -O2 -c -o main.o main.c
     $ gcc -O2 -c -o sub.o sub.c
     $ gcc -o test main.o sub.o
     ```
   
   - 连接器选项（Linker Option）：用于连接OBJ文件，输出可执行文件或库文件
   
     - object-file-name
   
       如果默写文件没有特别的后缀（a special recognized suffix)，GCC就认为他们是OBJ或库文件，这些文件将成为连接器的输入文件
   
       比如上面的 main.o、sub.o就是输入文件
   
     - -llibrary
   
       连接名为library的库文件。库文件的名字为"liblibrary.a"(用 lib 和 .a把library包裹起来，而且搜索一些目录)。实际上即使不使用此选项，一些默认的库也会被连接进去，可以使用 -v 选项看到这些：
   
       ```shell
       。。。
       $ gcc -v -o test main.o sub.o
       ```
   
     - -nostartfiles
   
       不连接系统标准启动文件，而标准库文件仍然正常使用：
   
       ```
       。。。
       $ gcc -v -nostartlib -o test main.o sub.o
       ```
   
       则启动文件crt1.o、crti.o、crtend.o、crtn.o没有被连接进去。这只是个例子，这些文件是必需的，这样的编译结果是无法执行的。
   
     - -static
   
       在支持动态连接（dynamic linking)的系统上组织连接共享库，即把所有相关共享库，都连接到当前可执行文件上。会导致可执行程序规模巨大。
   
       ```shell
       $ gcc -O2 -c -o main.o main.c
       $ gcc -O2 -c -o sub.o sub.c
       $ gcc -o test main.o sub.o
       $ gcc -o test_static main.o sub.o -static
       $
       $ ls -l test test_static
       -rwxr-xr-x 1 book book   6591 Jan 16 23:51 test
       -rwxr-xr-x 1 book book 546479 Jan 16 23:51 test_static
       ```
   
     - -shared
   
       生成一个共享的OBJ文件，可以和其它OBJ文件连接产生可执行文件。
   
       当不想以源代码发布程序时，可以使用"-shared"选项生成库文件，例如这样制作库文件：
   
       ```shell
       $ gcc -c -o sub.o sub.c
       $ gcc -shared -o sub.a sub.o
       
       以后要使用sub.c中的函数sub_fun时，在连接程序时，将 sub.a 加入即可：
       $ gcc -o test main.o ./sub.a
       
       也可以将多个文件制作为一个库文件：
       $ gcc -shared -o sub.a sub.o sub2.o sub3.o
       ```
   
     - -XLinker option
   
       把选项option传递给连接器，传递系统特性的连接选项。如果需要传递携带参数的选项，必需使用两次 " -Xlinker" 
   
       ```
       例如，要传递“-assert definitions”
       gcc ... -XLinker -assert -xLinker definitions ...
       ```
   
     - -W1，option
   
       把选项option传递给连接器
   
     - -u symbol
   
       使连接器认为取消了symbol的符号定义。
   
   - 目录选项 Directory Option
   
     指定搜索路径，用于查找头文件、库文件或编译器的某些成员
   
     - -Idir
   
       在头文件的搜索路径列表中添加dir目录。
   
     - -I-
   
       任何在“-I-”前面用“-I”定义的路径，只适用于`#include "file"`这种情况，不能用来搜索`#include <file>`包含的头文件。如果用“-I”指定的搜索路径位于“-l-”之后，就可以搜索所有的“#include”指令
   
     - -Ldir
   
       在“-I”选项的搜索路径列表中添加dir目录
   
       ```shell
       先制作库文件libsub.a
       $ gcc -c -o sub.o sub.c
       $ gcc -shared -o libsub.a sub.o
       
       编译main.c
       $ gcc -c -o main.o main.c
       
       连接程序(下面指令报错)
       $ gcc -o test main.o -lsub
       /...: cannot find -lsub
       collect2: error: ld returned 1 exit status
       可以使用“-Ldir”选项将当前目录加入搜索路径，则连接成功
       $ gcc -L. -o test main.o -lsub
       ```
   
     - -Bprefix
   
       指出在何处寻找可执行文件、库文件以及编译器自己的数据文件。两个标准前缀
   
       ```
       /usr/li/gcc/
       /usr/local/lib/gcc-lib/
       ```
   
       也可以通过环境变量GCC_EXEC_PREFIX获得近似的效果。
   
   - arm-linux-ld选项
   
     arm-linux-ld用于将多个目标文件、库文件连接成可执行文件，大多选项在上面已经介绍。
   
     -T 选项，可以直接使用它来指定代码段、数据段、bss段的其实地址，也可以用来指定一个连接脚本，在连接脚本中进行更复杂的地址设置。
   
     > **bss段**（bss segment）：通常指用来存放程序中未初始化的全局变量的一块内存区域。
     >
     > ​							bss是 Block Started  by Symbol。属于静态内存分配（即程序一开始将其清零）。
     >
     > **data段**：通常指用来存放程序中已初始化的全局变量的一块内存区域。属于静态内存分配
     >
     > **text段、代码段**：通常指用来存放程序执行代码的一块内存区域。通常为只读。
     >
     > **堆heep**：用于存放进程运行中动态分配的内存段。用malloc等函数分配内存到堆上，用free从堆上释放内存。
     >
     > **栈stack、堆栈**：是用户存放临时创建的局部变量的。函数被调用时，参数被压入发起调用的进程栈中，调用结束后，函数的返回值也会被存放在栈中。堆栈的FIFO特性，特别方便保存/恢复调用现场，从这个意义上讲，可以把堆栈看成一个 **寄存、交换临时数据的内存区**。
   
     **-T 选项，只用于连接Bootloader、内核等“没有底层软件支持”的软件；连接运行于操作系统之上的应用程序，无需指定 -T 选项。** 
   
     - 直接指定代码段、数据段、bss段的起始地址
   
         ```shell
         -Ttext startaddr
         -Tdata startaddr
         -Tbss startaddr

         startaddr是一个16进制数
         在/work/hardware/led_on/Makefile中，有如下语句：
            arm-linux-ld -Ttext 0x0000000 -g led_on.o -o led_on_elf
         ```
   
     - 举例说明“-Ttext”选项的作用。在/work/hardware/link目录下有个link.s文件：
   
       ```assembly
       .text
       .global _start
       _start:
       	b	step1	.
       step1:
       	ldr pc, =step2
       step2:
       	b step2
       ```
   
       使用下面的命令编译、连接、反汇编：
   
       ```shell
       arm-linux-gcc -c -o link.o link.s
       arm-linux-ld -Ttext 0x00000000 link.o -o link_elf_0x00000000
       arm-linux-ld -Ttext 0x30000000 link.o -o link_elf_0x30000000
       arm-linux-objdump -D link_elf_0x00000000 > link_0x00000000.dis
       arm-linux-objdump -D link_elf_0x30000000 > link_0x30000000.dis
       ```
   
       link.s中用到两种跳转方法：b跳转指令、直接向pc寄存器赋值。（***位置跳转***）
   
   - 使用连接脚本设置地址
   
     例如：Makefile中可以这样写
   
     ```shell
     arm-linux-ld -Ttimer.lds -o timer_elf $^
     
     "$^"表示“head.o init.o interrupt.o main.o”,所以代码就变为
     arm-linux-ld -Ttimer.lds -o timer-elf head.o init.o interrupt.o main.o
     ```
   
     timer.lds脚本内容如下：
   
     ```lisp
     SECTIONS {
     	. = 0x30000000;						当前运行地址
     	.text	:	{ *(.text) }			内容为“*（.text)”，表示所有输入文件的代码段。
     	.rodata ALIGN(4) : {*(.rodata)}		 rodata段，输出文件timer_elf中，紧挨着".text"段
     	.data ALIGN(4) : {*(.data)}			 同上，ALIGN(4)为运行地址4字节对齐。
     	.bss ALIGN(4) : {*(.bss) *(COMMON)}	  同上。
     }
     ```
   
     连接脚本格式：
   
     - 基本命令时 SECTIONS命令，描述了输出文件的"映射图"：输出文件中个段、各文件怎么放置。
   
     - 一个SECTIONS命令内部包含一个或多个段（Section），段是连接脚本的基本单元，表示输入文件中的某部分怎么放置。完整的连接脚本格式如下：
   
       ```
       SECTIONS {
       ...
       secname start ALIGN(align) (NOLOAD) : AT(ldadr)
       	{ contents } >region :phdr =fill
       ...
       }
       ```
   
       **secname**（段名）和**contents**（放入此段的代码内容）是必需的。
   
       **start**是这个段重定位地址，也称**运行地址**。如果代码中有位置无关的指令，程序在运行时，这个段必须放在这个地址上。
   
       **ALIGN（align）**：start指定了运行地址，但仍可使用BLOCK（align）来指定对齐的要求——这个对齐地址才是真正的运行地址。例如：上例中，如果.text段地址范围为0x30000000-0x300003f1，则 .rodata 段的地址是4字节对齐后的0x300003f4。
   
       **（NOLOAD）**：告诉加载器，在运行时不用加载这个段，只有在操作系统下才有意义。
   
       **AT(ldadr)**：指定这个段在编译出来的映象文件中的地址——加载地址（load address）。通过这个选项，可以控制隔断分别保存输出文件中不同的位置，便于把文件保存到单板上：A段放在A处，B段放在B处，运行前再把A、B段分别读出来组装成一个完整的执行程序。
     
   - arm-linux-objcopy选项
   
     `arm-linux-objcopy` 用来复制一个目标文件的内容到另一个文件中，可以进行格式转换。格式如下：
   
     ```
     arm-linux-objcopy	[-F bfdname | --target=bfdname]
     				   [-I bfdname | --input-target=bfdname]
     				   ...
     				   [-v | --verbose] [-V | --version] [--help]
     				   inputfile [outfile]
     ```
   
     - input-file、outfile
   
       源文件、目标文件，目标文件系统可以自动生成
   
     - -l bfdname 或 --input-target=bfdname
   
       指明源文件的格式，bfdname是BFD库中描述的标准格式名。如果不指明，arm-linux-objcopy会自己去分析。
   
     - -O bfdname 或--output-target=bfdname
   
       指明目标文件格式。
   
     - -F bfdname 或--target=bfdname
   
       同时指明源文件、目标文件的格式。复制过程不进行格式转换。
   
     - -R sectionname 或 --remove-section=sectionname
   
       从输出文件中删掉所有名为sectionname的段，此选项可多次使用。（！危险指令！）
   
     - -S 或 --strip-all
   
       不从源文件中复制重定位信息和符号信息到目标文件中去
   
     - -g或--strip-debug
   
       不从源文件复制调试符号到目标文件中去。
   
     - 举例
   
       ```shell
       在编译bootloader、内核时，常用arm-linux-objcopy命令将ELF格式的生成结果转换为二进制文件：
       $ arm-linux-objcopy -O binary -S elf_file bin_file
       ```
   
       > ELF格式：是一种用于二进制、可执行文件、目标代码、共享库和核心转储格式文件。
       >
       > ​				 是Liunx的主要可执行文件格式。
   
   - arm-linux-objdump选项
   
     `arm-linux-objdump`用于显示二进制文件信息，本书用来查看反汇编代码。
   
     ```
     arm-linux-objdump	[-a] [-b bfdname | --target=bfdname]
     				   ...
     				   [--adjust-vma=offset] [--version] [--help]
     				   objfile...
     ```
   
     - **-b** bfdname 或 --target=bfdname
   
       指定目标码格式。因其能自动识别，所以不是必须的。可以用`arm-linux-objdump -i`查看支持的目标码格式列表。
   
     - --disassemble 或 -d
   
       反汇编可执行代码段(executable sections)
   
     - --disassemble-all 或 **-D** 
   
       反汇编所有段
   
     - -EB 或 -EL 或 --endian={big | little}
   
       指定字节序
   
     - --file-headers 或 -f
   
       显示文件的整体头部摘要信息
   
     - --section-headers、--headers 或 -h
   
       显示目标文件各个段的头部摘要信息你
   
     - --info 或 -i
   
       显示支持的目标文件格式和CPU架构，它们在"-b"、"-m"选项中用到。
   
     - --section=name 或 -j name
   
       仅显示指定section的信息
   
     - --architecture=machine 或 **-m** machine
   
       指定反汇编目标文件时使用的架构。可以用"-i"列出这里能够指定的架构。
   
     - 在调试程序时，常用`arm-linux-objdump`命令来得到汇编代码：
   
       ```shell
       1. 将ELF格式的文件转换为反汇编文件：
       $ arm-linux-objdump -D elf_file > dis_file
       
       2. 将二进制文件转换为反汇编文件：
       $ arm-linux-objdump -D -b binary -m arm bin-file > dis_file
       ```
   
   - 汇编代码、机器码和存储器的关系以及数据显示
   
     ```assembly
     例如：
     4bc:		e3a0244e	mov r2, #1308622848 ; 0x4e000000
     4c0:		e3a0244e	mov r3, #1308622848 ; 0x4e000000
     4c4:		e5933000	ldr r3, [r3]
     (运行地址)	（机器码）	（汇编代码）
     
     以机器码 0xe3a0244e 为例：
     分解为2进制：
     e		3		a		0		2		4		4		e		(0x)
     1110	00 1  1	101  0	0000	0010	0100	0100	1110	   (0b)
     cond       [1][1101] [0][0000]   [0010]     [...]					(对应的实际值)
     31.28      25 24..21 20 19..16   15..12     11...0					(在二进制中的位置)
     Cond       L  OpCode S   Rn        Rd       Operand2  				(ARM数据处理指令格式)
     
     1. 31.28=0b1110,表示这条指令 无条件执行
     2. 25=0b1，表示Operand2是一个立即数。
     3. 24..21=0b1101,表示这是MOV指令，即Rd：=Op2
     4. 20=0b0,表示这条指令执行时，不影响状态位
     5. 15..12=0b0010,表示Rd就是r2
     6. 11...0=0x44e,这是一个立即数。高4位为rotate_imm，低8位为immed_8。
     	立即数算法：`<immediate>=immed_8循环右移（2xrotate_imm)` 
     	此处：rotate_imm=0x4、immed_8=0x4e = 0b 0000 0000 0000 0000 0000 0000 0100 1110
     	循环右移2*0x4=8位，即得到 0b 0100 1110 0000 0000 0000 0000 0000 0000 = 0x4e000000
     	所以立即数为0x4e000000
     
     因此，机器码 0xe3a0244e 的汇编代码为：
     mov r2， #0x4e000000
     即
     mov r2, #1308622848
     ```
   
   ​	![1565932001023](/1565932001023.png) 
   
2. Makefile介绍

   在Linux中，用make命令来编译程序，make命令依赖于定制的Makefile文件，例如：

   ```makefile
   hello: hello.c
   	gcc -o hello hello.c
   clean:
   	rm -f hello
   ```

   (**缩进必需用Tab缩进，不能用空格**) 

   > Makefile完整规则，参考《GUN Make使用手册》

   - Makefile规则

     ```
     目标target...: 依赖prerequiries...
     <tab>命令command
     ```

     目标（target）：要生成的文件的名称，可以是可执行文件或OBJ文件，也可以是一个执行的动作名称，如clean

     依赖（prerequiried）：用来产生目标的材料（如：源文件），一个目标经常有几个依赖。

     命令（command）：生成目标时执行的动作，一个规则可以含几个命令，每个命令占一行。

   - Makefile文件里的赋值方法

     ```makefile
     变量的定义语法形式如下：
     延时变量
     immediate = deferred
     immediate ?= deferred
     define immediate	
     	deferred		（延时变量）
     endef
     
     立即变量
     immediate := immediate
     
     根据定义时，确定是立即变量还是延时变量
     immediate += deferred or immediate
     ```

     - 延时变量：变量使用时才扩展开，即当使用时这个变量值才确定
     - 立即变量：定义时它的值就确定了。

   - Makefile常用函数

     ```makefile
     函数调用格式：
     $(function arguments)
     
     function: 函数名
     arguments： 该函数的参数。多个参数，用逗号“，”
     
     函数名和参数中间用Tab或空格隔开
     ```

     - 字符串替换和分析函数

       ```makefile
       在文本“text”中使用“to”替换所有“from”
       $(subst from,to,text)	
       例如：$(subst ee,EE,feet on the street)
       结果为："fEEt on the strEEt"
       
       在“text”中替换符合“pattern”的字，用replacement替换。可用通配符。
       $(patsubst pattern,replacement,text)	
       例如：$(patsubst %.c,%.o,x.c.c bar.c)
       结果为："x.c.o bar.o"
       
       去掉前后空格,并将中间的多个空格压缩为单个空格。
       $(strip string)
       
       在字符串in中搜索find，如果找到，返回"find"，否则返回" "：
       $(findstring find,in)
       
       返回在text中由空格隔开，且匹配格式"pattern...",去除不符合此格式的字
       $(filter pattern...,text)
       例如：$(filter %.c %.s,foo.c bar.c baz.s ugh.h)
       结果为："foo.c bar.c baz.s"
       
       （同上）但去除符合此格式的字
       $(filter-out pattern...,text)
       
       
     
         按字母顺序排序
           $(sort list)
         例如：$(sort foo bar lose)
           结果为："bar foo lose"。
       ```
     
     - 文件名函数
     
       ```makefile
       抽取每一个文件名的路径部分
       $(dir names...)
       例如：$(dir src/foo.c hacks)
       结果："src/ ./"
       
       抽取文件名
       $(notdir names...)
       
       抽取文件名后缀
       $(suffix names...)
       
       抽取除文件名后缀外一切字符
       $(basename names...)
       
       为一系列文件名添加后缀suffix
       $(addsuffix suffix,names...)
       
       为一系列文件名添加前缀prefix
     $(addprefix prefix,names...)
       
       列出当前目录下符合pattern的所有文件名,pattern包含通配符
     $(wildcard pattern)
       例如：$(wildcard *.c)
       当前文件夹下有文件1.c、2.c、1.h、2.h,结果为："1.c 2.c"
       ```
       
     - 其它函数
     
       ```makefile
       1. 循环遍历：遍历list中的元素，一个个依次赋给var，每个var都被text引用处理
       $(foreach var,list,text)
       例如：
       dirs := a b c d
       files := $(foreach dir, $(dirs), $(wildcard $(dir)/*))
       相当于：
       files := $(wildcard a/* b/* c/* d/*)
       
       2. if语句,条件分支语句。condition为非空，为真；为空，为假。
       $(if condition,then-part[,els-part])
     
       3. 查询变量，返回变量当前状态。如：undefined、default、file，environment，command line等
     $(origin variable)
       
       4. 执行shell命令,执行外部控制台命令
         $(shell command arguments)
       ```
       
     - Makefile范例
     
       File：Makefile
     
       ```makefile
       src := $(shell ls *.c)					# main.c sub.c
       objs := $(patsubst %.c,%.o,$(src))		 # main.o sub.o
       
       test: $(objs)
       	gcc -o $@ $^					   # gcc -o test main.o sub.o
     	
       %.o:%.c
     		gcc -c -o $@ $<					# gcc -c -o main.o main.c
       								   # gcc -c -o sub.o sub.c
       clean:
       	rm -f test *.o
       ```
       
       "$@"、"$^"、"$<"为自动变量，分别表示：
       
         - "$@"，规则的目标文件名
         - "$^"，所有依赖的名字
         - "$<"，第一个依赖的文件名
       
       “%”通配符，表示任意个数的字符。
   
     
   
3. 常用ARM汇编指令及ATPCS规则
   
   1. 本书使用的汇编指令
   
      嵌入式开发中，汇编程序常常用于对性能要求非常苛刻的函数，如系统启动时的初始化，进出中断时的环境保存、恢复等。
   
      > 汇编指令详见S3C2410、S3C2440的数据手册
   
      - 相对跳转指令：b、b1
   
        b1指令除了跳转外，还返回地址（b1的下一条指令的地址）保存在lr寄存器中。
   
        跳转范围是**当前指令的前后32MB**。
   
      - 数据传送指令mov，地址读取伪指令ldr
   
           mov指令可以把一个寄存器的值赋值给另一个寄存器，或者把一个常数赋给寄存器。
   
           ```
        mov r1, r2		/* r1=r2 */
           mov r1, #4096	/* r1=4096 */
        ```
   
           mov指令传送的常数必须能用立即数来表示，当不知道是否能用立即数表示时，可以使用ldr命令来赋值——此时为伪指令，第二个参数前面有“=”时。
   
           ```
           	ldr r1, =4097	/* r1=4097 将常数赋值给寄存器r1*/
           
           	ldr r1, =label	/* 获得代码的绝对地址 */
        label:
           ... ...
           ```
   
        ldr本意为“大范围的**地址读取伪指令**”(即第二个参数前面有"**=**"时)
   
      - **内存访问指令**：ldr、str、ldm、stm
   
           ldr指令从内存中读取数据到寄存器，str指令把寄存器的值存储到内存，操作32位数据。
   
           ```assembly
           ldr r1, [r2, #4]	/* 将地址(r2+4)的数据读取到r1中 */
           ldr r1, [r2]		/* 将地址r2的数据读到r1中，即*(r1)=*(r2)*/
           ldr r1, [r2], #4	/* 将地址r2的数据读到r1中，把r2地址加4，即r2=r2+4 */
           
           str r1, [r2, #4]	/* 将地址r1的数据保存到地址r2+4内存单元中 */
        str r1, [r2]		/* 将r1的数据保存到r2中，即 *(r2)=*(r1) */
        str r1, [r2], #4	/* 将r1的数据保存到r2中，把r2的地址加4，即r2=r2+4 */
        ```
   
           ldm和stm是批量内存访问指令：
   
           ```assembly
           ldm{cond}<addressing_mode> <rn>{!} <register list>{^}
           stm{cond}<addressing_mode> <rn>{!} <register list>{^}
           
           {cond}:指令执行条件，条件码。2^4个条件码(见下面表3.2)
           <addressing_mode> ： 地址变化模式
           	- ia:Increment After
           	- ib:Increment Before
           	- da:Decrement After
           	- db:Decrement Before
           <rn> : 保存内存的地址，加感叹号，表示执行指令后，rn的值，等于下一个内存单元的地址
           <register list> : 寄存器列表
           {^} : 两种含义：
           	- 如果<register list>中有pc寄存器，表示执行指令后，spsr寄存器的值，将自动复制到cpsr寄存器中。常用于从中断处理函数中返回
           	- 如果<register list>中没有pc寄存器，则表示操作的是用户模式下的寄存器，而不是当前特权模式的寄存器。
           	
        指令中寄存器列表和内存单元的对应关系为：
           	编号低的寄存器对应内存中的低地址单元，编号高的寄存器对应内存中的高地址单元。
           ```
   
        > cpsr（current program status register，程序状态寄存器）：在任何模式下被访问。包含条件标志位、中断禁止位、当前处理模式标识，以及一些控制和状态位。
        >
        > cpsr在用户级编程时用于存储一些条件码。
        >
        > spsr(saved program status register，程序状态保存寄存器)：任何模式下都有一个spsr，用于保存cpsr的状态，以便异常返回后恢复异常发生时的工作状态。由于人用户模式和系统模式不属于异常模式，所以它没有spsr。
        
         ldm和stm指令示例：(%%%%%%%%%%%%)
        
           ```assembly
        HandleIRQ:							    @ 中断入口函数
        	sub lr, lr, #4						@ 计算返回地址,即lr-4的地址
           	stmdb sp!, { r0-r12, lr }			 @ 保存使用到的寄存器
           									   @ r0-r12,lr被保存在sp表示的内存中
           									   @ "！"使得指令执行后sp=sp-14*4
           	
           	ldr lr, =int_return					 @ 设置调用IRQ_Handle函数后的返回地址
           	ldr pc, =IRQ_Handle					 @ 调用中断分发函数
        int_return:
           	ldmia sp!, { r0-r12, pc }^			  @ 中断返回，"^"表示将spsr的值复制到cpsr
           									    @ 于是从irq模式返回被中断的工作模式
           									    @ "!"使得指令执行后sp=sp+14*4
           ```
        
       - 加减指令：add、sub
      
         ```assembly
         add r1, r2, #1		/* [r1]=[r2]+1 */
         sub r1, r2, #1		/* [r1]=[r2]-1 */
         ```
      
       - 程序状态寄存器的访问指令： msr、mrs
      
         ARM处理器有一个**程序状态寄存器(cpsr)**，它用来控制处理器的工作模式、设置中断的总开关。
      
         ```assembly
         msr cpsr, r0	/* 复制r0到cpsr中 */
         mrs r0, cpsr	/* 复制cpsr到r0中 */
         ```
      
       - 其它伪指令
      
         例如：
      
         ```assembly
         .extern		main
         .text
         .global	_start
         ._start:
         ```
      
         ".extern"定义一个**外部**符号(**变量 或 函数**)，本例表示本文件引用的main是一个外部函数。
      
         ".text"表示下面的语句都属于**代码段**。
      
         ".global"将本文件中的某个程序标号**定义为全局**，本例：表示**_start是个全局函数** 
      
      - 汇编指令的执行条件
      
           大多数ARM指令都可以**条件执行，即根据cpsr寄存器中的条件标志位决定是否执行该指令**：不满足，则该指令相当于一条"nop指令"。
      
           每条ARM指令包含4位条件码域，即可定义16个执行条件，可以将这些执行条件的助记符附加在汇编指令之后，例如：moveq，movgt等。
      
           ![1566193519934](/1566193519934.png)
      
           表中的cpsr条件标志位N、Z、C、V分别表示Negative、Zero、Cary、oVerflow。
      
   2. ARM-THUMB子程序调用规则ATPCS
   
      C语言程序和汇编程序之间互相调用的规则，在ARM处理器中，被称为ATPCS（ARM-Thumb procedure call standard，ARM-Thumb程序中子程序调用规则）。
   
      规则包括：寄存器使用规则、数据栈使用规则、参数传递规则。
   
      - 寄存器使用规则
   
        ARM处理器中有 r0~r15 共16个寄存器。
   
        | 寄存器 | 别名   | 使用规则                                                     |
        | ------ | ------ | ------------------------------------------------------------ |
        | r15    | pc     | 程序计数器                                                   |
        | r14    | lr     | 连接寄存器                                                   |
        | r13    | sp     | 数据栈指针                                                   |
        | r12    | ip     | 子程序内部调用的scratch寄存器                                |
        | r11    | v8     | ARM状态局部变量寄存器8                                       |
        | r10    | v7、s1 | ARM状态局部变量寄存器7、在支持数据栈检查的ATPCS中为数据栈限制指针 |
        | r9     | v6、sb | ARM状态局部变量寄存器6、在支持RWPI的ATPCS中为静态基址寄存器  |
        | r8     | v5     | ARM状态局部变量寄存器5                                       |
        | r7     | v4、wr | ARM状态局部变量寄存器4、Thumb状态工作寄存器                  |
        | r6     | v3     | ARM状态局部变量寄存器3                                       |
        | r5     | v2     | ARM状态局部变量寄存器2                                       |
        | r4     | v1     | ARM状态局部变量寄存器1                                       |
        | r3     | a4     | 参数/结果/scratch寄存器4                                     |
        | r2     | a3     | 参数/结果/scratch寄存器3                                     |
        | r1     | a2     | 参数/结果/scratch寄存器2                                     |
        | r0     | a1     | 参数/结果/scratch寄存器1                                     |
   
        寄存器使用规则：
   
        - 子程序间通过寄存器r0~r3来**传递参数**，这时可以使用它们的别名a0~a3。被调用的子程序返回前无需恢复r0~r3的内容
   
        - 在子程序中，使用r4~r11来保存**局部变量**，可以使用别名v1~v8。如果在子程序中使用了它们的某些寄存器，子程序进入时，要保存这些寄存器的值，在返回前恢复它们；对于子程序中没有使用到的寄存器，则不必进行这些操作。在Thumb程序中，通常只能使用寄存器r4~r7来保存局部变量。
   
        - 寄存器r12用作子程序间**scratch寄存器**，别名为ip
   
        - 寄存器r13用作**数据栈**指针，别名**sp**。在子程序中**r13不能用作其它用途**，它的值在进入、退出子程序时 必需相等
   
        - 寄存器r14成为连接寄存器，别名lr。用于保存程序的**返回地址**。
   
          如果在子程序中保存了返回地址(比如：将lr值保存到数据栈中)，r14可以用作其它用途
   
        - 寄存器r15是程序计数器，别名**pc**。它**不能用作其它用途**。
   
      - 数据栈的使用规则
   
        数据栈两个增长方向：
   
        - 向内存地址减小的方向增长时，称为DESCENDING栈；
        - 向内存地址增加的方向增长时，称为ASCENDING栈
   
        所谓数据栈的增长就是**移动栈指针**：
   
        - 当栈指针指向栈顶元素时，称为FULL栈
        - 当栈指针指向栈顶元素相邻的一个空的数据单元时，称为EMPTY栈
   
        栈的分类：
   
        - **FD：Full Descending，满递减**
        - ED：Empty Descending，空递减
        - FA：Full Ascending，满递增
        - DA：Empty Ascending，空递增
   
        **ATPCS规定数据栈为FD类型**，对数据栈的操作时8字节对齐。使用**stmdb/ldmia**批量内存访问指令来操作FD数据栈。
   
        - 使用stmdb指令往数据栈保存内容时，先递减sp指针，再保存数据
   
        - 使用ldmia指令从数据栈中恢复数据时，先获得数据，在递增sp指针
   
          sp指针总是指向栈顶元素
   
      - 参数传递规则
   
        当参数<=4个时，使用r0~r3来传递参数。如果参数大于4个，剩余的参数通过数据栈来传递。
   
        返回值的结果，使用a0~a3来传递
   
        实例：
   
        ```
        假设CopyCode2SDRAM函数使用C语言实现的，它的数据原型如下：
        int CopyCode2SDRM(unsigned char *buf, unsigned long start_addr, int size)
        
        在汇编代码中，使用下面的代码调用它，并判断返回值：
        ldr r0, =0x30000000		@ 1. 目标地址 = 0x30000000，这是SDRAM的起始地址 *buf
        mov r1, #0			    @ 2. 源地址 = 0							start_addr
        mov r2, #16*1024		@ 3. 复制长度 = 16k							size
        b1	CopyCode2SDRAM		@ 调用C函数CopyCode2SDRAM
        cmp a0, #0				@ 判断函数返回值
        ```
   
        

### 第4章 Windows、Linux环境下相关工具、命令的使用

- Windows下的代码阅读工具、编辑工具——Source Insight
- Windows与Linux交互工具——Cuteftp、SecureCRT
- Linux下代码阅读、编辑工具——KScope，串口工具——C-kermit
- Linux常用命令

1. Windows环境工具

   1. 代码阅读、编辑工具Source Insight(收费软件)
   2. Windows与Linux交互工具——Cuteftp(收费软件)、SecureCRT(收费软件)
   3. TFTP服务器软件Tftpd32(DHCP、TFTP、SNTP和Syslog服务器软件，也是TFTP客户端软件)，可以用Linux下NFS代替。U-Boot就可以通过tftp从Windows系统中获取文件了。

2. Linux环境下的工具

   1. 代码阅读、编辑工具KScope

   2. 远程登录工具C-kermit，集成了网络通信、串口通信工具

      - 支持Kermit文件传输协议
      - 自定义脚本
      - 支持多种软硬件平台
      - 有安全认证、加密功能
      - 内建FTP、HTTP客户端，以及SSH接口
      - 支持字符集转换

   3. vi

   4. grep、find

      ```
      grep "request_irq" * -R		在当前目录下（*），递归（-R）查找“request_irq"字样的文件
      
      grep "request_irq" kernel -R	在当前目录下的kernel子目录下递归查找
      
      find -name "*fb*"			在内核目录下查找文件名中包含"fb"字样的文件
      
      find drivers/net -name "*fb*"	在目录drivers/net下查找文件
      ```

   5. tar

      ```
      tar czvf test.tar.gz dirA	// 将目录dirA压缩，c创建、z以gzip方式、v显示执行过程、f文件
      tar cjvf test.tar.bz2 dirA	// 同上，以bz2格式压缩
      
      tar xzvf test.tar.gz		// 同上，解压
      ```

   6. diff、patch

      diff制作补丁文件，patch打补丁命令

      ```
      // 生成linux-2.6.22.6 升级为 linux-2.6.22.6_ok 的补丁文件linux_2.6.22.6_ok.diff
      diff -urNwB linux-2.6.22.6 linux-2.6.22.6_ok > linux_2.6.22.6_ok.diff
      
      // 将补丁文件linux_2.6.22.6_ok.diff应用到原始目录 linux-2.6.22.6 上去
      // 文件linux_2.6.22.6_ok.diff 和 目录linux-2.6.22.6 位于当前目录下
      patch -p1 < ../ linux-2.6.22.6_ok.diff
      
      -pn 表示忽略路径中第n个斜线之前的目录
      ```



## 第2篇 ARM9 嵌入式系统基础实例篇

- GPIO接口
- 存储控制器
- 内存管理单元MMU
- NAND Flash控制器
- 中断体系结构
- 系统时钟和定时器
- 通用异步收发器UART
- I[^2]C接口
- LCD控制器
- ADC和触摸屏接口

### 第5章 GPIO接口

- 掌握嵌入式开发的步骤：编程、编译、烧写程序、运行
- 通过GPIO的操作了解软件如何控制硬件

1. GPIO硬件介绍

   GPIO(General Purpose I/O Ports)通用输入/输出端口，即一些引脚，可以通过它们输出高低电平或者通过它们读入引脚的状态——高电平或低电平。

   S3C2410有117个I/O端口，分为A~J共9组：GPA、GPB、...、GPJ。

   可以通过设置寄存器来确定某个引脚用于输入、输出还是其它特殊功能。比如可以设置GPH6作为一般的输入、输出引脚，或者用于串口。

   1. 通过寄存器来操作GPIO引脚

      对于输入，一定可以通过读取某个寄存器来确定引脚的电平是高还是低；

      对于输出，一定可以通过写入某个寄存器来让这个引脚输出高电平或低电平；

      对于其它特殊功能，则有另外的寄存器来控制他。

      **GPxCON寄存器**用于选择引脚功能，**GPxDAT**用于读写引脚数据，**GPxUP**用于确定是否使用内部上拉电阻。

      x为A、B、...、H/J，没有GPAUP寄存器。

      - GPxCON寄存器

        它用于配置（configure）——选择引脚的功能。

        **PORT A**与**PORT B~PORT H/J**在功能选择方面不同。

        GPACON中每一位对应一根引脚(共23根引脚)。

        - 当某位被设为0时，相应引脚为输出引脚。此时我们可以在GPADAT中相应位写入0或1让此引脚输出低电平或高电平；
        - 当某位被设为1时，相应引脚为地址线或用于地址控制，此时GPADAT无用。
        - 一般GPACON通常被设为全1，以便访问外部存储器件。

        PORT B~PORT H/J完全相同。**GPxCON中每两位控制一根引脚**：

        - 00输入、
        - 01输出、
        - 10特殊功能、
        - 11保留不用

      - GPxDAT寄存器

        GPxDAT用于读写引脚：

        - 当引脚被设为输入时，读此寄存器，可知相应引脚的电平状态（高或低）
        - 当引脚设为输出时，写此寄存器相应位可令相应引脚输出高电平或低电平

      - GPxUP寄存器

        某位为1时，相应引脚无内部上拉电阻；为0时，相应引脚使用内部上拉电阻

        ![1566283600828](/1566283600828.png)

        上拉电阻、下拉电阻的作用：

        当|GPIO引脚处于第三态(既不是输出高电平，也不是输出低电平，而是呈高阻态，相当于没接芯片)时，它的电平状态由上拉电阻、下拉电阻确定。

   2. 怎样使用软件来访问硬件

      - 访问单个引脚

        单个引脚的操作，有3种：

        - 输出高低电平
        - 检测引脚状态
        - 中断

        对某个引脚的操作通过读、写寄存器来完成。

        电路如下：

        ![1566283931762](/1566283931762.png)

        - 可以设置GPBCON寄存器GPB5、GPB6、GPB7和GPB8设为输出功能，然后写GPBDAT寄存器的响应位，使得这4个引脚输出高电平（LED熄灭）或低电平（LED点亮）。
        - 可以设置GPFCON寄存器将GPF0、GPF2、GPG3和GPG11设为输入功能，然后通过读出GPFDAT/GPGDAT寄存器并判断相应位是0或1来确定各个按键是否被按下，按下为低电平，GPFDAT/GPGDAT寄存器中相应位为0；否则为1。

        

        ##### 访问寄存器的方法：

        ​					**通过软件读写它们的地址**。（见对应的芯片手册，本书见S3C2440A芯片手册）
      
        
      
        比如：S3C2410和S3C2440的**GPBCON**、**GPBDAT**寄存器地址是**0x56000010**和**0x56000014**，可以通过如下指令让GPB5输出低电平，点亮LED1：
      
        ```c
      	 #define GPBCON		(*(volatile unsigned long *)0x56000010)
        #define GPBDAT		(*(volatile unsigned long *)0x56000014)
      	 #define GPB5_out	(1<<(5*2))	// 0000 0100 0000 0000 （GPB5的位置为输出）
        GPBCON = GPB5_out;	// 设置GPB5为输出
      	 GPBDAT &= ~(1<<5);	// 设置低电平，LED1点亮   0b 1110 1111 第5个位置为0，点亮
        				   // GPBDAT |= (1<<5);	LED1熄灭	0b 0001 0000 第5个位置为1，熄灭
        ```
     ```
      
   - 以总线方式访问硬件
      
     通过总线方式访问硬件更常见。
      
     以NOR Flash的访问为例。
      
     ![1566383756096](/1566383756096.png)
      
     图中缓冲器的作用为提高驱动能力、隔离前后级信号。
      
        NOR Flash AM29LV800BB的片选信号使用S3C2410/S3C2440的nGCS0信号。当CPU发出的地址信号处于0x00000000~0xFFFFFFFF之间时，nGCS0信号有效（低电平），于是NOR Flash被选中。
   
        这时
   
        - CPU发出的地址信号传到NOR Flash
   
        - 进行写操作时，nWE信号为低，数据信号从CPU发给NOR Flash
        - 进行读操作时，nWE信号为高，数据信号从NOR Flash发给CPU。
      
        本图所示的硬件连线决定了读写操作都是以16位为单位？？？？
      
        发起读写操作的方法，举例：
      
        ```c
        例5.1 地址对齐的16位读操作
        unsigned short * pwAddr = (unsigned short *)0x2;
     unsigned short wVal;
        wVal = *pwAddr;
        
        此代码会向NOR Flash发起读操作：
        CPU发出的读地址为0x2，则地址总线ADDR1~ADDR20、A0~A19的信号都是1、0、...、0（CPU的ADDR0为0，不过ADDR0没有接到NOR Flash上）。
        NOR Flash接收到的地址就是0x1，NOR Flash在稍后的时间里将此地址上的16位数据取出，并通过数据总线D0~D15发给CPU。
     ```

        ```c
        例5.2 地址不对齐的16位读操作
        unsigned short * pwAddr = (unsigned short *)0x1;
        unsigned short wVal;
        wVal = *pwAddr;
        
        由于地址为0x1，不是2对齐的，但是BANK0的位宽被设为16，这将导致异常。可以设置异常处理函数来处理这种情况。
     在异常处理函数中，使用0x0、0x2发起两次读操作，然后将两个结果组合起来：
        	- 使用地址0x0的两字节数据D0、D1；
        	- 使用地址0x2读到D2、D3；
        	- 最后，D1、D2组合成一个16位的数返回给wVal
        如果没有设置地址不对齐的异常处理函数，那么上述代码将出错。
        如果某个BANK的位宽被设为n，访问此BANK时，在总线上永远只会看到地址对齐的n位操作。
        ```

        ```c
        例5.3 8位读操作
        unsigned char * pucAddr = (unsigned char *)0x6;
        unsigned char ucVal;
     ucVal = *pucAddr;
        
        CPU首先使用地址0x6对NOR Flash发起16位的读操作，得到两字节的数据，假设为D0、D1；
        然后将D0取出赋值给变量ucVal。
        在读操作期间，地址总线ADDR1~ADDR20、A0~A19的信号都是1、1、...、0(CPU的ADDR0为0，不过ADDR0没有接到NOR Flash上).
        CPU会自动丢弃D1。
        ```
          
        ```c
        例5.4 32位读操作
        unsigned int * pdwAddr = (unsigned int *)0x6;
     unsigned int dwVal;
        dwVal = *pucAddr;
        
        CPU首先使用地址0x6对NOR Flash发起16位的读操作，得到两字节的数据，假设为D0、D1；
        再使用地址0x8发起读操作，得到两字节的数据，假设D2、D3;
        最后将这4个数据组合后赋给变量dwVal
        ```
          
        ```c
        例5.5 16位写操作
        unsigned short * pwAddr = (unsigned short *)0x6;
     *pwAddr = 0x1234;
        
     CPU发起一次多NOR Flash的写操作，地址总线ADDR1~ADDR20、A0~A19的信号都是1、1、...、0(CPU的ADDR0为0，不过ADDR0没有接到NOR Flash上);
        数据总线DATA0~DATA15、D0~D15的信号为(0、0、1、0)、1、1、0、0、0、1、0、0、(1、0、0、0)。
     									4           3           2           1
            								-------------------------------------->
        ```

        由此可见，CPU使用某个地址进行访问时，这个32位的地址值和ADDR0~ADDR31一一对应(也许CPU没有引出那么多地址信号，比如S3C2410/S3C2440只引出了ADDR0~ADDR26共27根地址线)。
          
        外接的设备可以以8位、16位、32位进行操作——这取决于硬件设计：
          
        - 如果以8位进行操作，则数值出现在数据信号DATA0~DATA7上；
        - 如果以16位进行操作，则数值出现在数据信号DATA0~DATA15上；
        - 如果以32位进行操作，则数值出现在数据信号DATA0~DATA31上

2. GPIO操作实例：LED和按键

   本节，涉及在开发版上运行程序。

   约定：所有程序均在hardware目录下的各级子目录中，生成的可执行文件名为，目录名加上后缀 .bin，比如leds目录下的可执行文件为 leds.bin

   1. 硬件设计

      LED和按键与处理器的电路连接如图5.2
      
      ![1566283931762](/1566283931762.png) 
      
   2. 程序设计及代码详解
   
      本节有3个实例，通过读写GPIO寄存器来驱动LED、获得按键状态。先使用汇编程序编写一个简单的点亮LED的程序，然后使用C语言实现更复杂的功能。
   
      - 实例1：使用汇编代码点亮一个LED
   
        源程序/work/hardware/led_on/led_on.S。它有7条指令，只是简单的点亮发光二极管LED1.
   
        操作步骤：
   
        - 把PC并口和开发板JTAG接口连起来，确保插上NAND_BOOT跳线、上电。
   
        - 进入led_on目录后，执行如下命令生成可执行文件led_on.bin
   
          ```shell
          $ make
          ```
   
        - 执行如下命令将led_on.bin写入NAND Flash
   
          sjf2410.exe、sjf2440.exe是Windows烧写开发板的JTAG工具，嵌入式Linux下的工具名为Jflash-s3c2410、Jflash-s3c2440，它们用法相似。工具位于/work/tools/jtag目录下，在Windows上使用前要先安装驱动，在/work/tools/jtag/for_windows/jtag_driver目录下。
   
          ```shell
          $ sjf2410.exe /f:led_on.bin /d=5
          或
          $ sjf2440.exe /f:led_on.bin /d=5
          
          (Linux下，Jflash-s3c2410、Jflash-s3c2440不支持对NOR Flash操作，前面要加上sudo)
          ```
   
          - 当出现如下提示，输入0选择NAND Flash K9F1208
   
            ![1566527147184](/1566527147184.png)
   
          - 当出现如下提示，输入0表示烧写NAND Flash K9F1208
   
            ![1566527387410](/1566527387410.png)
   
          - 当出现如下提示，输入0表示从第0块开始烧写
   
            ![1566527519476](/1566527519476.png)
   
          - 当再次出现第2个提示，输入2，并退出
   
            ![1566527387410](/1566527387410.png)
   
        - 按开发版上的复位键后可看见LED1被点亮了
   
        实例分为4个步骤：编写源程序、生成可执行程序、烧写程序、运行程序。
   
        
   
        **源程序led_on.S**:
   
        (S3C2410和S3C2440的GPBCON、GPBDAT寄存器地址是0x56000010和0x56000014，可以通过如下指令让GPB5输出低电平，点亮LED1：)
   
        ```assembly
        .text
        .global	_start
        _start:
        		LDR		R0,=0x56000010			@ R0设为GPBCON寄存器。此寄存器		04
        									   @ 用于选择端口B各引脚的功能			 05
        									   @ 是输出、输入、还是其它
        		MOV		R1,#0x00000400			@ 0x0400 = 0100 0000 0000		  07
        		STR		R1,[R0]				   @ 设置GPB5为输出口，位[11:10]=0b01	  08
        		LDR		R0,=0x56000014			@ R0设为GPBDAT寄存器。此寄存器		09
        									   @ 用于读写端口B各引脚的数据
        		MOV		R1,#0x00000000			@ 此值改为0x00000020，	             11
        									   @ 可让LED1熄灭
        		STR		R1,[R0]					@ GPB5输出0，LED1点亮			   13
        MAIN_LOOP:																 @14
        		B		MAIN_LOOP												 @15
        ```
   
        对应c语言代码：
   
        ```c
        #define GPBCON		(*(volatile unsigned long *)0x56000010)
        #define GPBDAT		(*(volatile unsigned long *)0x56000014)
        #define GPB5_out	(1<<(5*2))
        GPBCON = GPB5_out;	// GPB5位输出
        GPBDAT &= ~(1<<5);	// 设置低电平，LED1点亮   0b 1110 1111 第5个位置为0，点亮
        				   // GPBDAT |= (1<<5);	LED1熄灭	0b 0001 0000 第5个位置为1，熄灭
        ```
   ```
        
   汇编程序，第4、7、8行用于将LED1对应的引脚GPB5设置成输出引脚；第9、11、13让这个引脚输出0；第15行指令是个死循环。
        
   **指令make调用的Makefile**：
        
   ​```makefile
        led_on.bin:led_on.S
        	arm-linux-gcc -g -c -o led_on.o led_on.S					编译
        	arm-linux-ld -Ttext 0x00000000 -g led_on.o -o led_on_elf	 连接
        	arm-linux-objcopy -O binary -S led_on_elf led_on.bin	把ELF格式的可执行文件转为bin
        clean:
        	rm -f led_on.bin led_on_elf *.o
   ```
   
   make指令比较第一行中文件led_on.bin和文件led_on.S的时间，如果.S的较新，则编译。
        
   - 实例2：使用C语言代码点亮一个LED
     
     源程序位于/work/hardware/led_on_c目录下。
     
     C语言执行的第一条指令，并不在main函数中。生成C程序的可执行文件时，编译器通常会在代码中加上几个启动文件的代码——crt1.o、crti.o、crtend.o、crtn.o等。它们是标准库文件。这些代码设置C程序的堆栈等，然后调用main函数。它们依赖于操作系统，在裸板上无法执行，所以需要自己写一个。例如：
     
     ```assembly
        @**********************************************************
        @ File: crt0.s
        @ 功能：通过它转入C程序
        @**********************************************************
        
        .text
        .global _start
        _start:
        		ldr 	r0, =0x56000010			@ 09 WATCHDOG寄存器地址
        		mov		r1, #0x0
        		str		r1, [r0]				@ 11 写入0禁止WATCHDOG，否则CPU会不断重启
        		
        		ldr		sp, =1024*4	 @ 13 设置堆栈，不能大于4kb，现在可用内存只有4kb
        							@ 14 NAND Flash中的代码在复位后会移动到内部ram（只有4kb）
        		bl		main		@ 15 调用C程序中的main函数
        halt_loop:
        		b		halt_loop
     ```
     
     控制LED的C程序，main函数在led_on_c.c文件中：
     
     ```c
        #define GPBCON (*(volatile unsigned long *)0x56000010)
        #define GPBDAT (*(volatile unsigned long *)0x56000014)
        
        int main()
        {
        	GPBCON = 0x00000400; // 设置GPB5为输出端口，位[10:9]=0b01 即 0x0400=0b0100 0000 0000
        	GPBDAT = 0x00000000;
        	
        	return 0;
        }
     ```
     
     Makefile:
     
     ```makefile
        led_on.bin : crt0.S led_on_c.c
        	arm-linux-gcc -g -c -o crt0.o crt0.S									编译
        	arm-linux-gcc -g -c -o led_on_c.o led_on_c.c							 编译
        	arm-linux-ld -Ttext 0x00000000 -g crt0.o led_on_c.o -o led_on_c_elf	 	   连接
        	arm-linux-objcopy -O binary -S led_on_c_elf led_on_c.bin 			转为二进制bin
        	arm_linux-objdump -D -m arm led_on_c_elf > led_on_c.dis				转为汇编码
        clean:
        	rm -f led_on_c.dis led_on_c.bin led_on_c_elf *.o
     ```
     
     操作步骤：
     
     - 进入led_on_c目录后，执行make命令，生成led_on_c.bin
        - 使用JTAG工具将led_on_c.bin写入NAND Flash
        - 按开发板上复位键后可看见LED1被点亮了。
     
     目录leds中的程序是使用4个LED从0~15轮流计数，操作步骤：
     
     - 进入leds目录执行make
     
     - 烧写程序到NAND Flash
     
       ```shell
          $ sjf2410.exe /f:leds.bin /d=5
          或
          $ sjf2440.exe /f:leds.bin /d=5
       ```
     
     - 按开发版复位键运行
     
     - 可以使用如下命令查看二进制可执行文件的汇编码
     
       ```
          $ arm-linux-objdump -D -b binary -m arm xxxxxx(二进制可执行文件名)
       ```
     
   - 实例3：使用按键来控制LED
     
     目录/work/hardware/key_led中的程序功能为：
     
     ​		当K1~K4中某个按键被按下时，点亮LED1~LED4中相应的LED。
     
     key_led.c代码：
     
     ```c
        #define GPBCON	(*(volatile unsigned long *)0x56000010)
        #define GPBDAT	(*(volatile unsigned long *)0x56000014)
        
        #define GPFCON	(*(volatile unsigned long *)0x56000050)
        #define GPFDAT	(*(volatile unsigned long *)0x56000054)
        
        #define GPGCON	(*(volatile unsigned long *)0x56000060)
        #define GPGDAT	(*(volatile unsigned long *)0x56000064)
        
        /*
         * LED1-4对应GPB5、GPB6、GPB7、GPB8
         * 输出为0b01
         * 输入为0b00、特殊功能为0b10、保留不用为0b11
         */
        #define GPB5_out	(1<<(5*2))		// [11:10] 0000 0000 0000 0000 0000 0100 0000 0000
        #define GPB6_out	(1<<(6*2))		// [13:12] 0000 0000 0000 0000 0001 0000 0000 0000
        #define GPB7_out	(1<<(7*2))		// [15:14] 0000 0000 0000 0000 0100 0000 0000 0000
        #define GPB8_out	(1<<(8*2))		// [17:16] 0000 0000 0000 0001 0000 0000 0000 0000
        
        /*
         * K1-K4对应GPG11、GPG3、GPF2、GPF0	
         */
        #define GPG11_in	~(3<<(11*2))	// [23:22] 1111 1111 0011 1111 1111 1111 1111 1111
        #define GPG3_in		~(3<<(3*2))		// [7:6] ... 1111 1111 0011 1111
        #define GPF2_in		~(3<<(2*2))		// [5:4] ... 1111 1111 1100 1111
        #define GPF0_in		~(3<<(0*2))		// [1:0] ... 1111 1111 1111 1100
        
        int main()
        {
            unsigned long dwDat;
            // LED1~LED4对应的4根引脚设为输出
            GPBCON = GPB5_out | GPB6_out | GPB7_out | GPB8_out;
            							// 0000 0000 0000 0001 0101 0100 0000 0000
            							//                  08 0706 05
            
            // K1~K2对应的2根引脚设为输入
            GPGCON =GPG11_in & GPG3_in; 	// 1111 1111 0011 1111 1111 1111 0011 1111
            							  //           11                  03 
            
            // K3~K4对应的2根引脚设为输入
            GPFCON = GPF2_in & GPF0_in;		// 1111 1111 1111 1111 1111 1111 1100 1100
            							  //                                 02   00 
            
            while(1){
                //若Kn为0(表示按下)，则LEDn为0(表示点亮)
                dwDat = GPGDAT;				// 读取GPG管脚电平状态
                
                if(dwDat & (1<<11))			// K1没有按下
                    GPBDAT |= (1<<5);		// LED1熄灭	0b 0001 0000 第5个位置为1，熄灭
                else
                    GPBDAT &= ~(1<<5);		// LED1点亮   0b 1110 1111 第5个位置为0，点亮
                
                if(dwDat & (1<<3))			// K2没有按下
                    GPBDAT |= (1<<6);		// LED2熄灭
                else
                    GPBDAT &= ~(1<<6);		// LED2点亮
                
                dwDat = GPFDAT;				// 读取GPF管脚电平状态
                
                if(dwDat & (1<<2))			// K3没有按下
                    GPBDAT |= (1<<7);		// LED3熄灭
                else
                    GPBDAT &= ~(1<<7);		// LED3点亮
                
                if(dwDat & (1<<0))			// K4没有按下
                    GPBDAT |= (1<<8);		// LED4熄灭
                else
                  GPBDAT &= ~(1<<8);		// LED4点亮
         }
            
            return 0;
        }
     ```
     
        

### 第6章 存储控制器

- 了解S3C2410/S3C2440地址空间的布局

- 掌握如何通过总线形式访问扩展的外设，比如内存、NOR Flash、网卡等

  总线的使用方法是嵌入式底层开发的基础，了解它之后，再根据外设的具体特性，就可以驱动该外设了。

1. 使用存储控制器访问外设的原理
   
   1. S3C2410/S3C2440的地址空间
   
      - 每个BANK空间为128M，总共1GB（8BANKs)。物理地址从0x000000~0x40000000
      - BANK0~BANK5可以支持外接ROM、SRAM等，BANK6~BANK7还可额外支持SDRAM等
      - BANK6、BANK7的地址空间大小可编程控制
   
      S3C2410/S3C2440对外引出27根地址线ADDR0~ADDR26访问128MB空间。CPU对外还引出8根片选信号nGCS0~nGCS7，对应于BANK0~BANK7，nGCSx引脚输出低电平，表示访问BANKx地址空间。
   
      > 通过设置OM1、OM0引脚，可以选择从NAND Flash启动还是从NOR Flash启动
   
      OM[1:0]=01,10时，从NOR Flash启动（不接NAND_BOOT跳线）
   
      OM[1:0]=00，从NAND Flash启动（接上NAND_BOOT跳线）
   
      ![1566723469005](/1566723469005.png)
   
      S3C2410/S3C2440的寄存器地址范围都处于0x4800000~0x5FFFFFFF（见功能地址部件的寄存器地址范围对照表6.1）
   
      ![1566723257533](/1566723257533.png)
   
      ![1566723293360](/1566723293360.png)
   
   2. 存储控制器和外设的关系
   
      存储控制器的各BANK，可以分别外接各种设备：NOR Flash、IDE接口、10M网卡CS8900、100M网卡DM9000、扩展串口芯片16C2550、SDRAM等。
   
      外设访问地址确定：各BANK起始地址+地址线
   
      举例：
   
      - BANK5连接扩展串口芯片16C2550，扩展串口A、B的外设的访问地址
      - BANK6连接两片K4S561632C的SDRAM外设，访问地址
   
      举例所有外设访问地址：
   
      ![1566731426413](/1566731426413.png)
   
   3. 存储控制器的寄存器使用方法
   
      存储控制器共13个寄存器。
   
      BANK0~BANK5只需要设置 **BWSCON** 和 **BACKCONx** (x为0~5)两个寄存器；
   
      BANK6、BANK7外接SDRAM时，除 BWSCON 和 BACKCONx 外，还要设置 **REFRESH**、**BANKSIZE**、**MRSRB6**、**MRSRB7** 四个寄存器。
   
      - 位宽和等待控制寄存器 BWSCON (BUS WIDTH & WAIT CONTROL REGISTER)
   
        每四位控制一个BANK，从BANK7 ... BANK0
   
        - STx：启动/禁止 SDRAM 的数据掩码引脚，SDRAM为0；SRAM为1
        - WSx：是否使用存储器的WAIT信号，通常为0
        - DWx：(两位)设置BANK的位宽(Bus Width)
          - 0b00——8位
          - 0b01——16位
          - 0b10——32位
          - 0b11——保留
   
        BANK0，没有ST0和WS0，DW0([2:1])只读——由硬件跳线决定：0b01(16位宽)，0b10(32位宽)。
   
        根据表6.2确定BWSCON寄存器的值为：
   
        ​					0x22011110
   
      - BANK控制寄存器BANKCONx(BANK CONTROL REGISTER, x为0~5)
   
        控制BANK0~BANK5外接设备的访问时序，使用默认的0x0700
   
      - BANK控制寄存器BANKCONx（BANK CONTROL REGISTER, x为6~7)
   
        由于BANK6和BANK7可以外接SDRAM，所有与前6个不同
   
        当MT([16:15])=0b00时，与前6片相同
   
        当MT=0b11时，本BANK外接SDRAM
   
        - Trcd([3:2])：RAS to CAS delay，推荐0b01
   
        - SCAN([1:0])：SDRAM的列地址位数。0b00为8位， 0b01为9位， 0b10为10位。
   
          查看手册，本开发版用的SDRAM K4S561632，列地址位数为9，所以SCAN=0b01
   
      - 刷新控制寄存器 REFRESH(Refresh control register)
   
        设为 0x008C0000 + R_CNT
   
        - REFEN([23])：禁止SDRAM刷新=0，开启SDRAM刷新=1
   
        - TREFMD([22])：SDRAM刷新模式，0=CBR/Auto Refresh，1=Self Refresh(系统休眠时使用)
   
        - Trp([21:20])：=0
   
        - Tsrc([19:18])：=0b11
   
        - Refresh Count([10:0])：即R_CNT
   
          **R_CNT = 2[^11] +1 - SDRAM时钟频率 &times; SDRAM刷新周期(uS)**
   
          SDRAM刷新周期在SDRAM数据手册上有标明，如"64ms frfresh period(8K Cycle)"。所以，刷新周期 = 64ms/8192 = 7.8125uS
   
          未使用PLL是，SDRAM的时钟频率等于晶振频率 12MHz
   
          => R_CNT = 2[^11]+1-12&times;7.8125=1955。
   
        => 在未使用PLL时，REFRESH=0x008C0000+1955 = 0x008C07A3
   
      - BANKSIZE寄存器REFRESH
   
        本开发版为0xB1
   
      - SDRAM模式设置寄存器MRSRBx(SDRAM MODE REGISTER SET REGISTER，x为6，7)
   
        修改位为CL([6:4])，这是SDRAM时序的一个时间参数：
   
        [work]0b000 =1 clock, 0b010 =2 clocks, 0b011=3 clocks
   
        本开发版取0b11，所以MRSRB6/7的值为 0x30
   
      
   
2. 存储控制器操作实例：使用SDRAM

   1. 代码详解及程序的复制、跳转过程

      从NAND Flash启动CPU时，CPU会通过内部的硬件将NAND Flash开始的4KB数据复制到称为"Steppingstone"的4KB的内部RAM中(起始地址为0)，然后跳到地址0开始执行。

      **本例实现思路**：

      - 先使用汇编语言设置好存储控制器，使外接的SDRAM可用
      - 然后把程序本身从Steppingstone复制到SDRAM处
      - 最后跳到SDRAM中执行

      (源代码/work/hardware/sdram中，包含两个文件head.S、leds.c。leds.c和前一章一样，是让4个LED从0~15轮流计数)

      ```assembly
      @******************************************************************
      @ File： head.S
      @ 功能：设置SDRAM，将程序复制到SDRAM，然后跳到SDRAM继续执行
      @******************************************************************
      
      .equ		MEM_CRL_BASE,	0x48000000	@ CPU 存储控制器13个寄存器起始地址
      .equ		SDRAM_BASE,		0x30000000  @ 外设SDRAM 起始地址
      
      .text
      global _start
      _start:
      	b1	disable_watch_dog
      	b1	memsetup
      	b1	copy_steppingstone_to_sdram
      	ldr	pc, =on_sdram
      on_sdram:
      	ldr	sp, =0x34000000
      	bl	main
      halt_loop:
      	b	halt_loop
      	
      disable_watch_dog:					@ 关闭 WATCHDOG，否则CPU会不断重启
      	@ 往WATCHDOG寄存器写0即可
      	mov	r1,	#0x53000000
      	mov	r2,	#0x0
      	str	r2,	[r1]
      	mov	pc,	lr				@ 返回
      	
      copy_steppingstone_to_sdram:
      	@ 将 Steppingstone 的 4KB 数据全部复制到 SDRAM 中去
      	@ Steppingstone 起始地址为 0x00000000，SDRAM中起始地址为0x30000000
      	
      	mov	r1,	#0
      	ldr	r2,	=SDRAM_BASE
      	mov	r3,	#4*1024
      1:
      	ldr	r4,	[r1], #4
      	str	r4,	[r2], #4
      	cmp	r1,	r3
      	bne	1b					@40 若没复制完，跳转回去继续复制 ？？？
      	mov	pc,	lr
      	
      memsetup:
      	@ 设置存储控制器以便使用SDRAM等外设
      	
      	mov	r1,	#MEM_CTL_BASE
      	adr1	r2,	mem_cfg_val			@47 13个寄存器值的起始存储地址
      	add	r3,	r1, #52
      1:
      	ldr	r4,	[r2], #4
      	ldr	r4,	[r1], #4
      	cmp	r1,	r3
      	bne	1b
      	mov	pc,	lr
      
      .align	4
      mem_cfg_val:
      	@ 存储控制器13个寄存器的设置值
      	.long	0x22011110			@60 BWSCON
      	.long	0x00000700			@61 BANKCON0
      	.long	0x00000700			@62 BANKCON1
      	.long	0x00000700			@63 BANKCON2
      	.long	0x00000700			@64 BANKCON3
      	.long	0x00000700			@65 BANKCON4
      	.long	0x00000700			@66 BANKCON5
      	.long	0x00018005			@67 BANKCON6
      	.long	0x00018005			@68 BANKCON7
      	
      ```
      


### 第7章 内存管理单元MMU

- 了解虚拟地址和物理地址的关系
- 掌握如何通过设置MMU来控制虚拟地址到物理地址的转化
- 了解MMU的内存访问权限机制
- 了解TLB、Cache、Write buffer的原理，使用时的注意事项
- 通过实例深刻掌握上述要点

1. 内存管理单元MMU介绍

   1. S3C2410/S3C2440 MMU特性

      内存管理单元(**Memory Management Unit**, MMU)负责虚拟地址到物理地址的映射，并提供硬件机制的内存访问权限检查。

      各用户进程都拥有自己独立的地址空间：

      - 地址映射功能：使得各进程拥有"看起来"一样的地址空间
      - 内存访问权限检查：可以保护每个进程所拥有的内存，不会被其它进程破坏

      S3C2410/S3C2440特性：。。。

      本章重点在于地址映射：

      - 页表的结构与建立
      - 映射的过程
      - TLB、访问权限、Cache粗略介绍

   2. S3C2410/S3C2440 MMU地址变换过程

      - 地址的分类

        虚拟存储器从逻辑上对内存容量进行了扩充，虚拟内存地址范围为0~0xFFFFFFFF，称为**虚拟地址**空间。

        与之相对应的是物理地址空间和**物理地址**，即**实际地址**。

        虚拟地址需要转换为物理地址才能读写实际的数据。这通过将虚拟地址空间、物理地址空间划分为同样大小的一块块空间（称为 **段** 或 **页**），然后为**这两类空间建立映射**关系。

        *<u>虚拟地址空间>>--远远大于-->>物理地址空间</u>*。

        ARM CPU上的地址转换过程涉及3个概念：

        - 虚拟地址(VA, Virtual Address)
        - 变换后的虚拟地址(MVA, Modified Virtual Address)
        - 物理地址(PA, Physical Address)

        没启动MMU时，CPU核、catch、MMU、外设等所有部件使用的都是物理地址。

        启动MMU后：

        - CPU核对外发出虚拟地址VA；
        - VA被转换为MVA供cache、MMU使用，在这里MVA被转换为PA；
        - 最后使用PA读写实际设备(S3C2410/S3C2440内部寄存器或外接的设备)：

        (1) CPU核看到的、用到的只是虚拟地址VA

        (2) 而caches和MMU看不见VA，它们利用有**MVA转换得到的PA**

        (3) 实际设备看不到VA、MVA，读写它们的时候使用的是物理地址PA

        MVA是除CPU核之外的其它部分看到的虚拟地址，**VA——>MVA变换**关系如下：

        ![1566819978494](/1566819978494.png)

        如果VA<32M，需要使用进程标识号PID(通过读CP15的C13获得)来转换为MVA，转换**由硬件自动完成**。

      - **虚拟地址**到物理地址的转换（此后的虚拟地址，如无特别指出，就是指**MVA**）

        虚拟地址->物理地址方法：

        - 用一个确定的数学公式进行转换
        - 用表格存储虚拟地址对应的物理地址：这类表格称为**页表（Page table）**，页表由**条目（Entry）**组成；**每个条目存储了一段虚拟地址对应的<u>物理地址</u>及其<u>访问权限</u>，或下一级页表的地址**。ARM CPU用此方法。

        S3C2410/S3C2440<u>*最多用到两级页表*</u>：以**段（Section，1MB）**的方式进行转换时只用到一级页表，以**页（Page）**的方式进行转换时用到两级页表。

        页的大小有3种：**大页（64K）**，**小页（4K）**、**极小页（1K）**。

        条目(Entry)也称为描述符（Descriptor）：段描述符、大页描述符、小页描述符、极小页描述符——它们保存段、大页、小页、极小页的起始物理地址

        粗页表描述符、细页表描述符——它们保存二级页表的物理地址

        转换过程如下，图7.3、图7.4：

        - 根据给定的虚拟地址找到一级页表中的条目
        - 如果此条目是段描述符，则返回物理地址，转换结束
        - 否则如果此条目是二级描述符，则继续在此二级表中找下一个条目
        - 如果这第二个条目是页描述符，则返回物理地址，转换结束
        - 其它情况出错

        ![1566822308041](/1566822308041.png)

        ![1566822397805](/1566822397805.png)
        
        ​			
      
   3. 内存的访问权限检查
   
      内存的访问权限检查是MMU的主要功能之一，它决定了一块内存是否允许读、是否允许写。
   
      由CP15**寄存器C3**（域访问控制器）、**描述符**的**域**（Domain）、CP15**寄存器C1**的**R/S/A位**、**描述符**的**AP位**等联合作用。
   
      CP15寄存器C1中的A位表示是否进行对齐检查。对齐检查在MMU的权限检查、地址映射前进行。
   
      - “域”决定是否对某块内存进行权限检查
      - “AP”决定如何对模块内存进行权限检查
   
      S3C2410/S3C2440有16个域，CP15寄存器C3中每两位对应一个域
   
      ![1567071053992](/1567071053992.png)
      
       ![1567074967094](/1567074967094.png)
      
      图7.13中的"Domain"占据4字节，用来表示这块内存属于图7.12中的哪个域，举例如下：
      
      - 段描述符中的"Domain"为0b0000时，表示这1MB内存属于域0。如果域访问控制寄存器的位[1:0]等于0b00，则访问这1MB空间时都会产生"Domain fault"的异常，如果域访问控制寄存器的位[1:0]等于0b11，则使用描述符中的`AP`位进行权限检查。
      - 粗页表中的Domain为0b1111时，表示这1MB内存属于域15，如果域访问控制寄存器的位[31:30]等于0b00，则访问这1MB空间时都会产生"Domain fault"的异常，如果域访问控制寄存器的位[31:30]等于0b11，则使用二级页表中的大页/小页描述符中的"ap3"、"ap2"、"ap1"、"ap0"位进行权限检查。
      
      图7.13中的"AP"、"ap3"、"ap2"、"ap1"、"ap0"结合CP15寄存器C1的R/S位，决定如何进行访问权限检查。
      
      - 段描述符中的AP，控制整个段（1MB）的访问权限
      - 大页描述符中的每个 apx（x为0~3）控制一个大页（64kB）中的1/4内存的访问权限，即"ap3"对应大页高端的16k，"ap1"对应大页低端的16k
      - 小页描述符与大页相似，每个apx控制一个小页（4kb）的1/4内存访问权限
      - 极小页中的ap就控制整个极小页（1kb）的访问权限
      
      表7.2所示，AP位、S位和R位的组合，可以产生多种权限。ARM CPU有7中工作模式，其中6种属于特权模式，1种属于用户模式。在特权模式和用户模式下，相同的AP位、S位和R位的组合，其访问权限也不同。
      
      ![1567077513683](/1567077513683.png)
      
   4. TLB的作用
   
      通过使用一个高速、容量相对较小的存储器来存储近期用到的页表条目，以避免每次地址转换时都到主存去查找，这样可以大幅度提高性能。这个存储器用来帮助快速的进行地址转换，称为“转义查找缓存”（Translation Lookaside Buffers，TLB）
   
   5. Cache的作用
   
      同样基于程序访问的局部性，在主存和CPU通用寄存器之间设置一个高速的、容量相对较小的存储器，把正在执行的指令地址附近的一部分指令或数据从主存调入这个存储器，供cpu在一段时间使用。
   
      这个介于主存和CPU之间的高速小容量存储器称作**高速缓冲存储器**（Cache）。
   
      工作方式，CPU读取数据时
   
      - 如果Cache中有这个数据的副本则直接返回
      - 否则从主存中读入数据，并存入Cache中，下次在使用（读写）这个数据时，可以直接使用Cache中的副本。
   
      启用Cache后，CPU写数据时：
   
      - 写穿式（Write Through）：写完Cache，写主存
      - 回写式（Write back）：写Cache，不写主存。直到Cache中数据被换出或强制进行“清空”操作时，才写入主存。
   
      Cache的两个操作：
   
      - 清空（clean）：把Cache或Write buffer中已经脏的（修改过，但未写入主存）数据写入主存
      - 使无效（Invalidate）：使之不能再使用，并不将脏数据写入主存。
   
      S3C2410/S3C2440内置了指令Cache（ICaches）、数据Cache（DCaches）、写缓存（Write buffer）。下面需要用到页描述符中的C、B。
   
      ![1567080048485](/1567080048485.png)
   
      - 指令Cache（ICaches）
   
        系统上电或复位。ICache中的内容无效，功能关闭。往Icr位（即CP15协处理器中寄存器1的第12位）写1，启动ICaches，写0停止ICaches。
   
        ICaches在MMU开启后被使用，此时页表描述符的C位（Ctt）用来表示一段内存是否可以被Cache（=1或0）
   
        如果不开启，则从主存读取数据，性能很低，因此应尽早开启。
   
      - 数据Cache（DCAches）
   
        系统上电或复位。ICache中的内容无效，功能关闭。Write buffer中的内容也被废弃不用。往Ccr（CP15协处理器中寄存器1的第2位）写1，启动DCaches，写0停止DCaches。Write buffer与DCaches紧密结合，没有专门的控制位来开启、停止它。
   
        与TLB类似，使用Cache是需要保证Cache、Write buffer的内容和主存内容保持一致
   
   6. S3C2410/S3C2440 MMU、TLB、Cache的控制指令
   
      S3C2410/S3C2440中，除了有一个ARM920T的CPU核外，还有若干个协处理器。协处理器也是一个微处理器，它被用来帮助主CPU完成一些特殊功能，比如浮点计算等。
   
      对MMU、TLB、Cache等的操作就涉及协处理器。
   
      CPU核与协处理器间传送数据时使用这两条指令：MRC 和 MCR
   
      ```
      <MCR|MRC>{cond}	p#,<expression1>,Rd,cn,cm{,<expression2>}
      MRC		//从协处理器获得数据，传给ARM920T CPU核的寄存器
      MCR		//数据从ARM920T CPU核的寄存器传给协处理器
      {cond}	//执行条件，省略时表示无条件执行
      p#		//协处理器序号
      <expression1>	//一个常数
      Rd				//ARM920T CPU核的寄存器
      cn和cm			//协处理器中的寄存器
      <expression2>	//一个常数
      ```
   
2. MMU使用实例：地址映射

   1. 程序设计

      程序源码位于/work/hardware/mmu目录下。

      **SDRAM的物理地址**范围处于**0x30000000~0x33FFFFFF**，

      S3C2410/S3C2440的**寄存器地址**处于**0x48000000~0x5FFFFFFF**。

      在第5章中，通过往**GPBCON和GPBDAT**这两个**寄存器的物理地址0x56000010、x56000014**写入特定的数据来驱动4个LED。

      本实例将开启MMU，并将**虚拟地址空间0xA0000000~0xA0100000映射到物理地址空间0x56000000~0x56100000**上，这样，就可以通过操作地址**0xA0000010、0xA0000014**来达到驱动这4个LED的同样效果。

      另外，将**虚拟地址空间0xB0000000~0xB3FFFFFF映射到物理地址空间0x30000000~0x33FFFFFF**上，并在连接程序时将一部分代码的**运行地址指定为0xB0004000**，看看能否令程序跳转到0xB0004000处执行。

      本章只使用一级页表，以段的方式进行地址映射。32位CPU的虚拟地址空间达到4GB，一级页表使用4096个描述符来表示这个4GB空间（每个描述符对应1MB的虚拟地址），每个描述符占用4字节，所以一级页表占16KB。

      本实例使用SDRAM的开始16KB来存放一级页表，所以剩下的内存开始物理地址为0x30004000。
   
   将程序代码分为两部分：
   
   - 第一部分的运行地址设为0，它用来初始化SDRAM、复制第二部分的代码到SDRAM中（存放在0x30004000开始处）、设置页表、启动MMU，最后跳到SDRAM中（地址0xB0004000）去继续执行
      - 第二部分的运行地址设为0xB0004000，它用来驱动LED

      程序流程图如下：

      ![1567083438830](/1567083438830.png)

   2. 代码详解

      程序源码分3个文件：head.S、init.c、leds.c
   
      - head.S代码详解
   
        ```
        01 @*************************************************************************
        02  @ File： head.S
        03  @ 功能：设置SDRAM，将第二部分代码复制到SDRAM，设置页表，启动MMU，
        04  @	   然后跳到SDRAM继续执行
        05  @*************************************************************************
        06  
        07  .text
        08  .global	_start
        09  _start:
        10  	ldr	sp, =4096				@ 设置栈指针，以下都是C函数，调用前需设置好栈
        11  	bl	diaable_watch_dog		@关闭WATCHDOG，否则CPU
        
        
        12  	bl	memsetup
        13  	bl	copy_2th_to_sdram
        14  	bl	create_page_table
        15  	bl	mmu_init
        16  	ldr	sp, =0xB4000000
        17  	ldr	pc, =0xB0004000
     18  halt_loop:
        19  	b	halt_loop
        20  
        ```
   
        











