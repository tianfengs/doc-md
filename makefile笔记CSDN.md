# Makefile学习教程：跟我一起写Makefile

### 0 Makefile概述

make工具，是一个解释makefile中指令的命令工具。例如：Delphi的make， Visual C++的nmake， Linu下得GNU的make。

本文测试环境：RedHat Linux 8.0，make版本3.80。遵循IEEE 1003.2-1992b标准的（POSIX.2)。编译器使用gcc和cc。

#### 0.1 关于程序的编译和链接

 程序编译的基本规范和方法：

- 编译(compile)

  c、c++、pas ——> .obj(Windows)或.o(unix)，即object文件

- 链接(link)

  .obj或.o ——> 可执行文件

object为中间文件(中间目标文件)

很多时候，中间目标文件太多，链接的时候要明确的指明中间文件，这很不方便，因此要给中间目标文件打个包：

- windows下，这个包叫"库文件"(Library File)，也就是.lib文件
- UNIX下，是Archive File，也就是.a文件

在编译时，编译器只检测语法，和函数、变量是否被声明。如果函数未声明，只会给出一个警告。

在连接时，链接器会在所有Object File中找寻函数的实现，如果找不到，会报链接错误(Linker Error)，在VC中，一般是：Link 2001错误



### 1 Makefile 介绍

make执行时，需要一个Makefile文件。

```makefile
edit: main.o kbd.o command.o display.o \
	insert.o search.o files.o utils.o
	gcc -o edit main.o kbd.o command.o display.o \
	insert.o search.o files.o utils.o

main.o: main.c defs.h
	gcc -c main.c
kbd.o: kbd.c defs.h command.h
	gcc -c kbd.c
command.o: command.c defs.h buffer.h
	gcc -c command.c
display.o: display.c defs.h buffer.h
	gcc -c display.c
insert.o: insert.c defs.h buffer.h
	gcc -c insert.c
search.o: search.c defs.h buffer.h
	gcc -c search.c
files.o: files.c defs.h buffer.h command.h
	gcc -c files.c
utils.o: utils.c defs.h
	gcc -c utils.c

clean:
	rm edit main.o kbd.o command.o display.o \
		insert.o search.o files.o utils.o
```

这个makefile文件中

- 目标文件(target)包含：执行文件edit和中间目标文件

- 依赖文件(prerequisites)：冒号后面那些.c和.h文件。

  每一个.o文件都有一组依赖文件，而这些.o文件又是执行文件edit的依赖文件。

- 依赖行下面那行定义了如何生成目标文件的系统命令。一定要以Tab作为开头。

执行这个文件（λ 未Cmder的提示符）

```
λ makeqt
gcc -c main.c
gcc -c kbd.c
gcc -c command.c
gcc -c display.c
gcc -c insert.c
gcc -c search.c
gcc -c files.c
gcc -c utils.c
gcc -o edit main.o kbd.o command.o display.o \
insert.o search.o files.o utils.o
```

反斜杠（\）是换行符。(注：由于make被delphi的IDE占用，此处使用qt的makeqt文件(自己把qt的make改的名字，比较好记))

- 