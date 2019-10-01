Linux运维工程师从0基础到实践 20课（小时）

[TOC]

### 第0章 学习方法和红帽系统

##### 许可证

![Linux就该这么学01](pics\Linux就该这么学01.png)

##### Linux优势

安全性、高可用、高性能、开源性

Linux **PK** Windows

稳定或少许费用

漏洞少且快速修补

多任务多用户

更加安全的用户及文件权限策略（第6章 很复杂）

适合小内核程序的嵌入系统(最小可以做到100M)

相对不耗资源

##### 热门开源系统

redhat（RedHatEnterpriseLinux，RHEL.)

CentOS（RHEL的免费版本）

Fedora红帽用户桌面版（实验田，新鲜和酷的功能）

Debian：服务器版开源操作系统

Ubuntu：Debian的桌面版

**Linux维护内核，可以在 www.kernel.org 网站查看和下载内核代码**

##### 红帽认证

RHCSA（系统管理员）4千2左右，3个小时，300分，210及格。系统的配置，系统管理。

RHCE（工程师）4千2左右，3个小时。安装和配置服务，网络设备、安全，实际问题的诊断和总结。

RHCA（架构师）1万9-2万1

RHCSS（安全专家）

### 第一章 部署虚拟环境安装Linux系统

环境：(下载地址：www.linuxprobe.com/tools)

VmwareWrokStation 11.0

RedHatEnterpriseLinux[RHEL]7.0

Hash1.0.4

内存500m以上，硬盘20G以上

##### 1.5 从grub引导里重置root密码

重启客户机

选第一项，点击e

移动到linux16那一行，段最后输入

```
rd.break
```

ctrl+x

mount -o remount,rw /sysroot     	#重新挂载根目录，并给它可读可写权限

chroot /sysroot					#切换根目录到/sysroot

echo "linuxprobe" | passwd --stdin root	#将root密码修改为：linuxprobe

touch /.autorelabel				# 如果开启了selinux，必须创建更新该目录。

​								# 修改此文件的三个时间戳为当前时间

（如出现**实心方块**乱码，需要输入 LANG=en）

exit								#从根分区中，退到小系统中

reboot

```
**centOS7进入单用户模式的；另一种方法：**
(不需要像这里进入后重新挂载并赋予读写权限，而是在进入单用户模式前，就设定好rw读写权限)
1.开机的时候按方向键，出现选择菜单
2.选择第一个，按下e进入编辑
3.找到ro ，并且把它修改成rw init=/sysroot/bin/sh，完成之后按Ctrl+x
然后成功进入到单用户模式
```

##### 1.5.5 下载vnc客户端，远程登录

##### 1.5.6 配置一下本机的yum仓库

```
[root@localhost Desktop]#vim /etc/yum.repos.d/rhel.repo
​```
[rhel]							yum仓库唯一标识符，避免与其他仓库冲突
name=rhel						yum仓库的名称描述，易于识别仓库用处。
baseurl=file:///media/cdrom		提供方式包括FTP（ftp://..）、HTTP（http://..）、本地（file:///..）
enabled=1						设置此源是否可用，1为可用，0为禁用。
gpgcheck=0						设置此源是否校验文件，1为校验，0为不校验。
gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release 若为校验请指定公钥文件地址。
​```
df -h
mkdir /media/cdrom
```

```
df -h
umount /dev/sr0
mount /dev/cdrom  /media/cdrom
yum clean all
yum makecache
yum install tigervnc-server
```

清空一下防火墙

```
iptables -F
```

##### 1.5.7 配置一下网卡

nmtui

systemctl restart network

nm-connection-editor

ifconfig 

##### 1.5.8 配置一下vnc服务器

拷贝一份配置文件

cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:3.service

修改40，41行的<User>为linuxprobe

切换到linuxprobe用户

su - linuxprobe

初始化vnc，并设置密码

vncserver

启动vnc服务

exit	#退回到root用户

启动vnc服务：

systemctl start vncserver@:3.server

如果失败，则这样启动：

systemctl start vncserver@:3

##### 1.6 安装虚拟机增强包VMTools里的

##### 1.7 重要的守护进程

##### 1.8 红帽软件包管理器（RPM，Redhat Package Manager）

```
安装软件：rpm -ivh filename.rpm
升级软件：rpm -Uvh filename.rpm
卸载软件：rpm -e filename.rpm
查询软件的描述信息：rpm -qpi filename.rpm
列出软件的文件信息：rpm -1pl filename.rpm
查询文件属于哪个RPM：rpm -qf filename.rpm

rpm  -ivh  软件包（rmp）    				##安装软件，-v显示过程，-h指定加密方式
rpm  -e    软件名                 		##卸载
rpm  -ql   rpm  -q +软件名（精确名称）   ##查询软件生成文件
rpm  -qlp  软件包（rpm）    				##查询软件安装后会生成什么文件
rpm  -qc   软件名          			##查询软件的配置文件名称
rpm  -qd   软件名          			##查询软件的说明文件名称
rpm  -qa                      			##查询系统中安装的所有软件名称
rpm  -qa | grep + 软件名(模糊关键字)   	##查询这个软件是否安装(这里的grep是搜索功能)
rpm  -q    软件名（精确名称）        	##查询这个软件是否安装
rpm  -qf   文件名        				##查看filename属于那个安装包
rpm  -qp   软件包（rpm）   				##查询软件包安装后在系统的名字
rpm  -qi   软件名         				##查询软件信息
rpm  -qip  软件包（rpm）     			##软件包的信息
rpm  -Kv   软件包（rpm）      			##检测安装包是否被篡改
rpm  -qp   软件包（rpm） --scripts     	##检测软件在安装或卸载过程重执行的动作
```

##### 1.9 Yum软件仓库

为解决RPM软件包的相互的依赖关系

##### 本章节的复习题：

a. 为什么建议读者校验下载的系统镜像或工具

为了保证软件包的完整性和安全性。

b. 您知道安装系统中设置的交换分区（**SWAP**）有什么用吗?

将部分硬盘单做虚拟内存使用，当实体内存满载时将部分数据存放在硬盘里。（第6章）

c.RPM机制只有红帽企业系统在使用吗？

不对。RPM机制已经被大众认可，在centos/suse/Fedora等系统中都在使用。

d. Yum仓库有几种提供方式，分别是?

ftp,http,本地模式，一共三种

### 第二章 新手必须掌握的Linux命令

系统内核：负责完成对硬件资源的分配、调度等管理任务，驱动和管理硬件。

Linux系统的图形化管理工具，如逻辑卷管理器LVM，Logical Volume Manger。

###### Shell和Bash

Shell是命令行工具，也称为终端或壳，**充当人与内核(硬件)的翻译官**。红帽系统默认的命令行终端是Bash（Bourne-Again SHell）解释器。

Bash优势：

1. 通过上下键调用过往命令
2. 命令或参数可以用tab补全
3. 强大的批处理脚本
4. 具有实用的环境变量功能。

```
命令名称 【命令参数】 【命令对象】
```

##### man命令

**man**帮助信息页面快捷键：

```
空格键：	  下一页
PaGe down：	下一页
PaGe up：	上一页
home：		首页
end：		尾页
/：			从上到下搜索某个关键词，如“/linux"
?：			从下到上搜索某个关键词，如“?linux”
n：			定位到下一个搜索到的关键词
N：			定位到上一个搜索到的关键词
q：			退出帮助文档
```

**man**命令帮助信息的结构及意义：

```
NAME：			命令的名称
SYNOPSIS：		参数的大致使用方法
DESCRIPTION：	介绍说明
EXAMPLES：		演示（附带简单说明）
OVERVIEW：		概述
DEFAULTS：		默认的功能
OPTIONS：		具体可用选项（带介绍）
ENVIRONMENT：	环境变量
FILES：			用到的文件
SEE ALSO：		相关的资料
HISTORY：		维护历史与联系方式
```

man中命令后面括号里的数字含义

```
1、Standard commands （标准命令）
2、System calls （系统调用）
3、Library functions （库函数）
4、Special devices （设备说明）
5、File formats （文件格式）
6、Games and toys （游戏和娱乐）
7、Miscellaneous （杂项）
8、Administrative Commands （管理员命令）
9 其他（Linux特定的）， 用来存放内核例行程序的文档。
n 新文档， 可能要移到更适合的领域。
o 老文档， 可能会在一段期限内保留。
l 本地文档， 与本特定系统有关的。
```

##### 2.3 常用的系统工作命令

1. echo命令

   ```
   用于在终端输出字符串或变量提取后的值，格式为：echo [字符串 | $变量]
   例如输出一个字符串：
   echo Linuxprobe.Com
   下面，使用 $变量 的方式提取变量SHELL的值，并输出到屏幕
   [root@linuxprobe ~]# echo $SHELL
   /bin/bash
   
   echo $HOSTNAME 输出主机名
   ```

2. date命令

   ```
   用于显示及设置系统时间或日期，格式为：date [选项] [+指定的格式]
   
   ```

   | 参数 | 作用           |      |
   | ---- | -------------- | ---- |
   | %t   | 跳格           |      |
   | %H   | 小时00-23      |      |
   | %I   | 小时00-12      |      |
   | %M   | 分钟           |      |
   | %S   | 秒             |      |
   | %j   | 今年中的第几天 |      |

   ```
   例如，把打包后的文件自动按"年月日”的格式打包成“backup-2017-9-1.tar.gz"
   查看当前时间：
   [root@linuxprobe ~]# date
   Mon Aug 24 16:11:23 CST 2017
   按照“年-月-日 小时:分钟:秒”的格式查看当前系统时间的date命令如下所示：
   [root@linuxprobe ~]# date "+%Y-%m-%d %H:%M:%S"
   2017-08-24 16:29:12
   将系统的当前时间设置为2017年9月1日8点30分的date命令如下所示：
   [root@linuxprobe ~]# date -s "20170901 8:30:00"
   Fri Sep 1 08:30:00 CST 2017
   date命令中的参数%j可用来查看今天是当年中的第几天。这个参数能够很好地区分备份时间的新旧，即数字越大，越靠近当前时间。该参数的使用方式以及显示结果如下所示。
   [root@linuxprobe ~]# date "+%j"
   244
   
   date "+%Z" 显示时区
   date "+%A" 显示星期几
   date "+%p" 显示上午还是下午
   
   修改设置Linux服务器时区
   命令 ： "tzselect"
   
   复制相应的时区文件，替换系统时区文件；或者创建链接文件：（推荐方式2）
   方式1：复制响应时区文件
   cp /usr/share/zoneinfo/$主时区/$次时区 /etc/localtime
   例如：在设置中国时区使用亚洲/上海（+8）
   cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   方式2：修改链接/etc/locatime的时区文件（推荐）
   [root@db-server ~]# ln /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   ln: creating hard link `/etc/localtime' to `/usr/share/zoneinfo/Asia/Shanghai': File exists	（链接失败，因为已经存在这个链接文件）
   [root@db-server ~]# rm /etc/localtime
   rm: remove regular file `/etc/localtime'? y	（删除链接）
   [root@db-server ~]# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime （重链）
   [root@db-server ~]# date -R	（检测是否成功）
   Mon, 12 Jan 2015 10:56:10 +0800
   ```

3. reboot命令

   ```
   reboot命令用于重启系统，其格式为reboot。
   由于重启计算机这种操作会涉及硬件资源的管理权限，因此默认只能使用root管理员来重启，其命令如下：
   [root@linuxprobe ~]# reboot
   ```

4. poweroff命令

5. wget命令

   ```
   wget命令用于在终端中下载网络文件，格式为：wget [参数] 下载地址
   ```

   | 参数 | 作用                                 |      |
   | ---- | ------------------------------------ | ---- |
   | -b   | 后台下载模式                         |      |
   | -P   | 下载到指定目录                       |      |
   | -t   | 最大尝试次数                         |      |
   | -c   | 断点续传                             |      |
   | -p   | 下载页面内所有资源，包括图片、视频等 |      |
   | -r   | 递归下载                             |      |

   ```
   尝试使用wget命令从本书的配套站点中下载本书的最新pdf格式电子文档，这个文件的完整路径为https://www.linuxprobe.com/docs/LinuxProbe.pdf
   [root@linuxprobe ~]# wget https://www.linuxprobe.com/docs/LinuxProbe.pdf
   
   使用wget命令递归下载www.linuxprobe.com网站内的所有页面数据以及文件，下载完后会自动保存到当前路径下一个名为www.linuxprobe.com的目录中。执行该操作的命令为wget -r -p https://www.linuxprobe.com，该命令的执行结果如下。
   [root@linuxprobe ~]# wget -r -p https://www.linuxprobe.com
   
   ```

6. elinks

   ```
   elinks用于实现一个纯文本界面的浏览器，格式为： elinks [参数] 网址
   首先安装elinks纯文本浏览器：yum install elinks
   使用elinks访问网址：
   elinks www.linuxprobe.com
   ```

7. ps命令

   ```
   ps命令用于查看系统中的进程状态，格式为: ps [参数]
   -a	显示所有进程（包括其他用户的进程）
   -u	用户以及其他详细信息
   -x	显示没有控制终端的进程
   
   在Linux系统中，有5种常见的进程状态，分别为运行、中断、不可中断、僵死与停止，其各自含义如下所示。
   R（运行）：进程正在运行或在运行队列中等待。
   S（中断）：进程处于休眠中，当某个条件形成后或者接收到信号时，则脱离该状态。
   D（不可中断）：进程不响应系统异步信号，即便用kill命令也不能将其中断。
   Z（僵死）：进程已经终止，但进程描述符依然存在, 直到父进程调用wait4()系统函数后将进程释放。
   T（停止）：进程收到停止信号后停止运行。
   
   [root@linuxprobe ~]# ps aux
   ```

8. top命令

   ```
   top命令用于动态地监视进程活动与系统负载等信息，其格式为top。
   top命令相当强大，能够动态地查看系统运维状态，完全将它看作Linux中的“强化版的Windows任务管理器”。
   
   top命令执行结果的前5行为系统整体的统计信息，其所代表的含义如下。
   
   第1行：系统时间、运行时间、登录终端数、系统负载（三个数值分别为1分钟、5分钟、15分钟内的平均值，数值越小意味着负载越低）。
   第2行：进程总数、运行中的进程数、睡眠中的进程数、停止的进程数、僵死的进程数
   第3行：用户占用资源百分比、系统内核占用资源百分比、改变过优先级的进程资源百分比、空闲的资源百分比等。其中数据均为CPU数据并以百分比格式显示，例如“97.1 id”意味着有97.1%的CPU处理器资源处于空闲。
   第4行：物理内存总量、内存使用量、内存空闲量、作为内核缓存的内存量。
   第5行：虚拟内存总量、虚拟内存使用量、虚拟内存空闲量、已被提前加载的内存量。
   ```

9. pidof命令

   ```
   pidof命令用于查询某个指定服务进程的PID值，格式为: pidof [参数] [服务名称]
   每个进程的进程号码值（PID）是唯一的，因此可以通过PID来区分不同的进程。例如，可以使用如下命令来查询本机上sshd服务程序的PID：
   [root@linuxprobe ~]# pidof sshd
   2156
   ```

10. kill命令

    ```
    kill命令用于终止某个指定PID的服务进程，格式为: kill [参数] [进程PID]
    
    接下来，我们使用kill命令把上面用pidof命令查询到的PID所代表的进程终止掉，其命令如下所示。这种操作的效果等同于强制停止sshd服务。
    [root@linuxprobe ~]# kill 2156
    ```

11. killall命令

    ```
    killall命令用于终止某个指定名称的服务所对应的全部进程，格式为：“killall [参数] [服务名称]”。
    
    通常来讲，复杂软件的服务程序会有多个进程协同为用户提供服务，如果逐个去结束这些进程会比较麻烦，此时可以使用killall命令来批量结束某个服务程序带有的全部进程。下面以httpd服务程序为例，来结束其全部进程。由于RHEL7系统默认没有安装httpd服务程序，因此大家此时只需看操作过程和输出结果即可，等学习了相关内容之后再来实践。
    
    [root@linuxprobe ~]# pidof httpd
    13581 13580 13579 13578 13577 13576
    [root@linuxprobe ~]# killall httpd
    [root@linuxprobe ~]# pidof httpd
    [root@linuxprobe ~]# 
    ```

    如果我们在系统终端中执行一个命令后想立即停止它，可以同时按下**Ctrl + C组合键**（生产环境中比较常用的一个快捷键），这样将立即终止该命令的进程。或者，如果有些命令在执行时不断地在屏幕上输出信息，影响到后续命令的输入，则可以在执行命令时在末尾添加上一个**&符号**，这样命令将进入系统后台来执行

##### 2.4 系统状态检测命令

1. ifconfig命令

   ```
   ifconfig命令用于获取网卡配置与网络状态等信息，格式为: ifconfig [网络设备] [参数]
   ```

2. uname命令

   ```
   uname命令用于查看系统内核与系统版本等信息，格式为: uname [-a]
   
   在使用uname命令时，一般会固定搭配上-a参数来完整地查看当前系统的内核名称、主机名、内核发行版本、节点名、系统时间、硬件名称、硬件平台、处理器类型以及操作系统名称等信息。
   [root@linuxprobe ~]# uname -a
   Linux localhost.localdomain 3.10.0-123.el7.x86_64 #1 SMP Mon May 5 11:16:57 EDT 2014 x86_64 x86_64 x86_64 GNU/Linux
   依次是：
   Linux									kernel name, 
   localhost.localdomain					hostname,
   3.10.0-123.el7.x86_64					kernel release,
   #1 SMP Mon May 5 11:16:57 EDT 2014		kernel version,
   x86_64									machine hareware name
   x86_64									processor type 或 unknown
   x86_64									hardware platform 或 unknown
   GNU/Linux								operating system
   
   顺带一提，如果要查看当前系统版本的详细信息，则需要查看redhat-release文件，其命令以及相应的结果如下：
   [root@linuxprobe ~]# cat /etc/redhat-release
   Red Hat Enterprise Linux Server release 7.0 (Maipo)
   ```

3. uptime命令

   ```
   uptime用于查看系统的负载信息。就是top命令的第一行信息
   ```

4. free命令

   ```
   free用于显示当前系统中内存的使用量信息，格式为：free [-h]
   top命令的第4行，第5行
   -m	以兆为单位显示
   ```

5. who命令

   ```
   who用于查看当前登入主机的用户终端信息，格式为“who [参数]”。
   ```

6. last命令

   ```
   last命令用于查看所有系统的登录记录，格式为“last [参数]”。
   
   使用last命令可以查看本机的登录记录。但是，由于这些信息都是以日志文件的形式保存在系统中，因此黑客可以很容易地对内容进行篡改。千万不要单纯以该命令的输出信息而判断系统有无被恶意入侵！
   ```

7. history命令

   ```
   history命令用于显示历史执行过的命令，格式为“history [-c]”。
   
   history命令应该是作者最喜欢的命令。执行history命令能显示出当前用户在本地计算机中执行过的最近1000条命令记录。如果觉得1000不够用，还可以自定义/etc/profile文件中的HISTSIZE变量值。
   cat /etc/profile|grep HISTSIZE
   
   在使用history命令时，如果使用-c参数则会清空所有的命令历史记录。
   [root@linuxprobe ~]# history -c
   
   还可以使用“!编码数字”的方式来重复执行某一次的命令。
   [root@linuxprobe ~]# history
   1 tar xzvf VMwareTools-9.9.0-2304977.tar.gz
   2 cd vmware-tools-distrib/
   3 ls
   4 ./vmware-install.pl -d
   [root@linuxprobe ~]# !3
   
   历史命令会被保存到用户家目录中的.bash_history文件中。Linux系统中以点（.）开头的文件均代表隐藏文件，这些文件大多数为系统服务文件，可以用cat命令查看其文件内容。
   [root@linuxprobe ~]# cat ~/.bash_history
   
   总之，history命令有很多有趣的玩法等待您去开发。
   ```

8. sosreport命令

   ```
   sosreport命令用于收集系统配置及架构信息并输出诊断文档
   
   当Linux系统出现故障需要联系技术支持人员时，大多数时候都要先使用这个命令来简单收集系统的运行状态和服务配置信息，以便让技术支持人员能够远程解决一些小问题，亦或让他们能提前了解某些复杂问题。在下面的输出信息中，加粗的部分是收集好的资料压缩文件以及校验码，将其发送给技术支持人员即可
   ```

##### 2.5 工作目录切换命令

1. pwd命令

   ```
   -P	显示真实目录（即非快捷连接的地址）
   ```

2. cd命令

   ```
   -	   		切换到上一次的目录
   ~			切换到Home目录
   ~username	切换到其它用户的Home目录
   ..			切换到上级目录
   ```

3. ls命令

   ```
   ls命令用于显示目录中的文件信息，格式为“ls [选项] [文件] ”。
   
   所处的工作目录不同，当前工作目录下的文件肯定也不同。使用ls命令的“-a”参数看到全部文件（包括隐藏文件），使用“-l”参数可以查看文件的属性、大小等详细信息。将这两个参数整合之后，再执行ls命令即可查看当前目录中的所有文件并输出这些文件的属性信息
   -a
   -l
   -d		仅看目录本身
   -h		用k，m，g显示容量
   ```

##### 2.6 文本文件编辑命令

1. cat命令

   ```
   cat命令用于查看纯文本文件（内容较少的），格式为：cat [选项] [文件]
   
   如果在查看文本内容时还想顺便显示行号的话，不妨在cat命令后面追加一个-n参数
   ```

2. more命令

   ```
   more命令用于查看纯文本文件（内容较多的），格式为：more [选项]文件
   
   如果需要阅读长篇小说或者非常长的配置文件，那么“小猫咪”可就真的不适合了。因为一旦使用cat命令阅读长篇的文本内容，信息就会在屏幕上快速翻滚，导致自己还没有来得及看到，内容就已经翻篇了。因此对于长篇的文本内容，推荐使用more命令来查看。more命令会在最下面使用百分比的形式来提示您已经阅读了多少内容。您还可以使用空格键或回车键向下翻页
   ```

3. head命令

   ```
   head命令用于查看纯文本文档的前N行，格式为：head [选项] [文件]
   
   在阅读文本内容时，谁也难以保证会按照从头到尾的顺序往下看完整个文件。如果只想查看文本中前20行的内容，该怎么办呢？head命令可以派上用场了：
   [root@linuxprobe ~]# head -n 20  initial-setup-ks.cfg 
   ```

4. tail命令

   ```
   tail命令用于查看纯文本文档的后N行或持续刷新内容，格式为：tail [选项] [文件]
   ```

5. od命令

   ```
   查看特殊格式的文件，格式为：od [选项] [文件]
   ```

6. tr命令

   ```
   tr命令用于替换文本文件中的字符，格式为“tr [原始字符] [目标字符]”。
   
   在很多时候，我们想要快速地替换文本中的一些词汇，又或者把整个文本内容都进行替换，如果进行手工替换，难免工作量太大，尤其是需要处理大批量的内容时，进行手工替换更是不现实。这时，就可以先使用cat命令读取待处理的文本，然后通过管道符（详见第3章）把这些文本内容传递给tr命令进行替换操作即可。例如，把某个文本内容中的英文全部替换为大写：
   
   [root@linuxprobe ~]# cat anaconda-ks.cfg | tr [a-z] [A-Z]
   ```

7. wc命令

   ```
   wc命令用于统计指定文本的行数、字数、字节数，格式为“wc [参数] 文本”。
   -l	只显示行数
   -w	只显示单词数
   -c	只显示字节数
   
   在Linux系统中，passwd是用于保存系统账户信息的文件，要统计当前系统中有多少个用户，可以使用下面的命令来进行查询，是不是很神奇：
   [root@linuxprobe ~]# wc -l /etc/passwd
   38 /etc/passwd
   
   查看etc文件夹下有多少个文件：
   [root@linuxprobe ~]# ls -l /etc/ | wc -l
   260
   
   less /etc/passwd ??
   ```

8. stat命令

   ```
   stat命令用于查看文件的具体存储信息和时间等信息，格式为“stat 文件名称”。
   
   stat命令可以用于查看文件的存储信息和时间等信息，命令stat anaconda-ks.cfg会显示出文件的三种时间状态（已加粗）：Access、Modify、Change。这三种时间的区别将在下面的touch命令中详细详解
   ```

9. cut命令

   ```
   cut命令用于按“列”提取文本字符，格式为“cut [参数] 文本
   
   按基于“行”的方式来提取数据是比较简单的，只需要设置好要搜索的关键词即可。
   
   如果按列搜索，不仅要使用"-f参数"来设置需要看的列数，还需要使用"-d参数"来设置间隔符号。passwd在保存用户数据信息时，用户信息的每一项值之间是采用冒号来间隔的，接下来我们使用下述命令尝试提取出passwd文件中的用户名信息，即提取以冒号（：）为间隔符号的第一列内容
   -f
   -d
   -c	显示字符
   [root@linuxprobe ~]# head -n 2 /etc/passwd
   root:x:0:0:root:/root:/bin/bash
   bin:x:1:1:bin:/bin:/sbin/nologin
   [root@linuxprobe ~]# cut -d: -f1 /etc/passwd
   root
   bin
   daemon
   adm
   lp
   
   获取root用户的默认shell解释器：
   [root@linuxprobe ~]# less /etc/passwd
   [root@linuxprobe ~]# grep ^root /etc/passwd | cut -d: -f7
   
   用户管理有两个最重要的配置文件，一个是保存用户信息的文件/etc/passwd，一个是保存了用户密码的文件/etc/shadow。
   用户信息文件：/etc/passwd
   用户名：密码：UID：GID：注释性描述：宿主目录：解释器终端
   UID为0的是超级用户root，里面大多为伪用户，uid在1-499之间
   
   用户配置文件：
   https://blog.csdn.net/qq_34556414/article/details/78902662 
   用户信息文件： /etc/passwd
   密码文件： /etc/shadow
   用户组文件：	/etc/group
   用户组密码文件： /etc/gshadow
   用户配置文件：
   	/etc/login.defs
   	/etc/default/useradd
   新用户信息文件； /etc/skel (文件夹？？？)
   登录信息： /etc/motd
   ```

10. diff命令

    ```
    diff命令用于比较多个文本文件的差异，格式为“diff [参数] 文件
    使用--brief参数来确认两个文件是否不同，还可以使用-c参数来详细比较出多个文件的差异之处。
    
    使用diff --brief命令显示比较后的结果，判断文件是否相同：
    [root@linuxprobe ~]# diff --brief diff_A.txt diff_B.txt
    Files diff_A.txt and diff_B.txt differ
    
    最后使用带有-c参数的diff命令来描述文件内容具体的不同：
    [root@linuxprobe ~]# diff -c diff_A.txt diff_B.txt
    ```

##### 2.7 文件目录管理命令

1. touch命令

   ```
   touch命令用于创建空白文件或设置文件的时间，格式为“touch [选项] [文件]
   创建空白的文本文件，touch命令相当简捷。
   对touch命令来讲，有难度的操作主要是体现在设置文件内容的修改时间（mtime）、文件权限或属性的更改时间（ctime）与文件的读取时间（atime）上面。touch命令的参数及其作用如表2-11所示。
   -a	仅修改“读取时间”（atime）
   -m	仅修改“修改时间”（mtime）
   -d	同时修改atime与mtime
   
   接下来，我们先使用ls命令查看一个文件的修改时间，然后修改这个文件，最后再通过touch命令把修改后的文件时间设置成修改之前的时间（很多黑客就是这样做的呢）：
   [root@linuxprobe ~]# ls -l anaconda-ks.cfg 
   -rw-------. 1 root root 1213 May  4 15:44 anaconda-ks.cfg
   [root@linuxprobe ~]# echo "Visit the LinuxProbe.com to learn linux skills" >> anaconda-ks.cfg
   [root@linuxprobe ~]# ls -l anaconda-ks.cfg
   -rw-------. 1 root root 1260 Aug  2 01:26 anaconda-ks.cfg
   [root@linuxprobe ~]# touch -d "2017-05-04 15:44" anaconda-ks.cfg 
   [root@linuxprobe ~]# ls -l anaconda-ks.cfg 
   -rw-------. 1 root root 1260 May  4 15:44 anaconda-ks.cfg
   
   注：三个时间用stat命令查看比较好！！！
   ```

2. mkdir命令

   ```
   mkdir命令用于创建空白的目录，还可以结合-p参数来递归创建出具有嵌套叠层关系的文件目录。
   [root@linuxprobe linuxprobe]# mkdir -p a/b/c/d/e
   -m 755	设置目录权限
   ```

3. cp命令

   ```
   cp命令用于复制文件或目录，格式为“cp [选项] 源文件 目标文件”。
   
   在Linux系统中，复制操作具体分为3种情况：
   - 如果目标文件是目录，则会把源文件复制到该目录中；
   - 如果目标文件也是普通文件，则会询问是否要覆盖它；
   - 如果目标文件不存在，则执行正常的复制操作。
   
   -p	保留原始文件的属性
   -d	若对象为“链接文件”，则保留该“链接文件”的属性
   -r	递归持续复制（用于目录）
   -i	若目标文件存在则询问是否覆盖
   -a	相当于-pdr（p、d、r为上述参数）
   ```

4. mv命令

   ```
   mv命令用于剪切文件或将文件重命名，格式为：mv [选项] 源文件 [目标路径|目标文件名]
   ```

5. rm命令

   ```
   rm命令用于删除文件或目录，格式为“rm [选项] 文件
   -f  强制删除
   -r	删除一个目录
   ```

6. dd命令

   ```
   dd命令用于按照指定大小和个数的数据块来复制文件或转换文件，格式为“dd [参数]
   if	输入的文件名称
   of	输出的文件名称
   bs	设置每个“块”的大小
   count	设置要复制“块”的个数
   
   dd命令是一个比较重要而且比较有特色的一个命令，它能够让用户按照指定大小和个数的数据块来复制文件的内容。当然如果愿意的话，还可以在复制过程中转换其中的数据。
   Linux系统中有一个名为/dev/zero的设备文件，每次在课堂上解释它时都充满哲学理论的色彩。因为这个文件不会占用系统存储空间，但却可以提供无穷无尽的数据，因此可以使用它作为dd命令的输入文件，来生成一个指定大小的文件。
   
   例如我们可以用dd命令从/dev/zero设备文件中取出一个大小为560MB的数据块，然后保存成名为560_file的文件。在理解了这个命令后，以后就能随意创建任意大小的文件了：
   [root@linuxprobe ~]# dd if=/dev/zero of=560_file count=1 bs=560M
   1+0 records in
   1+0 records out
   587202560 bytes (587 MB) copied, 27.1755 s, 21.6 MB/s
   
   dd命令的功能也绝不仅限于复制文件这么简单。如果您想把光驱设备中的光盘制作成iso格式的镜像文件，在Windows系统中需要借助于第三方软件才能做到，但在Linux系统中可以直接使用dd命令来压制出光盘镜像文件，将它变成一个可立即使用的iso镜像：
   [root@linuxprobe ~]# dd if=/dev/cdrom of=RHEL-server-7.0-x86_64-LinuxProbe.Com.iso
   7311360+0 records in
   7311360+0 records out
   3743416320 bytes (3.7 GB) copied, 370.758 s, 10.1 MB/s
   
   bs块大小与count块个数的关系，下面举一个吃货的例子进行解释。假设小明的饭量（即需求）是一个固定的值，用来盛饭的勺子的大小即bs块大小，而用勺子盛饭的次数即count块个数。小明要想吃饱（满足需求），则需要在勺子大小（bs块大小）与用勺子盛饭的次数（count块个数）之间进行平衡。勺子越大，用勺子盛饭的次数就越少。由上可见，bs与count都是用来指定容量的大小，只要能满足需求，可随意组合搭配方式。
   ```

7. file命令

   ```
   file命令用于查看文件的类型，格式为“file 文件名
   
   在Linux系统中，由于文本、目录、设备等所有这些一切都统称为文件，而我们又不能单凭后缀就知道具体的文件类型，这时就需要使用file命令来查看文件类型了。
   [root@linuxprobe ~]# file anaconda-ks.cfg 
   anaconda-ks.cfg: ASCII text
   [root@linuxprobe ~]# file /dev/sda
   /dev/sda: block special
   ```

##### 2.8 打包压缩与搜索命令

1. tar命令

   ```
   tar命令用于对文件进行打包压缩或解压，格式为：tar [选项] [文件]
   Linux下最常用的打包程序就是tar了，使用tar程序打出来的包我们常称为tar包，tar包文件的命令通常都是以.tar结尾的。生成tar包后，就可以用其它的程序来进行压缩了
   -c	创建压缩文件
   -x	解开压缩文件
   -t	查看压缩包内有哪些文件
   -z	用Gzip压缩或解压
   -j	用bzip2压缩或解压
   -v	显示压缩或解压的过程
   -f	目标文件名
   -p	保留原始的权限与属性
   -P	使用绝对路径来压缩
   -C	指定解压到的目录
   
   首先，-c参数用于创建压缩文件，-x参数用于解压文件，因此这两个参数不能同时使用。
   其次，-z参数指定使用Gzip格式来压缩或解压文件，-j参数指定使用bzip2格式来压缩或解压文件，-Z使用Compress来压缩或解压缩
   非常推荐使用-v参数向用户不断显示压缩或解压的过程。
   -C参数用于指定要解压到哪个指定的目录。
   -f参数特别重要，它必须放到参数的最后一位，代表要压缩或解压的软件包名称。
   
   一般使用
   tar -czvf 压缩包名称.tar.gz 
   要打包的目录”命令把指定的文件进行打包压缩；
   相应的解压命令为
   tar -xzvf 压缩包名称.tar.gz
   
   先使用tar命令把/etc目录通过gzip格式进行打包压缩，并把文件命名为etc.tar.gz：
   [root@linuxprobe ~]# tar czvf etc.tar.gz /etc
   接下来将打包后的压缩包文件指定解压到/root/etc目录中（先使用mkdir命令来创建/root/etc目录）：
   [root@linuxprobe ~]# mkdir /root/etc
   [root@linuxprobe ~]# tar xzvf etc.tar.gz -C /root/etc
   ```

2. grep命令

   ```
   grep命令用于在文本中执行关键词搜索，并显示匹配的结果，格式为:grep [选项] [文件]
   -b	将可执行文件(binary)当作文本文件（text）来搜索
   -c	仅显示找到的行数
   -i	忽略大小写
   -n	显示行号
   -v	反向选择——仅列出没有“关键词”的行。
   两个最最常用的参数：-n参数用来显示搜索到信息的行号；-v参数用于反选信息（即没有包含关键词的所有信息行）
   
   在Linux系统中，/etc/passwd文件是保存着所有的用户信息，而一旦用户的登录终端被设置成/sbin/nologin，则不再允许登录系统，因此可以使用grep命令来查找出当前系统中不允许登录系统的所有用户信息：
   [root@linuxprobe ~]# grep /sbin/nologin /etc/passwd
   bin:x:1:1:bin:/bin:/sbin/nologin
   daemon:x:2:2:daemon:/sbin:/sbin/nologin
   ```

3. find命令

   ```
   find命令用于按照指定条件来查找文件，格式为: find [查找路径] 寻找条件 操作
   在Linux系统中，搜索工作一般都是通过find命令来完成的，它可以使用不同的文件特性作为寻找条件（如文件名、大小、修改时间、权限等信息），一旦匹配成功则默认将信息显示到屏幕上
   -name	匹配名称
   -perm	匹配权限（mode为完全匹配，-mode为包含即可）
   -user	匹配所有者
   -group	匹配所有组
   -mtime -n +n	匹配修改内容的时间（-n指n天以内，+n指n天以前）
   -atime -n +n	匹配访问文件的时间（-n指n天以内，+n指n天以前）
   -ctime -n +n	匹配修改文件权限的时间（-n指n天以内，+n指n天以前）
   -nouser	匹配无所有者的文件
   -nogroup	匹配无所有组的文件
   -newer f1 !f2	匹配比文件f1新但比f2旧的文件
   --type b/d/c/p/l/f	匹配文件类型（后面的字幕字母依次表示块设备、目录、字符设备、管道、链接文件、文本文件）
   -size	匹配文件的大小（+50KB为查找超过50KB的文件，而-50KB为查找小于50KB的文件）
   -prune	忽略某个目录
   -exec …… {}\;	后面可跟用于进一步处理搜索结果的命令（下文会有演示）
   
   这里需要重点讲解一下-exec参数重要的作用。这个参数用于把find命令搜索到的结果交由紧随其后的命令作进一步处理，它十分类似于第3章将要讲解的管道符技术，并且由于find命令对参数的特殊要求，因此虽然exec是长格式形式，但依然只需要一个减号（-）。
   
   根据文件系统层次标准（Filesystem Hierarchy Standard）协议，Linux系统中的配置文件会保存到/etc目录中（详见第6章）。如果要想获取到该目录中所有以host开头的文件列表，可以执行如下命令：
   [root@linuxprobe ~]# find /etc -name "host*" -print
   /etc/avahi/hosts
   /etc/host.conf
   /etc/hosts
   
   如果要在整个系统中搜索权限中包括SUID权限的所有文件（详见第5章），只需使用-4000即可：
   [root@linuxprobe ~]# find / -perm -4000 -print
   /usr/bin/fusermount
   /usr/bin/su
   /usr/bin/umount
   /usr/bin/passwd
   /usr/sbin/userhelper
   /usr/sbin/usernetctl
   ………………省略部分输出信息………………
   
   在整个文件系统中找出所有归属于linuxprobe用户的文件并复制到/root/findresults目录。
   该实验的重点是“-exec {}   \;”参数，其中的{}表示find命令搜索出的每一个文件，并且命令的结尾必须是“\;”。完成该实验的具体命令如下：
   [root@linuxprobe ~]# find / -user linuxprobe -exec cp -a {} /root/findresults/ \;
   
   ```

### 文件系统层次结构标准(FHS，Filesystem Hierarchy Standard )(for Linux)-简介


> 文件系统层次结构标准(FHS，Filesystem Hierarchy Standard )(for Linux)-简介
>
> https://blog.csdn.net/qq_29753285/article/details/69791332
>
> https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard
>
> FHS由**linux基金会**维护
>
> **开发者** [Linux基金会](https://en.wikipedia.org/wiki/Linux_Foundation) 
> **初始版本** 1994年2月14日 
> **最新版本** 3.0 (2015年6月3日) 
> **网站** [官网](http://www.linuxfoundation.org/collaborate/workgroups/lsb/fhs) 
>
> - / 主层次结构的根，整个文件系统的根目录
>   - /bin		所有用户在单用户模式中必须具备的二进制命令文件，如cat,ls,cp
>   - /boot      Boot loader文件，如kernels，initrd
>   - /dev        必要的device文件，如/dev/null
>   - /etc         特定主机全系统的配置文件
>     - /etc/opt		保存在/opt/中的插件包的配置文件
>     - /etc/sgml       处理SGML的程序（如catalogs）的配置文件   
>     - /etc/X11         X Window System, version 11的配置文件
>     - /etc/xml         处理xml程序（如catalogs）的配置文件
>   - /home     用户的个人目录，包含保存的文件和个人设置等
>   - /lib           /bin/和/sbin/中二进制文件必须的依赖库
>   - /lib          交替格式基本库。搜索目录是可选的，但如果他们存在，他们有一些要求。
>   - /media    一些可以热插拔的介质（如CD-ROMs）的挂载点
>   - /mnt       临时挂载的文件系统
>   - /opt        可选的应用程序包
>   - /proc      将进程和内核信息以文件形式呈现的虚拟文件系统。在Linux中，与procfs mount（进程文件系统）对应
>   - /root      root用户的个人目录
>   - /run       运行时变量数据：从最后一次启动到现在的信息：例如，当前的登录用户和正在运行的守护进程
>   - /sbin     必备的系统可执行文件，如：fsck，init，route
>   - /srv       本系统提供的特定站点的数据。如：web服务器提供的数据和脚本，FTP服务器提供的数据，VCS的仓库
>   - /tmp     临时文件（和/var/tmp相同）通常重启后清空，并严格限制大小
>   - /usr      只读用户数据的次要层次，包含大部分用户功能和应用
>     - /usr/bin		所有用户的非必要的二进制可执行文件（在单用户中不需要）
>     - /usr/include  标准include文件
>     - /usr/lib           /usr/bin/和/usr/sbin/中二进制的依赖库
>     - /usr/lib          可替代的格式化库（可选）
>     - /usr/local      仅针对当前主机的本地数据的第三个层次。一般包含其他的子目录，如bin/,lib/,share/
>     - /usr/sbin      非必须的系统二进制文件，如：多种网络服务的守护进程
>     - /usr/share    结构独立（共享）的数据
>     - /usr/src         源代码，如：内核的源代码和它的头文件
>     - /usr/X11R6   X Window System, Version 11,Release 6
>   - /var        Variable files：各种在系统运行中，内容会不停改变的文件，如：日志文件，spool files，临时的电子邮件文件
>     - /var/cache	应用缓存数据。由耗时的I/O或计算而生成在此，但这些数据删除时不会造成数据丢失
>     - /var/lib          状态信息。程序运行时会改变的持久化数据，如：数据库，packaging system metadata，etc.
>     - /var/lock       Lock files，追踪当前正在使用的资源的文件
>     - /var/log         Log files,各种日志
>     - /var/mail       Mailbox files。某些发行版放在不推荐使用的/var/spool/mail目录中
>     - /var/opt        用来保存在/opt中的插件包的可变数据
>     - /var/run       运行时可变数据。包含描述系统最后一次启动以来的系统信息数据。FHS 3.0中已经被/run替代，不应提供指向/run的符号连接，以免兼容性倒退
>     - /var/spool    放置等待运行的进程，例如：打印序列和等待发送的邮件序列
>       - /var/spool/mail	不建议使用的邮箱位置，应使用/var/mail
>     - /var/tmp      重启时会被保存的临时数据

##### 本章节的习题

1. 在RHEL 7系统及众多的Linux系统中，最常使用的Shell终端是什么？
2. 执行Linux系统命令时，添加参数的目的是什么？
3. Linux系统命令、命令参数及命令对象之间，普遍应该使用什么来间隔？
4. 请写出用echo命令把SHELL变量值输出到屏幕终端的命令。
5. 简述Linux系统中5种进程的名称及含义。
6. 请尝试使用Linux系统命令关闭PID为5529的服务进程。
7. 使用ifconfig命令查看网络状态信息时，需要重点查看的4项信息分别是什么？
8. 使用uptime命令查看系统负载时，对应的负载数值如果是0.91、0.56、0.32，那么最近15分钟内负载压力最大的是哪个时间段？
9. 使用history命令查看历史命令的执行记录时，命令前面的数字除了排序外还有什么用处？
10. 若想查看的文件具有较长的内容，那么使用cat、more、head、tail中的哪个命令最合适？
11. 在使用mkdir命令创建有嵌套关系的目录时，应该加上什么参数呢？
12. 在使用rm命令删除文件或目录时，可使用哪个参数来避免二次确认呢？
13. 若有一个名为backup.tar.gz的压缩包文件，那么解压的命令应该是什么？
14. 使用grep命令对某个文件进行关键词搜索时，若想要进行文件内容反选，应使用什么参数？

### 第三章 管道符、重定向与环境变量

##### 3.1 输入输出重定向

- 标准**输入**重定向（STDIN，文件描述符为0）：默认从键盘输入，重定向为从其他文件或命令中输入。

- 标准**输出**重定向（STDOUT，文件描述符为1）：默认输出到屏幕，重定向把信息输出到指定文件中。

- 错误**输出**重定向（STDERR，文件描述符为2）：默认输出到屏幕，重定向把信息输出到指定文件中。

输入重定向：

```
命令 < 文件				  将文件作为命令的标准输入
命令 << 分界符			 从标准输入中读入，直到遇见分界符才停止
命令 < 文件1 > 文件2		将文件1作为命令的标准输入并将标准输出到文件2
```

输出重定向：

```
命令 > 文件				  将标准输出重定向到一个文件中（清空原有文件的数据）
命令 2> 文件			  将错误输出重定向到一个文件中（清空原有文件的数据）
命令 >> 文件			  将标准输出重定向到一个文件中（追加到原有内容的后面）
命令 2>> 文件			  将错误输出重定向到一个文件中（追加到原有内容的后面）
命令 >> 文件 2>&1 
或						将标准输出与错误输出共同写入到文件中（追加到原有内容的后面）
命令 &>> 文件	
```

例如：

```
标准输出重定向：
[root@linuxprobe ~]# man bash > readme.txt

追加两行标准输出重定向：
[root@linuxprobe ~]# echo "Welcome to LinuxProbe.Com" > readme.txt
[root@linuxprobe ~]# echo "Quality linux learning materials" >> readme.txt

错误输出重定向：
[root@linuxprobe ~]# ls -l xxxxxx 
cannot access xxxxxx: No such file or directory
[root@linuxprobe ~]# ls -l xxxxxx 2> /root/stderr.txt
[root@linuxprobe ~]# cat /root/stderr.txt 
ls: cannot access xxxxxx: No such file or directory

统计一下文件中的内容行数：
[root@linuxprobe ~]# wc -l < readme.txt
2
相当于：
[root@linuxprobe ~]# cat readme.txt | wc -l
2
```

##### 3.2 管道符命令

格式为“命令A | 命令B”。

**把前一个命令原本要输出到屏幕的标准正常数据当作是后一个命令的标准输入**。

```
找出被限制登录用户的命令是grep "/sbin/nologin" /etc/passwd；
统计文本行数的命令则是wc -l。
[root@linuxprobe ~]# grep "/sbin/nologin" /etc/passwd | wc -l
33

ls列表命令分页显示：
[root@linuxprobe ~]# ls -l /etc/ | more

用一条命令来完成密码重置操作：
[root@linuxprobe ~]# echo "linuxprobe" | passwd --stdin root
Changing password for user root.
passwd: all authentication tokens updated successfully.

结合使用了mail邮件命令与输入重定向的分界符，其目的是让用户一直输入内容，直到用户输入了其自定义的分界符时，才结束输入：（over 为结束分解符）
[root@linuxprobe ~]# mail -s "Readme" root@linuxprobe.com << over
> I think linux is very practical
> I hope to learn more
> can you teach me ?
> over
[root@linuxprobe ~]#
```

##### 3.3 命令行的通配符

通配符就是通用的匹配信息的符号，比如

```
星号（*）代表匹配零个或多个字符
问号（?）代表匹配单个字符
中括号内加上数字[0-9]代表匹配0～9之间的单个数字的字符
中括号内加上字母[abc]则是代表匹配a、b、c三个字符中的任意一个字符

[root@linuxprobe ~]# ls -l /dev/sda*
brw-rw----. 1 root disk 8, 0 May 4 15:55 /dev/sda
brw-rw----. 1 root disk 8, 1 May 4 15:55 /dev/sda1
brw-rw----. 1 root disk 8, 2 May 4 15:55 /dev/sda2

[root@linuxprobe ~]# ls -l /dev/sda?
brw-rw----. 1 root disk 8, 1 May 4 15:55 /dev/sda1
brw-rw----. 1 root disk 8, 2 May 4 15:55 /dev/sda2

[root@linuxprobe ~]# ls -l /dev/sda[0-9]
brw-rw----. 1 root disk 8, 1 May 4 15:55 /dev/sda1
brw-rw----. 1 root disk 8, 2 May 4 15:55 /dev/sda2
[root@linuxprobe ~]# ls -l /dev/sda[135]
brw-rw----. 1 root disk 8, 1 May 4 15:55 /dev/sda1
```

##### 3.4 常用的转义字符

10个转义字符中4个最常用的转义字符如下所示。

```
反斜杠（\）：使反斜杠后面的一个变量变为单纯的字符串。
单引号（''）：转义其中所有的变量为单纯的字符串。
双引号（""）：保留其中的变量属性，不进行转义处理。
反引号（``）：把其中的命令执行后返回结果。
```

```
[root@linuxprobe ~]# PRICE=5
[root@linuxprobe ~]# echo "Price is $PRICE"
Price is 5

希望能够输出“Price is $5”：
[root@linuxprobe ~]# echo "Price is \$$PRICE"
Price is $5

只需要某个命令的输出值时，可以像`命令`这样，将命令用反引号括起来，达到预期的效果。例如，将反引号与uname -a命令结合，然后使用echo命令来查看本机的Linux版本和内核信息：
[root@linuxprobe ~]# echo `uname -a`
Linux linuxprobe.com 3.10.0-123.el7.x86_64 #1 SMP Mon May 5 11:16:57 EDT 2017 x86_64 x86_64 x86_64 GNU/Linux
```

##### 3.5 重要的环境变量

变量是计算机系统用于保存可变值的数据类型。在Linux系统中，变量名称一般都是大写的，这是一种约定俗成的规范。Linux系统中的环境变量是用来定义系统运行环境的一些参数，比如每个用户不同的家目录、邮件存放位置等。

命令在Linux中的执行分为4个步骤：

**第1步**：判断用户是否以绝对路径或相对路径的方式输入命令（如/bin/ls），如果是的话则直接执行。

**第2步**：Linux系统检查用户输入的命令是否为“别名命令”，即用一个自定义的命令名称来替换原本的命令名称。

可以用alias命令来创建一个属于自己的命令别名，格式为“alias 别名=命令”。若要取消一个命令别名，则是用unalias命令，格式为“unalias 别名”。

```
我们之前在使用rm命令删除文件时，Linux系统都会要求我们再确认是否执行删除操作，其实这就是Linux系统为了防止用户误删除文件而特意设置的rm别名命令，接下来我们把它取消掉：
[root@linuxprobe ~]# ls
anaconda-ks.cfg Documents initial-setup-ks.cfg Pictures Templates
Desktop Downloads Music Public Videos
[root@linuxprobe ~]# rm anaconda-ks.cfg 
rm: remove regular file ‘anaconda-ks.cfg’? y
[root@linuxprobe~]# alias rm
alias rm='rm -i'
[root@linuxprobe ~]# unalias rm
[root@linuxprobe ~]# rm initial-setup-ks.cfg 
[root@linuxprobe ~]#
```

**第3步**：Bash解释器判断用户输入的是内部命令还是外部命令。

内部命令是解释器内部的指令，会被直接执行；而用户在绝大部分时间输入的是外部命令，这些命令交由步骤4继续处理。可以使用“type命令名称”来判断用户输入的命令是内部命令还是外部命令。

**第4步**：系统在多个路径中查找用户输入的命令文件，而定义这些路径的变量叫作PATH，可以简单地把它理解成是“解释器的小助手”，作用是告诉Bash解释器待执行的命令可能存放的位置，然后Bash解释器就会乖乖地在这些位置中逐个查找。

```
PATH是由多个路径值组成的变量，每个路径值之间用冒号间隔，对这些路径的增加和删除操作将影响到Bash解释器对Linux命令的查找。
[root@linuxprobe ~]# echo $PATH
/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin
[root@linuxprobe ~]# PATH=$PATH:/root/bin
[root@linuxprobe ~]# echo $PATH
/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/root/bin
```

```
注意：如果你在当前路径下有一个shell脚本例如sh2.sh 这样每次需要通过./sh2.sh 或者 sh sh2.sh 来执行。
通过下面的命令可以将当前路径临时加到环境变量中，然后直接sh2.sh就可以执行了，因为bash可以找到这个shell的路径了
ADD_PATH="$(pwd)" 
PATH="${ADD_PATH}:${PATH}"
export PATH

比较经典的问题：“为什么不能将当前目录（.）添加到PATH中呢? ”
答：尽管可以将当前目录（.）添加到PATH变量中，从而在某些情况下可以让用户免去输入命令所在路径的麻烦。但是，如果黑客在比较常用的公共目录/tmp中存放了一个与ls或cd命令同名的木马文件，而用户又恰巧在公共目录中执行了这些命令，那么就极有可能中招了。

所以，作为一名态度谨慎、有经验的运维人员，在接手了一台Linux系统后一定会在执行命令前先检查PATH变量中是否有可疑的目录
```

我们可以使用env命令来查看到Linux系统中所有的环境变量，而刘遄老师为您精挑细选出了最重要的10个环境变量：

```
HOME			用户的主目录（即家目录）
SHELL			用户在使用的Shell解释器名称
HISTSIZE		输出的历史命令记录条数
HISTFILESIZE	保存的历史命令记录条数
MAIL			邮件保存路径
LANG			系统语言、语系名称
RANDOM			生成一个随机数字
PS1				Bash解释器的提示符
PATH			定义解释器搜索用户执行命令的路径
EDITOR			用户默认的文本编辑器
```

```
相同的变量会因为用户身份的不同而具有不同的值：
[root@linuxprobe ~]# echo $HOME
/root
[root@linuxprobe ~]# su - linuxprobe
Last login: Fri Feb 27 19:49:57 CST 2017 on pts/0
[linuxprobe@linuxprobe ~]$ echo $HOME
/home/linuxprobe

我们完全可以自行创建变量，来满足工作需求。例如设置一个名称为WORKDIR的变量，方便用户更轻松地进入一个层次较深的目录：
[root@linuxprobe ~]# mkdir /home/workdir
[root@linuxprobe ~]# WORKDIR=/home/workdir
[root@linuxprobe ~]# cd $WORKDIR 
[root@linuxprobe workdir]# pwd
/home/workdir

可以使用export命令将其提升为全局变量，这样其他用户也就可以使用它了：
[root@linuxprobe workdir]# su linuxprobe
Last login: Fri Mar 20 20:52:10 CST 2017 on pts/0
[linuxprobe@linuxprobe ~]$ cd $WORKDIR
[linuxprobe@linuxprobe ~]$ echo $WORKDIR
[linuxprobe@linuxprobe ~]$ exit
[root@linuxprobe ~]# export WORKDIR
[root@linuxprobe ~]# su linuxprobe
Last login: Fri Mar 20 21:52:10 CST 2017 on pts/0
[linuxprobe@linuxprobe ~]$ cd $WORKDIR
[linuxprobe@linuxprobe workdir]$ pwd
/home/workdir
```

### 第四章 Vim编辑器与Shell命令脚本

- vim编辑器用法。并通过配置主机名、网卡以及Yum软件仓库参数文件学习命令、快捷键、模式切换方法。

- 把Linux命令、命令语法与Shell脚本中的流程控制语句结合，通过vim写到Shell脚本中，实现自动化工作的脚本文件。

- 通过at命令和crond计划任务服务，实现一次性系统任务设置和长期性的系统任务设置。

“**在[Linux系统](https://www.linuxprobe.com/)中一切都是文件，而配置一个服务就是在修改其配置文件的参数**"

##### 4.1 Vim文本编辑器

Vim编辑器中设置了三种模式—命令模式、末行模式和编辑模式

- 命令模式：控制光标移动，可对文本进行复制、粘贴、删除和查找等工作。

  ```
  dd	删除（剪切）光标所在整行
  5dd	删除（剪切）从光标处开始的5行
  yy	复制光标所在整行
  5yy	复制从光标处开始的5行
  n	显示搜索命令定位到的下一个字符串
  N	显示搜索命令定位到的上一个字符串
  u	撤销上一步的操作
  p	将之前删除（dd）或复制（yy）过的数据粘贴到光标后面
  ```

- 输入模式：正常的文本录入。

- 末行模式：保存或退出文档，以及设置编辑环境。

  ```
  :w	保存
  :q	退出
  :q!	强制退出（放弃对文档的修改内容）
  :wq!	强制保存退出
  :set nu	显示行号
  :set nonu	不显示行号
  :命令	执行该命令
  :整数	跳转到该行
  :s/one/two	将当前光标所在行的第一个one替换成two
  :s/one/two/g	将当前光标所在行的所有one替换成two
  :%s/one/two/g	将全文中的所有one替换成two
  ?字符串	在文本中从下至上搜索该字符串
  /字符串	在文本中从上至下搜索该字符串
  ```

  

![vim不同模式间的切换](pics\vim不同模式间的切换.png)

###### 4.1.1 编写简单文档

###### 4.1.2 配置主机名称

**第1步**：使用Vim编辑器修改“/etc/hostname”主机名称文件。

**第2步**：把原始主机名称删除后追加“linuxprobe.com”。注意，使用Vim编辑器修改主机名称文件后，要在末行模式下执行:wq!命令才能保存并退出文档。

**第3步**：保存并退出文档，然后使用hostname命令检查是否修改成功。

###### 4.1.3 配置网卡信息

在RHEL 5、RHEL 6中，网卡配置文件的前缀为eth，第1块网卡为eth0，第2块网卡为eth1；以此类推。而在RHEL 7中，网卡配置文件的前缀则以ifcfg开始，加上网卡名称共同组成了网卡配置文件的名字，例如ifcfg-eno16777736；除了文件名变化外也没有其他大的区别。

**第1步**：首先切换到/etc/sysconfig/network-scripts目录中（存放着网卡的配置文件）。

**第2步**：使用Vim编辑器修改网卡文件ifcfg-eno16777736，逐项写入下面的配置参数并保存退出。由于每台设备的硬件及架构是不一样的，因此请读者使用ifconfig命令自行确认各自网卡的默认名称。

> 设备类型：TYPE=Ethernet
>
> 地址分配模式：BOOTPROTO=static
>
> 网卡名称：NAME=eno16777736
>
> 是否启动：ONBOOT=yes
>
> IP地址：IPADDR=192.168.10.10
>
> 子网掩码：NETMASK=255.255.255.0
>
> 网关地址：GATEWAY=192.168.10.1
>
> DNS地址：DNS1=192.168.10.1

**第3步**：重启网络服务并测试网络是否联通。

```
[root@linuxprobe network-scripts]# systemctl restart network
```

###### 4.1.4 配置Yum仓库

**第1步**：进入到/etc/yum.repos.d/目录中（因为该目录存放着Yum软件仓库的配置文件）。

**第2步**：使用Vim编辑器创建一个名为rhel7.repo的新配置文件（文件名称可随意，但后缀必须为.repo），逐项写入下面加粗的配置参数并保存退出（不要写后面的中文注释）。

> **[rhel-media]** ：Yum软件仓库唯一标识符，避免与其他仓库冲突。
>
> **name=linuxprobe**：Yum软件仓库的名称描述，易于识别仓库用处。
>
> **baseurl=file:///media/cdrom**：提供的方式包括FTP（ftp://..）、HTTP（http://..）、本地（file:///..）。
>
> **enabled=1**：设置此源是否可用；1为可用，0为禁用。
>
> **gpgcheck=1**：设置此源是否校验文件；1为校验，0为不校验。
>
> **gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release**：若上面参数开启校验，那么请指定公钥文件地址。

**第3步**：按配置参数的路径挂载光盘，并把光盘挂载信息写入到/etc/fstab文件中。

**第4步**：使用“yum install httpd -y”命令检查Yum软件仓库是否已经可用。

##### 4.2 编写Shell脚本

Shell脚本命令的工作方式有两种：交互式和批处理。

> 交互式（Interactive）：用户每输入一条命令就立即执行。
>
> 批处理（Batch）：由用户事先编写好一个完整的Shell脚本，Shell会一次性执行脚本中诸多的命令。

###### 4.2.1 编写简单的脚本

例如，如果想查看当前所在工作路径并列出当前目录下所有的文件及属性信息，实现这个功能的脚本应该类似于下面这样：

```
[root@linuxprobe ~]# vim example.sh
#!/bin/bash 			# 第一行的脚本声明（#!）用来告诉系统使用哪种Shell解释器来执行该脚本；
#For Example BY linuxprobe.com # 第二行的注释信息（#）是对脚本功能和某些命令的介绍信息
pwd 
ls -al
```

执行一下看看结果：

```
[root@linuxprobe ~]# bash example.sh
/root/Desktop
total 8
drwxr-xr-x. 2 root root 23 Jul 23 17:31 .
dr-xr-x---. 14 root root 4096 Jul 23 17:31 ..
-rwxr--r--. 1 root root 55 Jul 23 17:31 example.sh
```

第二种运行脚本程序的方法是通过输入完整路径的方式来执行。但默认会因为权限不足而提示报错信息，此时只需要为脚本文件增加执行权限即可（详见第5章）。：

```
[root@linuxprobe ~]# ./example.sh
bash: ./Example.sh: Permission denied
[root@linuxprobe ~]# chmod u+x example.sh
[root@linuxprobe ~]# ./example.sh
/root/Desktop
total 8
drwxr-xr-x. 2 root root 23 Jul 23 17:31 .
dr-xr-x---. 14 root root 4096 Jul 23 17:31 ..
-rwxr--r--. 1 root root 55 Jul 23 17:31 example.sh
```

###### 4.2.2 接收用户的参数

Linux系统中的Shell脚本语言内设了用于接收参数的变量，变量之间可以使用空格间隔。

$0对应的是当前Shell脚本程序的名称

$#对应的是总共有几个参数

$*对应的是所有位置的参数值

$?对应的是显示上一次命令的执行返回值

$1、$2、$3……则分别对应着第N个位置的参数值，如图所示

![Shell脚本程序中的参数位置变量](pics\Shell脚本程序中的参数位置变量.png)

###### 4.2.3 判断用户的参数

系统在执行mkdir命令时会判断用户输入的信息，即判断用户指定的文件夹名称是否已经存在，如果存在则提示报错；反之则自动创建。Shell脚本中的条件测试语法可以判断表达式是否成立，若条件成立则返回数字0，否则便返回其他随机数值。条件测试语法的执行格式如图4-16所示。切记，条件表达式两边均应有一个空格。

![测试语句格式](pics\测试语句格式.png)

按照测试对象来划分，条件测试语句可以分为4种：

- 文件测试语句；

  ```
  -d	测试文件是否为目录类型
  -e	测试文件是否存在
  -f	判断是否为一般文件
  -r	测试当前用户是否有权限读取
  -w	测试当前用户是否有权限写入
  -x	测试当前用户是否有权限执行
  
  下面使用文件测试语句来判断/etc/fstab是否为一个目录类型的文件，然后通过Shell解释器的内设$?变量显示上一条命令执行后的返回值。如果返回值为0，则目录存在；如果返回值为非零的值，则意味着目录不存在：
  [root@linuxprobe ~]# [ -d /etc/fstab ]
  [root@linuxprobe ~]# echo $?
  1
  ```

- 逻辑测试语句；

  ```
  逻辑语句用于对测试结果进行逻辑分析，根据测试结果可实现不同的效果。例如在Shell终端中逻辑“与”的运算符号是&&，它表示当前面的命令执行成功后才会执行它后面的命令，因此可以用来判断/dev/cdrom文件是否存在，若存在则输出Exist字样。
  [root@linuxprobe ~]# [ -e /dev/cdrom ] && echo "Exist"
  Exist
  
  逻辑“或”，它在Linux系统中的运算符号为||，表示当前面的命令执行失败后才会执行它后面的命令，因此可以用来结合系统环境变量USER来判断当前登录的用户是否为非管理员身份：
  [root@linuxprobe ~]# echo $USER
  root
  [root@linuxprobe ~]# [ $USER = root ] || echo "user"
  [root@linuxprobe ~]# su - linuxprobe 
  [linuxprobe@linuxprobe ~]$ [ $USER = root ] || echo "user"
  user
  
  逻辑“非”-（！），它表示把条件测试中的判断结果取相反值。
  我们现在判断当前用户是否为一个非管理员的用户。由于判断结果因为两次否定而变成正确，因此会正常地输出预设信息：
  [linuxprobe@linuxprobe ~]$ exit
  logout
  [root@linuxprobe root]# [ $USER != root ] || echo "administrator"
  administrator
  
  当前我们正在登录的即为管理员用户—root。下面这个示例的执行顺序是，先判断当前登录用户的USER变量名称是否等于root，然后用逻辑运算符“非”进行取反操作，效果就变成了判断当前登录的用户是否为非管理员用户了。最后若条件成立则会根据逻辑“与”运算符输出user字样；或条件不满足则会通过逻辑“或”运算符输出root字样，而如果前面的&&不成立才会执行后面的||符号。
  [root@linuxprobe ~]# [ $USER != root ] && echo "user" || echo "root"
  root
  ```

- 整数值比较语句；

  ```
  整数比较运算符仅是对数字的操作，不能将数字与字符串、文件等内容一起操作，而且不能想当然地使用日常生活中的等号、大于号、小于号等来判断。因为等号与赋值命令符冲突，大于号和小于号分别与输出重定向命令符和输入重定向命令符冲突
  -eq	是否等于
  -ne	是否不等于
  -gt	是否大于
  -lt	是否小于
  -ge	是否大于或等于
  -le	是否等于或小于
  
  测试一下10是否大于10以及10是否等于10（通过输出的返回值内容来判断）：
  [root@linuxprobe ~]# [ 10 -gt 10 ]
  [root@linuxprobe ~]# echo $?
  1
  [root@linuxprobe ~]# [ 10 -eq 10 ]
  [root@linuxprobe ~]# echo $?
  0
  
  先使用free -m命令查看内存使用量情况（单位为MB），然后通过grep Mem:命令过滤出剩余内存量的行，再用awk '{print $4}'命令只保留第四列，最后用FreeMem=`语句`的方式把语句内执行的结果赋值给变量。
  其实可以直接用awk过滤，不需要使用grep查询，这里只是练习管道符
  [root@serverlinux Downloads]# free -m
                total        used        free      shared  buff/cache   available
  Mem:           3704        3273         120          35         310         103
  Swap:          3968        1965        2003
  [root@serverlinux Downloads]# FreeMem=`free -m | grep Mem: | awk '{print $4}'`
  [root@serverlinux Downloads]# FreeMem=`free -m | awk '/Mem:/{print $4}'`
  [root@serverlinux Downloads]# echo $FreeMem
  121
  我们使用整数运算符来判断内存可用量的值是否小于1024，若小于则会提示“Insufficient Memory”（内存不足）的字样：
  [root@linuxprobe ~]# [ $FreeMem -lt 1024 ] && echo "Insufficient Memory"
  Insufficient Memory
  
  同上，如果当前可用内存<=116M，则显示内存不足，否则显示可用内存的数量
  [root@serverlinux Downloads]# [ `free -m | awk '/Mem:/{print $4}'` -le 116 ] && echo "Insufficient Memory" || echo `free -m | awk '/Mem/{print $4}'`
  122
  ```

- awk命令

  ```
  awk命令：
  用法：awk ' pattern {action} '  
  awk命令形式:
  awk [-F|-f|-v] ‘BEGIN{} //{command1; command2} END{}’ file
   [-F|-f|-v]   大参数，-F指定分隔符，-f调用脚本，-v定义变量 var=value
  '  '          引用代码块
  BEGIN   初始化代码块，在对每一行进行处理之前，初始化代码，主要是引用全局变量，设置FS分隔符
  //           匹配代码块，可以是字符串或正则表达式
  {}           命令代码块，包含一条或多条命令
  ；          多条命令使用分号分隔
  END      结尾代码块，在对每一行进行处理之后再执行的代码块，主要是进行最终计算或输出结尾摘要信息 
  特殊要点:
  $0           表示整个当前行
  $1           每行第一个字段
  NF          字段数量变量
  NR          每行的记录号，多文件记录递增
  FNR        与NR类似，不过多文件记录不递增，每个文件都从1开始
  \t            制表符
  \n           换行符
  FS          BEGIN时定义分隔符
  RS       输入的记录分隔符， 默认为换行符(即文本是按一行一行输入)
  ~            匹配，与==相比不是精确比较
  !~           不匹配，不精确比较
  ==         等于，必须全部相等，精确比较
  !=           不等于，精确比较
  &&　     逻辑与
  ||             逻辑或
  +            匹配时表示1个或1个以上
  /[0-9][0-9]+/   两个或两个以上数字
  /[0-9][0-9]*/    一个或一个以上数字
  FILENAME 文件名
  OFS      输出字段分隔符， 默认也是空格，可以改为制表符等
  ORS        输出的记录分隔符，默认为换行符,即处理结果也是一行一行输出到屏幕
  -F'[:#/]'   定义三个分隔符
  
  开始范例：
  [root@serverlinux Downloads]# cat example1.sh
  #! /bin/bash
  #
  echo "当前的脚本名称为$0。"
  echo "总共有$#个参数，分别是$*。"
  echo "第一个参数为$1, 第5个参数为$5。"
  
  显示file文件中包含 0 的那一行
  [root@serverlinux Downloads]# awk '/0/' example1.sh
  echo "当前的脚本名称为$0。"
  
  显示file文件中包含0到包含5的那几行
  [root@serverlinux Downloads]# awk '/0/,/5/' example1.sh
  echo "当前的脚本名称为$0。"
  echo "总共有$#个参数，分别是$*。"
  echo "第一个参数为$1, 第5个参数为$5。"
  
  显示file中，第一域中有字符串echo的那些行
  [root@linuxprobe xx]# awk '$1=="echo"' example1.sh 
  echo "The name of current scritp is $0."
  echo "The number of parameters is $#. They are $*"
  echo "The 1st parameter is $1, the 5th parameter is $5."
  
  显示包含0那一行的第二个域
  [root@linuxprobe xx]# awk '/0/ {print $2}' example1.sh 
  "The
  
  根据新的分隔符"（双引号）来重新分区域，显示包含0那一行的，第二个区域的内容
  [root@linuxprobe xx]# awk -F"\"" '/0/ {print $2}' example1.sh 
  The name of current scritp is $0.
  
  调用脚本awkfile来进行控制：打印Hello！，然后打印每一行的第一、第二个域
  [root@linuxprobe xx]# cat awkfile 
  /0/{print "\047 Hello! \047"}    --遇到匹配行以后打印 ' Hello! '.  \047代表单引号。 
  {print $1,$2}                    --因为没有模式控制，打印每一行的前两个域。
  
  [root@linuxprobe xx]# awk -f awkfile example1.sh 
  #!/bin/bash 
  # 
  ' Hello! '
  echo "The
  echo "The
  echo "The
  
  显示第一个域为echo的那些行的第1和第2个域
  [root@serverlinux Downloads]# awk '$1~/echo/{print $1,$2}' example1.sh
  echo "当前的脚本名称为$0。"
  echo "总共有$#个参数，分别是$*。"
  echo "第一个参数为$1,
  
  计算当前目录下纯文件的大小之和
  [root@serverlinux Downloads]# ll
  total 8
  -rw-r--r--. 1 root root 150 Feb 14 19:29 example1.sh
  -rwxr--r--. 1 root root  51 Feb 14 15:27 example.sh
  drwxr-xr-x. 2 root root  89 Feb 13 14:21 xx
  [root@serverlinux Downloads]# ls -l|awk 'BEGIN{sum=0} !/^d/{sum+=$5} END{print "total size is:",sum,"B"}' # 解释：！非；^d以d开头的行；$5第五个域，即文件大小；
  total size is: 201 B
  
  统计netstat -anp 状态为LISTEN和CONNECT的连接数量分别是多少
  [root@serverlinux Downloads]# netstat -anp
  Active Internet connections (servers and established)
  Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
  tcp        0      0 0.0.0.0:5902            0.0.0.0:*               LISTEN      23291/Xvnc
  tcp        0      0 0.0.0.0:5903            0.0.0.0:*               LISTEN      24221/Xvnc
  。。。
  Active UNIX domain sockets (servers and established)
  Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Path
  unix  2      [ ACC ]     STREAM     LISTENING     166179   23312/dbus-daemon    @/tmp/dbus-aKbSoFysIR
  unix  2      [ ACC ]     STREAM     LISTENING     29412    1411/master          private/virtual
  。。。
  unix  3      [ ]         STREAM     CONNECTED     168731   23766/tracker-miner
  unix  3      [ ]         STREAM     CONNECTED     144114   17485/gnome-shell
  unix  3      [ ]         STREAM     CONNECTED     37289    1826/packagekitd
  。。。
  [root@serverlinux Downloads]# netstat -anp|awk '$6~/LISTEN|CONNECTED/{sum[$6]++} END{for (i in sum) printf "%-10s %-6s %-3s \n", i," ",sum[i]}' 
  #解释：第6个域含有LISTEN或CONNECTED,统计每种各自的数目总数，最后用一个for循环输出，格式化输出，%为分割符，-10表示长度为10个字符，s表示字符串类型，i为LISTEN或CONNECTED
  LISTEN            20
  CONNECTED         738
  
  统计当前目录下文件的总大小，不包括子文件夹
  [root@serverlinux Downloads]# ls -l
  total 68
  -rw-r--r--. 1 root root   150 Feb 14 19:29 example1.sh
  -rwxr--r--. 1 root root    51 Feb 14 15:27 example.sh
  -rw-r--r--. 1 root root   395 Feb 16 09:41 fs
  -rw-r--r--. 1 root root 56473 Feb 16 09:37 netstat.txt
  drwxr-xr-x. 2 root root    89 Feb 13 14:21 xx
  [root@serverlinux Downloads]# ls -l|awk 'NR!=1 && !/^d/{sum[$3]+=$5} END{for (i in sum) printf "%-6s %-5s %-3s %-2s \n" ,i," ",int(sum[i]/1000),"kB"}'
  root         57  kB
  [root@serverlinux Downloads]#
  
  输出成绩表
  [root@serverlinux Downloads]# cat test0
  Marry 2143 78 84 77
  Jack 2321 66 78 45
  Tom 2122 48 77 71
  Mike 2537 87 97 95
  Bob 2415 40 57 62
  [root@serverlinux Downloads]# awk 'BEGIN{math=0;eng=0;com=0;printf "Lineno.   Name    No.    Math   English   Computer
     Total\n";printf "------------------------------------------------------------\n"}{math+=$3; eng+=$4; com+=$5;printf
  "%-8s %-7s %-7s %-7s %-9s %-10s %-7s \n",NR,$1,$2,$3,$4,$5,$3+$4+$5} END{printf "-------------------------------------- ----------------------\n";printf "%-24s %-7s %-9s %-20s \n","Total:",math,eng,com;printf "%-24s %-7s %-9s %-20s \n","Av g:",math/NR,eng/NR,com/NR}' test0
  Lineno.   Name    No.    Math   English   Computer    Total
  ------------------------------------------------------------
  1        Marry   2143    78      84        77         239
  2        Jack    2321    66      78        45         189
  3        Tom     2122    48      77        71         196
  4        Mike    2537    87      97        95         279
  5        Bob     2415    40      57        62         159
  ------------------------------------------------------------
  Total:                   319     393       350
  Avg:                     63.8    78.6      70
  ```

  

- 字符串比较语句。

  ```
  字符串比较语句用于判断测试字符串是否为空值，或两个字符串是否相同。它经常用来判断某个变量是否未被定义（即内容为空值）
  =	比较字符串内容是否相同
  !=	比较字符串内容是否不同
  -z	判断字符串内容是否为空
  
  接下来通过判断String变量是否为空值，进而判断是否定义了这个变量：
  [root@linuxprobe ~]# [ -z $String ]
  [root@linuxprobe ~]# echo $?
  0
  
  尝试引入逻辑运算符来试一下。当用于保存当前语系的环境变量值LANG不是英语（en.US）时，则会满足逻辑测试条件并输出“Not en.US”（非英语）的字样：
  [root@linuxprobe ~]# echo $LANG
  en_US.UTF-8
  [root@linuxprobe ~]# [ $LANG != "en.US" ] && echo "Not en.US"
  Not en.US
  ```

##### 4.3 流程控制语句

我们通过if、for、while、case这4种流程控制语句来学习编写难度更大、功能更强的Shell脚本。

###### 4.3.1 if条件测试语句

if条件语句分为单分支结构、双分支结构、多分支结构

- 单分支结构由if、then、fi关键词组成

  ```
  使用单分支的if条件语句来判断/media/cdrom文件是否存在，若存在就结束条件判断和整个Shell脚本，反之则去创建这个目录：
  [root@linuxprobe ~]# vim mkcdrom.sh
  #!/bin/bash
  DIR="/media/cdrom"
  if [ ! -e $DIR ]
  then
  	mkdir -p $DIR
  fi
  
  这里继续用“bash 脚本名称”的方式来执行脚本。在正常情况下，顺利执行完脚本文件后没有任何输出信息，但是可以使用ls命令验证/media/cdrom目录是否已经成功创建：
  [root@linuxprobe ~]# bash mkcdrom.sh
  [root@linuxprobe ~]# ls -d /media/cdrom
  /media/cdrom
  ```

- 双分支结构由if、then、else、fi关键词组成

  ```
  下面使用双分支的if条件语句来验证某台主机是否在线，然后根据返回值的结果，要么显示主机在线信息，要么显示主机不在线信息。这里的脚本主要使用ping命令来测试与对方主机的网络联通性，而Linux系统中的ping命令不像Windows一样尝试4次就结束，因此为了避免用户等待时间过长，需要通过-c参数来规定尝试的次数，并使用-i参数定义每个数据包的发送间隔，以及使用-W参数定义等待超时时间。
  [root@linuxprobe ~]# vim chkhost.sh
  #!/bin/bash
  ping -c 3 -i 0.2 -W 3 $1 &> /dev/null
  if [ $? -eq 0 ]
  then
  echo "Host $1 is On-line."
  else
  echo "Host $1 is Off-line."
  fi
  
  我们来验证一下脚本的效果：
  [root@linuxprobe ~]# bash chkhost.sh 192.168.10.10
  Host 192.168.10.10 is On-line.
  [root@linuxprobe ~]# bash chkhost.sh 192.168.10.20
  Host 192.168.10.20 is Off-line.
  ```

  **注意：/dev/null是一个被称作Linux黑洞的文件，把输出信息重定向到这个文件等同于删除数据（类似于没有回收功能的垃圾箱），可以让用户的屏幕窗口保持简洁。**

- 多分支结构由if、then、else、elif、fi关键词组成

  ```
  下面使用多分支的if条件语句来判断用户输入的分数在哪个成绩区间内，然后输出如Excellent、Pass、Fail等提示信息。在Linux系统中，read是用来读取用户输入信息的命令，能够把接收到的用户输入信息赋值给后面的指定变量，-p参数用于向用户显示一定的提示信息。在下面的脚本示例中，只有当用户输入的分数大于等于85分且小于等于100分，才输出Excellent字样；若分数不满足该条件（即匹配不成功），则继续判断分数是否大于等于70分且小于等于84分，如果是，则输出Pass字样；若两次都落空（即两次的匹配操作都失败了），则输出Fail字样：
  [root@linuxprobe ~]# vim chkscore.sh
  #!/bin/bash
  read -p "Enter your score（0-100）：" GRADE
  if [ $GRADE -ge 85 ] && [ $GRADE -le 100 ] ; then
    echo "$GRADE is Excellent"
    elif [ $GRADE -ge 70 ] && [ $GRADE -le 84 ] ; then
      echo "$GRADE is Pass"
    elif [ $GRADE -gt 100 ] || [ $GRADE -lt 0 ] ; then
      echo "$GRADE is Error"
    else
      echo "$GRADE is Fail" 
  fi
  [root@linuxprobe ~]# bash chkscore.sh
  Enter your score（0-100）：88
  88 is Excellent
  [root@linuxprobe ~]# bash chkscore.sh 
  Enter your score（0-100）：80
  80 is Pass
  [root@linuxprobe ~]# bash chkscore.sh  
  Enter your score（0-100）：30
  30 is Fail
  [root@linuxprobe ~]# bash chkscore.sh
  Enter your score（0-100）：200 
  200 is Error
  ```

###### 4.3.2 for条件循环语句

![for条件语句-1](pics\for条件语句-1.png)

```
使用for循环语句从列表文件中读取多个用户名，然后为其逐一创建用户账户并设置密码。首先创建用户名称的列表文件users.txt，每个用户名称单独一行。读者可以自行决定具体的用户名称和个数：
[root@linuxprobe ~]# vim users.txt
andy
barry
carl
duke
eric
george

接下来编写Shell脚本Example.sh。在脚本中使用read命令读取用户输入的密码值，然后赋值给PASSWD变量，并通过-p参数向用户显示一段提示信息，告诉用户正在输入的内容即将作为账户密码。在执行该脚本后，会自动使用从列表文件users.txt中获取到所有的用户名称，然后逐一使用“id 用户名”命令查看用户的信息，并使用$?判断这条命令是否执行成功，也就是判断该用户是否已经存在。

注意：/dev/null是一个被称作Linux黑洞的文件，把输出信息重定向到这个文件等同于删除数据（类似于没有回收功能的垃圾箱），可以让用户的屏幕窗口保持简洁。

[root@linuxprobe ~]# vim Example.sh
#!/bin/bash
read -p "Enter The Users Password : " PASSWD
for UNAME in `cat users.txt`
do
id $UNAME &> /dev/null
if [ $? -eq 0 ]
then
echo "Already exists"
else
useradd $UNAME &> /dev/null
echo "$PASSWD" | passwd --stdin $UNAME &> /dev/null
if [ $? -eq 0 ]
then
echo "$UNAME , Create success"
else
echo "$UNAME , Create failure"
fi
fi
done

执行批量创建用户的Shell脚本Example.sh，在输入为账户设定的密码后将由脚本自动检查并创建这些账户。由于已经将多余的信息通过输出重定向符转移到了/dev/null黑洞文件中，因此在正常情况下屏幕窗口除了“用户账户创建成功”（Create success）的提示后不会有其他内容。

在Linux系统中，/etc/passwd是用来保存用户账户信息的文件。如果想确认这个脚本是否成功创建了用户账户，可以打开这个文件，看其中是否有这些新创建的用户信息。
[root@linuxprobe ~]# bash Example.sh
Enter The Users Password : linuxprobe
andy , Create success
barry , Create success
carl , Create success
duke , Create success
eric , Create success
george , Create success
[root@linuxprobe ~]# tail -6 /etc/passwd
andy:x:1001:1001::/home/andy:/bin/bash
barry:x:1002:1002::/home/barry:/bin/bash
carl:x:1003:1003::/home/carl:/bin/bash
duke:x:1004:1004::/home/duke:/bin/bash
eric:x:1005:1005::/home/eric:/bin/bash
george:x:1006:1006::/home/george:/bin/bash

尝试让脚本从文本中自动读取主机列表，然后自动逐个测试这些主机是否在线。
首先创建一个主机列表文件ipadds.txt：
[root@linuxprobe ~]# vim ipadds.txt
192.168.10.10
192.168.10.11
192.168.10.12
然后前面的双分支if条件语句与for循环语句相结合，让脚本从主机列表文件ipadds.txt中自动读取IP地址（用来表示主机）并将其赋值给HLIST变量，从而通过判断ping命令执行后的返回值来逐个测试主机是否在线。脚本中出现的$（命令）是一种完全类似于第3章的转义字符中反引号`命令`的Shell操作符，效果同样是执行括号或双引号括起来的字符串中的命令。大家在编写脚本时，多学习几种类似的新方法，可在工作中大显身手：
[root@linuxprobe ~]# vim CheckHosts.sh
#!/bin/bash
HLIST=$(cat ~/ipadds.txt)
for IP in $HLIST
do
ping -c 3 -i 0.2 -W 3 $IP &> /dev/null
if [ $? -eq 0 ] ; then
echo "Host $IP is On-line."
else
echo "Host $IP is Off-line."
fi
done
[root@linuxprobe ~]# ./CheckHosts.sh
Host 192.168.10.10 is On-line.
Host 192.168.10.11 is Off-line.
Host 192.168.10.12 is Off-line.
```

###### 4.3.3 while条件循环语句

![while条件语句-1](pics\while条件语句-1.png)

```
编写一个用来猜测数值大小的脚本Guess.sh。该脚本使用$RANDOM变量来调取出一个随机的数值（范围为0～32767），将这个随机数对1000进行取余操作，并使用expr命令取得其结果，再用这个数值与用户通过read命令输入的数值进行比较判断。这个判断语句分为三种情况，分别是判断用户输入的数值是等于、大于还是小于使用expr命令取得的数值。当前，现在这些内容不是重点，我们当前要关注的是while条件循环语句中的条件测试始终为true，因此判断语句会无限执行下去，直到用户输入的数值等于expr命令取得的数值后，这两者相等之后才运行exit 0命令，终止脚本的执行。

[root@linuxprobe ~]# vim Guess.sh
#!/bin/bash
PRICE=$(expr $RANDOM % 1000)
TIMES=0
echo "商品实际价格为0-999之间，猜猜看是多少？"
while true
do
read -p "请输入您猜测的价格数目：" INT
let TIMES++
if [ $INT -eq $PRICE ] ; then
echo "恭喜您答对了，实际价格是 $PRICE"
echo "您总共猜测了 $TIMES 次"
exit 0
elif [ $INT -gt $PRICE ] ; then
echo "太高了！"
else
echo "太低了！"
fi
done

在这个Guess.sh脚本中，我们添加了一些交互式的信息，从而使得用户与系统的互动性得以增强。而且每当循环到let TIMES++命令时都会让TIMES变量内的数值加1，用来统计循环总计执行了多少次。这可以让用户得知总共猜测了多少次之后，才猜对价格。

[root@linuxprobe ~]# bash Guess.sh
商品实际价格为0-999之间，猜猜看是多少？
请输入您猜测的价格数目：500
太低了！
请输入您猜测的价格数目：800
太高了！
请输入您猜测的价格数目：650
太低了！
请输入您猜测的价格数目：720
太高了！
请输入您猜测的价格数目：690
太低了！
请输入您猜测的价格数目：700
太高了！
请输入您猜测的价格数目：695
太高了！
请输入您猜测的价格数目：692
太高了！
请输入您猜测的价格数目：691
恭喜您答对了，实际价格是 691
您总共猜测了 9 次
```

###### 4.3.4 case条件测试语句

case语句是在多个范围内匹配数据，若匹配成功则执行相关命令并结束整个条件测试；而如果数据不在所列出的范围内，则会去执行星号（*）中所定义的默认命令

![case条件语句-1](pics\case条件语句-1.png)

```
我们必须有一定的措施来判断用户的输入内容，当用户输入的内容不是数字时，脚本能予以提示，从而免于崩溃。

通过在脚本中组合使用case条件测试语句和通配符（详见第3章），完全可以满足这里的需求。接下来我们编写脚本Checkkeys.sh，提示用户输入一个字符并将其赋值给变量KEY，然后根据变量KEY的值向用户显示其值是字母、数字还是其他字符。

[root@linuxprobe ~]# vim Checkkeys.sh
#!/bin/bash
read -p "请输入一个字符，并按Enter键确认：" KEY
case "$KEY" in
[a-z]|[A-Z])
echo "您输入的是 字母。"
;;
[0-9])
echo "您输入的是 数字。"
;;
*)
echo "您输入的是 空格、功能键或其他控制字符。"
esac
[root@linuxprobe ~]# bash Checkkeys.sh
请输入一个字符，并按Enter键确认：6
您输入的是 数字。
[root@linuxprobe ~]# bash Checkkeys.sh
请输入一个字符，并按Enter键确认：p
您输入的是 字母。
[root@linuxprobe ~]# bash Checkkeys.sh
请输入一个字符，并按Enter键确认：^[[15~
您输入的是 空格、功能键或其他控制字符。
```

##### 4.4 计划任务服务程序

计划任务分为一次性计划任务与长期性计划任务，大家可以按照如下方式理解。

> 一次性计划任务：今晚11点30分开启网站服务。
>
> 长期性计划任务：每周一的凌晨3点25分把/home/wwwroot目录打包备份为backup.tar.gz。

- 一次性计划任务只执行一次，一般用于满足临时的工作需求。

  ```
  我们可以用at命令实现这种功能，只需要写成“at 时间”的形式就可以。如果想要查看已设置好但还未执行的一次性计划任务，可以使用“at -l”命令；要想将其删除，可以用“atrm 任务序号”。在使用at命令来设置一次性计划任务时，默认采用的是交互式方法。例如，使用下述命令将系统设置为在今晚23:30分自动重启网站服务。
  [root@linuxprobe ~]# at 23:30
  at > systemctl restart httpd
  at > 此处请同时按下Ctrl+d来结束编写计划任务
  job 3 at Mon Apr 27 23:30:00 2015
  [root@linuxprobe ~]# at -l
  3 Mon Apr 27 23:30:00 2016 a root
  
  可以把前面学习的管道符（任意门）放到两条命令之间，让at命令接收前面echo命令的输出信息，以达到通过非交互式的方式创建计划一次性任务的目的。
  [root@linuxprobe ~]# echo "systemctl restart httpd" | at 23:30
  job 4 at Mon Apr 27 23:30:00 2015
  [root@linuxprobe ~]# at -l
  3 Mon Apr 27 23:30:00 2016 a root
  4 Mon Apr 27 23:30:00 2016 a root
  
  可以使用下面的命令轻松删除其中一个：
  [root@linuxprobe ~]# atrm 3
  [root@linuxprobe ~]# at -l
  4 Mon Apr 27 23:30:00 2016 a root
  ```

- 如果希望Linux系统能够周期性地、有规律地执行某些具体的任务，要启用Linux系统中默认的crond服务

  ```
  crontab -e 		创建、编辑计划任务
  crontab -l		查看当前计划任务的命令
  crontab -r		删除某条计划任务的命令
  crontab -u		如果以管理员的身份登录的系统，可以用来编辑他人的计划任务
  
  crond服务设置任务的参数格式：“分、时、日、月、星期 命令”。
  如果有些字段没有设置，则需要使用星号（*）占位，如图:
  ```

  ![cron计划任务的参数](pics\cron计划任务的参数.png)

  ```
  分钟	取值为0～59的整数
  小时	取值为0～23的任意整数
  日期	取值为1～31的任意整数
  月份	取值为1～12的任意整数
  星期	取值为0～7的任意整数，其中0与7均为星期日
  命令	要执行的命令或程序脚本
  
  假设在每周一、三、五的凌晨3点25分，都需要使用tar命令把某个网站的数据目录进行打包处理，使其作为一个备份文件。我们可以使用crontab -e命令来创建计划任务。为自己创建计划任务无需使用-u参数，具体的实现效果的参数如crontab -l命令结果所示：
  [root@linuxprobe ~]# crontab -e
  no crontab for root - using an empty one
  crontab: installing new crontab
  [root@linuxprobe ~]# crontab -l
  25 3 * * 1,3,5 /usr/bin/tar -czvf backup.tar.gz /home/wwwroot
  
  需要说明的是，除了用逗号（,）来分别表示多个时间段，例如“8,9,12”表示8月、9月和12月。还可以用减号（-）来表示一段连续的时间周期（例如字段“日”的取值为“12-15”，则表示每月的12～15日）。以及用除号（/）表示执行任务的间隔时间（例如“*/2”表示每隔2分钟执行一次任务）。
  
  尤其需要注意的是，在crond服务的计划任务参数中，所有命令一定要用绝对路径的方式来写，如果不知道绝对路径，请用whereis命令进行查询，rm命令路径为下面输出信息中加粗部分。
  [root@linuxprobe ~]# whereis rm
  rm: /usr/bin/rm /usr/share/man/man1/rm.1.gz /usr/share/man/man1p/rm.1p.gz
  [root@linuxprobe ~]# crontab -e
  crontab: installing new crontab
  [root@linuxprobe ~]# crontab -l
  25 3 * * 1,3,5 /usr/bin/tar -czvf backup.tar.gz /home/wwwroot
  0 1 * * 1-5 /usr/bin/rm -rf /tmp/*
  
  注意事项:
  - 在crond服务的配置参数中，可以像Shell脚本那样以#号开头写上注释信息，这样在日后回顾这段命令代码时可以快速了解其功能、需求以及编写人员等重要信息。
  - 计划任务中的“分”字段必须有数值，绝对不能为空或是*号，而“日”和“星期”字段不能同时使用，否则就会发生冲突。
  ```

### 第五章 用户身份与文件权限

本章将详细讲解文件的**所有者**、**所属组**以及**其他人**可对文件进行的**读（r）、写（w）、执行（x）**等操作

如何在[Linux系统](https://www.linuxprobe.com/)中**添加、删除、修改**用户**账户信息**。

使用**SUID、SGID**与**SBIT**特殊权限更加灵活地设置系统权限功能，来弥补对文件设置一般操作权限时所带来的不足。

**隐藏权限**能够给系统增加一层隐形的防护层，让黑客最多只能查看关键日志信息，而不能进行修改或删除。

**文件的访问控制列表（Access Control List，ACL）**可以进一步让单一用户、用户组对单一文件或目录进行特殊的权限设置，让文件具有能满足工作需求的最小权限。

使用**su命令**与**sudo服务**让普通用户具备管理员权限，不仅满足日常的工作需求，还可以确保系统的安全。

##### 5.1 用户身份与能力

在Linux系统中，**UID**就相当于我们的身份证号码一样具有唯一性，因此可通过用户的UID值来判断用户身份。在RHEL 7系统中，用户身份有下面这些。

> 管理员UID为0：系统的管理员用户。
>
> 系统用户UID为1～999： Linux系统为了避免因某个服务程序出现漏洞而被黑客提权至整台服务器，默认服务程序会有独立的系统用户负责运行，进而有效控制被破坏范围。
>
> 普通用户UID从1000开始：是由管理员创建的用于日常工作的用户。

Linux系统中还引入了用户组的概念。通过使用**用户组号码（GID，Group IDentification）**，我们可以把多个用户加入到同一个组中，从而方便为组中的用户统一规划权限或指定任务。

在Linux系统中创建每个用户时，将自动创建一个与其同名的**基本用户组**，而且这个基本用户组只有该用户一个人。如果该用户以后被归纳入其他用户组，则这个其他用户组称之为**扩展用户组**。**一个用户只有一个基本用户组，但是可以有多个扩展用户组**。

###### 5.1.1.useradd命令

useradd命令用于创建新的用户。默认的用户家目录会被存放**在/home目录下**中，默认的Shell**解释器为/bin/bash**，而且默认会创建一个与该用户同名的**基本用户组**。

```
useradd命令格式为：useradd [选项] 用户名

-d	指定用户的家目录（默认为/home/username）
-e	账户的到期时间，格式为YYYY-MM-DD.
-u	指定该用户的默认UID
-g	指定一个初始的用户基本组（必须已存在）
-G	指定一个或多个扩展用户组
-N	不创建与用户同名的基本用户组
-s	指定该用户的默认Shell解释器

创建一个普通用户:(一旦用户的解释器被设置为nologin，则代表该用户不能登录到系统中)
[root@linuxprobe ~]# useradd -d /home/linux -u 8888 -s /sbin/nologin linuxprobe
[root@linuxprobe ~]# id linuxprobe
uid=8888(linuxprobe) gid=8888(linuxprobe) groups=8888(linuxprobe)
[root@linuxprobe ~]# tail -n 3 /etc/passwd
```

###### 5.1.2 groupadd命令

groupadd命令用于创建用户组。

```
groupadd命令格式为：groupadd [选项] 群组名

[root@linuxprobe ~]# groupadd ronny
[root@linuxprobe ~]# tail -n 3 /etc/group
```

###### 5.1.3 usermod命令

usermod命令用于修改用户的属性。用户的信息保存在**/etc/passwd文件**中，可以直接用文本编辑器来修改其中的用户参数项目，也可以用usermod命令修改已经创建的用户信息，诸如用户的UID、基本/扩展用户组、默认终端等

```
usermod命令格式为：usermod [选项] 用户名”
-c	填写用户账户的备注信息
-d -m	参数-m与参数-d连用，可重新指定用户的家目录并自动把旧的数据转移过去
-e	账户的到期时间，格式为YYYY-MM-DD
-g	变更所属用户组
-G	变更扩展用户组 -a 增加扩展组，不加这个参数会用新的扩展组列表替换原扩展组
-L	锁定用户禁止其登录系统
-U	解锁用户，允许其登录系统
-s	变更默认终端
-u	修改用户的UID

先来看一下账户linuxprobe的默认信息：
[root@linuxprobe ~]# id linuxprobe
uid=1000(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe)

然后将用户linuxprobe加入到root用户组中，这样扩展组列表中则会出现root用户组的字样，而基本组不会受到影响：
[root@linuxprobe ~]# usermod -G root linuxprobe
[root@linuxprobe ~]# id linuxprobe
uid=1000(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe),0(root)

再来试试用-u参数修改linuxprobe用户的UID号码值。除此之外，我们还可以用-g参数修改用户的基本组ID，用-G参数修改用户扩展组ID。
[root@linuxprobe ~]# usermod -u 8888 linuxprobe
[root@linuxprobe ~]# id linuxprobe
uid=8888(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe),0(root)

重新指定用户的家目录并自动把旧的数据转移过去，使用：usermod -d 新家目录 -m 登录名
[root@centos ~]$ ls /home/
kekcentos  linux  
[root@centos ~]# ls /home/linux/
sdf.txt  下载  公共  图片  文档  桌面  模板  视频  音乐
[root@centos ~]# usermod -d /home/linuxprobe -m linuxprobe 
[root@centos ~]# ls /home/
kekcentos  linuxprobe 
[root@centos ~]# ls /home/linuxprobe/
sdf.txt  下载  公共  图片  文档  桌面  模板  视频  音乐
```

###### 5.1.4. passwd命令

passwd命令用于修改用户密码、过期时间、认证信息等

```
passwd命令格式为：passwd [选项] [用户名]
-l	锁定用户，禁止其登录
-u	解除锁定，允许用户登录
--stdin	允许通过标准输入修改用户密码，如echo "NewPassWord" | passwd --stdin Username
-d	使该用户可用空密码登录系统
-e	强制用户在下次登录时修改密码
-S	显示用户的密码是否被锁定，以及密码所采用的加密算法名称

修改用户自己的密码，以及如何修改其他人的密码（修改他人密码时，需要具有root管理员权限）：
[root@linuxprobe ~]# passwd
Changing password for user root.
New password:此处输入密码值
Retype new password: 再次输入进行确认
passwd: all authentication tokens updated successfully.
[root@linuxprobe ~]# passwd linuxprobe
Changing password for user linuxprobe.
New password:此处输入密码值
Retype new password: 再次输入进行确认
passwd: all authentication tokens updated successfully.

假设您有位同事正在度假，而且假期很长，那么可以使用passwd命令禁止该用户登录系统，等假期结束回归工作岗位时，再使用该命令允许用户登录系统，而不是将其删除
[root@linuxprobe ~]# passwd -l linuxprobe
Locking password for user linuxprobe.
passwd: Success
[root@linuxprobe ~]# passwd -S linuxprobe
linuxprobe LK 2017-12-26 0 99999 7 -1 (Password locked.)
[root@linuxprobe ~]# passwd -u linuxprobe
Unlocking password for user linuxprobe.
passwd: Success
[root@linuxprobe ~]# passwd -S linuxprobe
linuxprobe PS 2017-12-26 0 99999 7 -1 (Password set, SHA512 crypt.)
```

###### 5.1.5 userdel命令

```
userdel命令用于删除用户，格式为：userdel [选项] 用户名
-f	强制删除用户
-r	同时删除用户及用户家目录
```

###### 5.1.6 chmod命令

```
用来设置文件或目录的权限，格式为：chmod [-cfvR] [--help] [--version] mode 文件或目录名称
是“change mode”的缩写，直译”改变模式“，改变模式是指改变用户对文件或则目录的访问模式（权限），主体对象是文件或目录，针对某一个文件或则目录给某一个用户添加权限或移除权限。

-c (--change): 若该文件权限确实已经更改，才显示其更改动作
-f (--silent,--quiet): 若该文件权限无法被更改也不要显示错误讯息
-v (--verbose): 显示权限变更的详细资料
-R (--recursive): 对目前目录下的所有文件与子目录进行相同的权限变更(即以递回的方式逐个变更)
--help : 显示辅助说明
--version : 显示版本

mode:权限设定字串，
一是文字设定法：格式如下 :[ugoa...][[+-=][rwxX]...][,...]
u(user) 表示该文件的拥有者，g(group)表示与该文件的拥有者属于同一个群体，o(others)表示其他以外的人，a(all)表示这三者皆是。
+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
r 表示可读取，w 表示可写入，x 表示可执行, s 特殊权限，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。

将文件 file1.txt 设为所有人皆可读取 :
chmod ugo+r file1.txt
或
chmod a+r file1.txt

chmod也可以用数字来表示权限如 :
语法为：chmod abc file 其中a,b,c各为一个数字，分别表示User、Group、及Other的权限
如 :
chmod 777 file
```

###### 5.1.7 chown命令

```
设置文件或目录的所有者和所属组。格式为：chown [参数] 所有者:所属组 文件或目录名称
chown [-cfhvR] [--help] [--version] user[:group] file...
change file owner and group
user : 新的文件拥有者的使用者 ID
group : 新的文件拥有者的使用者组(group)
-c (--changes): 显示更改的部分的信息
-f (--slient,--quiet): 忽略错误信息
-h (--no-dereference):修复符号链接
-v (--verbose): 显示详细的处理信息
-R (--recursive): 处理指定目录以及其子目录下的所有文件
--help : 显示辅助说明
--version : 显示版本

[root@linuxprobe ~]# ls -l test
-rwxrw----. 1 linuxprobe root 15 Feb 11 11:50 test
[root@linuxprobe ~]# chown root:bin test
[root@linuxprobe ~]# ls -l test
-rwxrw----. 1 root bin 15 Feb 11 11:50 test
```

chmod和chown命令是用于修改文件属性和权限的最常用命令，它们还有一个特别的共性，就是针对目录进行操作时需要加上大写**参数-R来表示递归操作**，即对目录内所有的文件进行整体操作。

##### 5.2 文件权限与归属

Linux系统中一切都是文件，但是文件的类型不相同，因此Linux系统使用了不同的字符来加以区分，常见的字符如下所示。

>  -：普通文件。
>
>  d：目录文件。
>
>  l：链接文件。
>
>  b：块设备文件。
>
>  c：字符设备文件。
>
>  p：管道文件。

如：下面的一个文件
[root@centos ~]# ll initial-setup-ks.cfg 
-rw-r--r--. 1 root root 1977 Jan 24 23:57 initial-setup-ks.cfg

在Linux系统中，每个文件都有所属的所有者和所有组，并且规定了文件的所有者、所有组以及其他人对文件所拥有的可读（r）、可写（w）、可执行（x）等权限。

一般文件权限比较容易理解：

- “可读”表示能够读取文件的实际内容；
- “可写”表示能够编辑、新增、修改、删除文件的实际内容；
- “可执行”则表示能够运行一个脚本程序。

目录文件：

- “可读”表示能够读取目录内的文件列表；
- “可写”表示能够在目录内新增、删除、重命名文件；
- “可执行”则表示能够进入该目录。

文件的读、写、执行权限可以简写为**rwx**，亦可分别用数字**4、2、1**来表示，文件**所有者**，**所属组**及**其他用户**权限之间无关联，如表所示：

![权限值](pics\权限值.png)

举例：

现在有这样一个文件，其**所有者**拥有可读、可写、可执行的权限，其文件**所属组**拥有可读、可写的权限；而且**其他人**只有可读的权限。那么，这个文件的权限就是**rwxrw-r--**，数字法表示即为**764**。

##### 5.3 文件的特殊权限

这是一种对文件权限进行设置的特殊功能，可以与一般权限同时使用，以弥补一般权限不能实现的功能。

###### 5.3.1 SUID

SUID（super user ID）是一种对二进制程序进行设置的特殊权限，可以让**二进制程序**的执行者**临时拥有属主的权限**（仅对拥有执行权限的二进制程序有效）。

查看passwd命令属性时发现所有者的权限由rwx变成了rws，其中x改变成s就意味着该文件被赋予了SUID权限。另外有读者会好奇，那么如果原本的权限是rw-呢？如果原先权限位上没有x执行权限，那么被赋予特殊权限后将变成大写的S。

```
[root@linuxprobe ~]# ls -l /etc/shadow
----------. 1 root root 1004 Jan 3 06:23 /etc/shadow
[root@linuxprobe ~]# ls -l /bin/passwd
-rwsr-xr-x. 1 root root 27832 Jan 29 2017 /bin/passwd
```

###### 5.3.2 SGID

SGID（super group ID）主要实现如下两种功能：

> 让执行者**临时拥有属组的权限**（对拥有执行权限的**二进制程序**进行设置）；
>
> 在某个**目录中创建的文件自动继承该目录的用户组**（只可以对**目录**进行设置）。

一般情况：

每个文件都有其归属的所有者和所属组，当创建或传送一个文件后，这个文件就会自动归属于执行这个操作的用户（即该用户是文件的所有者）。

目录设置SGID权限后：

如果现在需要在一个部门内设置共享目录，让部门内的所有人员都能够读取目录中的内容，那么就可以创建部门共享目录后，在该目录上设置SGID特殊权限位。这样，部门内的任何人员在里面创建的任何文件都会归属于该目录的所属组，而不再是自己的基本用户组。此时，我们用到的就是SGID的第二个功能，即在某个目录中创建的文件自动继承该目录的用户组（只可以对目录进行设置）。

```
[root@linuxprobe ~]# cd /tmp
[root@linuxprobe tmp]# mkdir testdir
[root@linuxprobe tmp]# ls -ald testdir/			（ls的 -d参数 列出目录本身，而不是它的内容）
drwxr-xr-x. 2 root root 6 Feb 11 11:50 testdir/
[root@linuxprobe tmp]# chmod -Rf 777 testdir/
[root@linuxprobe tmp]# chmod -Rf g+s testdir/
[root@linuxprobe tmp]# ls -ald testdir/
drwxrwsrwx. 2 root root 6 Feb 11 11:50 testdir/
```

在使用上述命令设置好目录的777权限（确保普通用户可以向其中写入文件），并为该目录设置了SGID特殊权限位后，就可以切换至一个普通用户，然后尝试在该目录中创建文件，并查看新创建的文件是否会继承新创建的文件所在的目录的所属组名称：

```
[root@linuxprobe tmp]# su - linuxprobe
Last login: Wed Feb 11 11:49:16 CST 2017 on pts/0
[linuxprobe@linuxprobe ~]$ cd /tmp/testdir/
[linuxprobe@linuxprobe testdir]$ echo "linuxprobe.com" > test
[linuxprobe@linuxprobe testdir]$ ls -al test
-rw-rw-r--. 1 linuxprobe root 15 Feb 11 11:50 test
```

###### 5.3.3 SBIT

当**对**某个**目录**设置了SBIT（Sticky Bit）粘滞位权限后，那么该目录中的文件就只能被其所有者执行删除操作了。

与前面所讲的SUID和SGID权限显示方法不同，当目录被设置SBIT特殊权限位后，文件的其他人权限部分的x执行权限就会被替换成t或者T，原本有x执行权限则会写成t，原本没有x执行权限则会被写成T。

```
[root@linuxprobe tmp]# su - linuxprobe
Last login: Wed Feb 11 12:41:20 CST 2017 on pts/0
[linuxprobe@linuxprobe tmp]$ ls -ald /tmp
drwxrwxrwt. 17 root root 4096 Feb 11 13:03 /tmp
[linuxprobe@linuxprobe ~]$ cd /tmp
[linuxprobe@linuxprobe tmp]$ ls -ald
drwxrwxrwt. 17 root root 4096 Feb 11 13:03 .
[linuxprobe@linuxprobe tmp]$ echo "Welcome to linuxprobe.com" > test
[linuxprobe@linuxprobe tmp]$ chmod 777 test
[linuxprobe@linuxprobe tmp]$ ls -al test 
-rwxrwxrwx. 1 linuxprobe linuxprobe 10 Feb 11 12:59 test
```

我们切换到另外一个普通用户，然后尝试删除这个其他人创建的文件就会发现，即便读、写、执行权限全开，但是由于SBIT特殊权限位的缘故，依然无法删除该文件：

```
[root@linuxprobe tmp]# su - blackshield
Last login: Wed Feb 11 12:41:29 CST 2017 on pts/1
[blackshield@linuxprobe ~]$ cd /tmp
[blackshield@linuxprobe tmp]$ rm -f test
rm: cannot remove ‘test’: Operation not permitted
```

要是也想对其他目录来设置SBIT特殊权限位，用chmod命令就可以了。对应的参数o+t代表设置SBIT粘滞位权限：

```
[blackshield@linuxprobe tmp]$ exit
Logout
[root@linuxprobe tmp]# cd ~
[root@linuxprobe ~]# mkdir linux
[root@linuxprobe ~]# chmod -R o+t linux/
[root@linuxprobe ~]# ls -ld linux/
drwxr-xr-t. 2 root root 6 Feb 11 19:34 linux/
```

###### 5.3.4 SUID，SGID，SBIT总结

```
用数字来表示权限：
4表示SUID
2表示SGID
1表示SBIT
如果两个或三个权限同时存在时，就将者写权限的值相加就是需要的结果了，例如：增加SUID和SGID权限
chmod 6777 test

用字母添加SUID权限（只能对二进制文件设置，让其它人拥有所有者权限）
	设置后user的"x-->s"
	-rwsr-xr-x. 1 root root 27832 Jan 29 2017 /bin/passwd
	
用字母添加SGID权限（对目录设置，让其它人也拥有当前组的权限）（对二进制文件，让others拥有组权限）
chmod -Rf g+s testdir/   
	设置后group的"x-->s"
    -r-xr-xr-x   1 bin system 59346 Feb 11 2017  ps
    变为
    -r-xr-sr-x   1 bin system 59346 Feb 11 2017  ps

用字母添加SBIT属性（只能对目录设置，让目录中的文件只能被所有者删除）
[root@linuxprobe ~]# chmod -R o+t linux/
	设置后others的"x-->t"
	drwxrwxrwx.  30 root root  4096 Feb 17 13:40 testdir
    变为
    drwxrwxrwt.  30 root root  4096 Feb 17 13:40 testdir
```

##### 5.4 文件的隐藏属性

Linux系统中的文件除了具备一般权限和特殊权限之外，还有一种隐藏权限，即被隐藏起来的权限，默认情况下不能直接被用户发觉。这在一定程度上阻止了黑客篡改系统日志的图谋，因此这种“奇怪”的文件也保障了Linux系统的安全性。

###### 5.4.1 chattr命令

chattr命令用于设置文件的隐藏权限，格式为“chattr [参数] 文件”。如果想要把某个隐藏功能添加到文件上，则需要在命令后面追加“+参数”，如果想要把某个隐藏功能移出文件，则需要追加“-参数”。

```
i	无法对文件进行修改；若对目录设置了该参数，则仅能修改其中的子文件内容而不能新建或删除文件
a	仅允许补充（追加）内容，无法覆盖/删除内容（Append Only）
S	文件内容在变更后立即同步到硬盘（sync）
s	彻底从硬盘中删除，不可恢复（用0填充原文件所在硬盘区域）
A	不再修改这个文件或目录的最后访问时间（atime）
b	不再修改文件或目录的存取时间
D	检查压缩文件中的错误
d	使用dump命令备份时忽略本文件/目录
c	默认将文件或目录进行压缩
u	当删除该文件后依然保留其在硬盘中的数据，方便日后恢复
t	让文件系统支持尾部合并（tail-merging）
x	可以直接访问压缩文件中的内容
```

我们先来创建一个普通文件，然后立即尝试删除（这个操作肯定会成功）：

```
[root@linuxprobe ~]# echo "for Test" > linuxprobe
[root@linuxprobe ~]# rm linuxprobe
rm: remove regular file ‘linuxprobe’? y
```

实践是检验真理的唯一标准。如果您没有亲眼见证过隐藏权限强大功能的美妙，就一定不会相信原来Linux系统会如此安全。接下来我们再次新建一个普通文件，并为其设置不允许删除与覆盖（+a参数）权限，然后再尝试将这个文件删除：

```
[root@linuxprobe ~]# echo "for Test" > linuxprobe
[root@linuxprobe ~]# chattr +a linuxprobe
[root@linuxprobe ~]# rm linuxprobe
rm: remove regular file ‘linuxprobe’? y
rm: cannot remove ‘linuxprobe’: Operation not permitted
```

###### 5.4.2 lsattr命令

lsattr命令用于显示文件的隐藏权限，格式为“lsattr [参数] 文件

一旦使用lsattr命令后，文件上被赋予的隐藏权限马上就会原形毕露。此时可以按照显示的隐藏权限的类型（字母），使用chattr命令将其去掉：

```
[root@linuxprobe ~]# lsattr linuxprobe
-----a---------- linuxprobe
[root@linuxprobe ~]# chattr -a linuxprobe
[root@linuxprobe ~]# lsattr linuxprobe 
---------------- linuxprobe
[root@linuxprobe ~]# rm linuxprobe 
rm: remove regular file ‘linuxprobe’? y
```

##### 5.5 文件访问控制列表

前文讲解的一般权限、特殊权限、隐藏权限其实有一个共性—权限是针对某一类用户设置的。如果**希望对某个指定的用户进行单独的权限控制，就需要用到文件的访问控制列表（ACL）**了。

```
故事背景：
　　　　一个老师，给一个班的学员上课，他在linux的根目录下面建立了一个文件夹，只允许本班级的学员对该目录进行读写执行操作，其他人都不行，这时该目录的权限一般是770（一般我们设置权限都是所有者的权限大于所属组的权限，所属组的权限大于其他人的权限，依次往下），此时有个同学想试听我们的课程，他们只有读和执行的权限，没有写的权限，那么此时该怎么分配权限呢？此时的话所属组、所有者、其他人都不满足，这是就用到ACL权限。
　　　　也就是说，除了user和group外，其它人里只有这个同学可以进入和读取这个文件夹的内容，其它人不可以。
```

基于普通文件或目录设置ACL其实就是针对指定的用户或用户组设置文件或目录的操作权限。

- 如果针对某个目录设置了ACL，则目录中的文件会继承其ACL；

- 若针对文件设置了ACL，则文件不再继承其所在目录的ACL。

测试：

我们先切换到普通用户，然后尝试进入root管理员的家目录中。在没有针对普通用户对root管理员的家目录设置ACL之前，其执行结果如下所示：

```
[root@linuxprobe ~]# su - linuxprobe
Last login: Sat Mar 21 16:31:19 CST 2017 on pts/0
[linuxprobe@linuxprobe ~]$ cd /root
-bash: cd: /root: Permission denied
[linuxprobe@linuxprobe root]$ exit
```

###### 5.5.1 setfacl命令

setfacl命令用于管理文件的ACL规则，格式为：setfacl [参数] 文件名称

文件的ACL提供的是在所有者、所属组、其他人的读/写/执行权限之外的**特殊权限控制**，使用setfacl命令可以针对**单一用户**或**用户组**、**单一文件**或**目录**来进行读/写/执行**权限的控制**。

-R		针对目录文件的递归参数

-m		针对普通文件

-b		删除某个文件的ACL

##### 权限

文件的**所有者**以及**有`CAP_FOWNER`的用户**进程可以设置一个文件的acl。（在目前的linux系统上，**root用户**是唯一有`CAP_FOWNER`能力的用户）

```
设置用户权限：
	setfacl -m u:用户名:权限 文件
设置组权限：
	setfacl -m g:组名称:权限 文件 
设置最大有效权限：
	setfacl -m m:权限 文件

说明：
	acl权限中有个"mask"的选项，它就是ACL权限的最大权限，现在是rwx，当你设置某个用户或组的ACL权限时，要跟mask的权限“相与”之后产生的权限才是该用户的最终权限，也就是加入mask的最大权限是rx,但是你给st用户设置的是rwx权限，此时st用户它的权限只有rx的权限，因为与最大权限“相与”得出的结果就是rx

删除用户权限：
	setfacl -x u:用户名 文件名
删除组权限：
	setfacl -x g:组名称 文件名
删除整个权限：
	setfacl -b 文件名

设置递归权限：
    setfacl -m u:用户名:权限 -R 文件
    说明：
    递归只对该目录下面现有的子文件或目录有用，对于该目录下面新添加的子文件或目录没用
设置默认权限：
    setfacl -m d:u:用户名:权限 文件
    说明：
    默认权限只对该目录下面新建的文件或目录有效，对已经存在的子文件无效
```

下面来设置用户在/root目录上的权限：

```
[root@linuxprobe ~]# setfacl -Rm u:linuxprobe:rwx /root
[root@linuxprobe ~]# su - linuxprobe
Last login: Sat Mar 21 15:45:03 CST 2017 on pts/1
[linuxprobe@linuxprobe ~]$ cd /root
[linuxprobe@linuxprobe root]$ ls
anaconda-ks.cfg Downloads Pictures Public
[linuxprobe@linuxprobe root]$ cat anaconda-ks.cfg
[linuxprobe@linuxprobe root]$ exit
```

常用的ls命令是看不到ACL表信息的，但是却可以看到文件的权限最后一个点（**.**）变成了加号（**+**）,这就意味着该文件已经设置了ACL了

```
[root@linuxprobe ~]# ls -ld /root
dr-xrwx---+ 14 root root 4096 May 4 2017 /root
[linuxprobe@centos /]$ getfacl /root
getfacl: Removing leading '/' from absolute path names
# file: root
# owner: root
# group: root
user::r-x
user:linuxprobe:rwx
group::r-x
mask::r-x
other::---
```

删除ACL

```
[root@centos /]# setfacl -b /root/
[root@linuxprobe ~]# ls -ld /root
dr-xrwx---. 14 root root 4096 May 4 2017 /root
[linuxprobe@centos /]$ getfacl /root
getfacl: Removing leading '/' from absolute path names
# file: root
# owner: root
# group: root
user::r-x
group::r-x
mask::r-x
other::---
```

###### 5.5.2 getfacl命令

getfacl命令用于显示文件上设置的ACL信息，格式为：getfacl 文件名称

下面使用getfacl命令显示在root管理员家目录上设置的所有ACL信息。

```
[root@linuxprobe ~]# getfacl /root
getfacl: Removing leading '/' from absolute path names
# file: root
# owner: root
# group: root
user::r-x
user:linuxprobe:rwx
group::r-x
mask::rwx
other::---
```

##### 5.6 su命令与sudo服务

###### 5.6.1su命令不退出登录切换用户

可以解决切换用户身份的需求，使得当前用户在不退出登录的情况下，顺畅地切换到其他用户。

```
[root@linuxprobe ~]# id 
uid=0(root) gid=0(root) groups=0(root)
[root@linuxprobe ~]# su - linuxprobe
Last login: Wed Jan 4 01:17:25 EST 2017 on pts/0
[linuxprobe@linuxprobe ~]$ id 
uid=1000(linuxprobe) gid=1000(linuxprobe) groups=1000(linuxprobe) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

su命令与用户名之间有一个减号（-），这意味着完全切换到新的用户，即把环境变量信息也变更为新用户的相应信息，而不是保留原始的信息。强烈建议在切换用户身份时添加这个减号（-）。

###### 5.6.2 使用sudo命令把特定命令的执行权限赋予给指定用户

合理配置sudo服务，以便兼顾系统的安全性和用户的便捷性。

sudo服务的配置原则也很简单—在保证普通用户完成相应工作的前提下，尽可能少地赋予额外的权限。

```
-h	列出帮助信息
-l	列出当前用户可执行的命令
-u 用户名或UID值	以指定的用户身份执行命令
-k	清空密码的有效时间，下次执行sudo时需要再次进行密码验证
-b	在后台执行指定的命令
-p	更改询问密码的提示语
```

总结来说，sudo命令具有如下功能：

> 限制用户执行指定的命令：
>
> 记录用户执行的每一条命令；
>
> 配置文件（/etc/sudoers）提供集中的用户管理、权限与主机等参数；
>
> 验证密码的后5分钟内（默认值）无须再让用户再次验证密码。

可以使用sudo命令提供的visudo命令来配置用户权限。这条命令在配置用户权限时将禁止多个用户同时修改sudoers配置文件，还可以对配置文件内的参数进行语法检查，并在发现参数错误时进行报错。

> 只有root管理员才可以使用visudo命令编辑sudo服务的配置文件。

在sudo命令的配置文件中，按照下面的格式将第99行（大约）填写上指定的信息：

> **谁可以使用  允许使用的主机=（以谁的身份）  可执行命令的列表**

```
[root@linuxprobe ~]# visudo
 96 ##
 97 ## Allow root to run any commands anywhere
 98 root ALL=(ALL) ALL
 99 linuxprobe ALL=(ALL) ALL
```

在填写完毕后记得要先保存再退出，然后切换至指定的普通用户身份，此时就可以用sudo -l命令查看到所有可执行的命令了（下面的命令中，验证的是该普通用户的密码，而不是root管理员的密码，请读者不要搞混了）：

```
[root@linuxprobe ~]# su - linuxprobe
Last login: Thu Sep 3 15:12:57 CST 2017 on pts/1
[linuxprobe@linuxprobe ~]$ sudo -l
[sudo] password for linuxprobe:此处输入linuxprobe用户的密码
Matching Defaults entries for linuxprobe on this host:
requiretty, !visiblepw, always_set_home, env_reset, env_keep="COLORS
DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS", env_keep+="MAIL PS1
PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE
LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY
LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL
LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",
secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin
User linuxprobe may run the following commands on this host:
(ALL) ALL
```

接下来是见证奇迹的时刻！作为一名普通用户，是肯定不能看到root管理员的家目录（/root）中的文件信息的，但是，只需要在想执行的命令前面加上sudo命令就可以了：

```
[linuxprobe@linuxprobe ~]$ ls /root
ls: cannot open directory /root: Permission denied
[linuxprobe@linuxprobe ~]$ sudo ls /root
anaconda-ks.cfg Documents initial-setup-ks.cfg Pictures Templates
Desktop Downloads Music Public Videos
```

考虑到生产环境中不允许某个普通用户拥有整个系统中所有命令的最高执行权（这也不符合前文提到的权限赋予原则，即尽可能少地赋予权限），因此ALL参数就有些不合适了。因此只能赋予普通用户具体的命令以满足工作需求，这也受到了必要的权限约束。如果需要让某个用户只能使用root管理员的身份执行指定的命令，切记一定要给出该命令的绝对路径，否则系统会识别不出来。我们可以先使用whereis命令找出命令所对应的保存路径，然后把配置文件第99行的用户权限参数修改成对应的路径即可：

```
[linuxprobe@linuxprobe ~]$ exit
logout
[root@linuxprobe ~]# whereis cat
cat: /usr/bin/cat /usr/share/man/man1/cat.1.gz /usr/share/man/man1p/cat.1p.gz
[root@linuxprobe ~]# visudo
 96 ##
 97 ## Allow root to run any commands anywhere
 98 root ALL=(ALL) ALL
 99 linuxprobe ALL=(ALL) /usr/bin/cat
```

在编辑好后依然是先保存再退出。再次切换到指定的普通用户，然后尝试正常查看某个文件的内容，此时系统提示没有权限。这时再使用sudo命令就可以顺利地查看文件内容了：

```
[root@linuxprobe ~]# su - linuxprobe
Last login: Thu Sep 3 15:51:01 CST 2017 on pts/1
[linuxprobe@linuxprobe ~]$ cat /etc/shadow
cat: /etc/shadow: Permission denied
[linuxprobe@linuxprobe ~]$ sudo cat /etc/shadow
root:$6$GV3UVtX4ZGg6ygA6$J9pBuPGUSgZslj83jyoI7ThJla9ZAULku3BcncAYF00Uwk6Sqc4E36MnD1hLtlG9QadCpQCNVJs/5awHd0/pi1:16626:0:99999:7:::
bin:*:16141:0:99999:7:::
daemon:*:16141:0:99999:7:::
adm:*:16141:0:99999:7:::
lp:*:16141:0:99999:7:::
sync:*:16141:0:99999:7:::
shutdown:*:16141:0:99999:7:::
halt:*:16141:0:99999:7:::
mail:*:16141:0:99999:7:::
operator:*:16141:0:99999:7:::
games:*:16141:0:99999:7:::
ftp:*:16141:0:99999:7:::
nobody:*:16141:0:99999:7:::
………………省略部分文件内容………………
```

不知大家是否发觉在每次执行sudo命令后都会要求验证一下密码。虽然这个密码就是当前登录用户的密码，但是每次执行sudo命令都要输入一次密码其实也挺麻烦的，这时可以添加NOPASSWD参数，使得用户执行sudo命令时不再需要密码验证：

```
[linuxprobe@linuxprobe ~]$ exit
logout
[root@linuxprobe ~]# whereis poweroff
poweroff: /usr/sbin/poweroff /usr/share/man/man8/poweroff.8.gz
[root@linuxprobe ~]# visudo
 96 ##
 97 ## Allow root to run any commands anywhere
 98 root ALL=(ALL) ALL
 99 linuxprobe ALL=NOPASSWD: /usr/sbin/poweroff
```

这样，当切换到普通用户后再执行命令时，就不用再频繁地验证密码了，我们在日常工作中也就痛快至极了。

```
[root@linuxprobe ~]# su - linuxprobe
Last login: Thu Sep 3 15:58:31 CST 2017 on pts/1
[linuxprobe@linuxprobe ~]$ poweroff
User root is logged in on seat0.
Please retry operation after closing inhibitors and logging out other users.
Alternatively, ignore inhibitors and users with 'systemctl poweroff -i'.
[linuxprobe@linuxprobe ~]$ sudo poweroff
```

### 第六章 存储结构与磁盘划分

> Linux系统中的文件存储结构 
>
> 文件系统层次化标准（FHS，Filesystem Hierarchy Standard）
>
> udev硬件命名规则 硬盘分区的规划方法
>
> 详细地分析Linux系统中最常见的Ext3、Ext4与XFS文件系统的不同之处
>
> 着重练习硬盘设备分区、格式化以及挂载等常用的硬盘管理操作，以便熟练掌握文件系统的使用方法
>
> 完整地部署SWAP交换分区
>
> 配置quota磁盘配额服务
>
> 掌握ln命令带来的软硬链接

##### 6.1 一切从"/"开始

###### **文件**

在Linux系统中，目录、字符设备、块设备、套接字、打印机等都被抽象成了文件，Linux系统中一切都是文件。

在Linux系统中并不存在C/D/E/F等盘符，Linux系统中的一切文件都是从“根（/）”目录开始的，并按照文件系统层次化标准（FHS）采用树形结构来存放文件，以及定义了常见目录的用途。

Linux系统中的文件和目录名称是严格区分大小写的，并且文件名称中不得包含斜杠（/）。

![Linux存储架构](pics\Linux存储架构.png)

```
/boot	开机所需文件—内核、开机菜单以及所需配置文件等
/dev	以文件形式存放任何设备与接口
/etc	配置文件
/home	用户主目录
/bin	存放单用户模式下还可以操作的命令
/lib	开机时用到的函数库，以及/bin与/sbin下面的命令要调用的函数
/sbin	开机过程中需要的命令
/media	用于挂载设备文件的目录
/opt	放置第三方的软件
/root	系统管理员的家目录
/srv	一些网络服务的数据文件目录
/tmp	任何人均可使用的“共享”临时目录
/proc	虚拟文件系统，例如系统内核、进程、外部设备及网络状态等
/usr/local	用户自行安装的软件
/usr/sbin	Linux系统开机时不会使用到的软件/命令/脚本
/usr/share	帮助与说明文件，也可放置共享文件
/var	主要存放经常变化的文件，如日志
/lost+found	当文件系统发生错误时，将一些丢失的文件片段存放在这里
```

###### **路径**

> **绝对路径（absolute path）:**指的是从根目录（/）开始写起的文件或目录名称。
>
> **相对路径（relative path）:**指的是相对于当前路径的写法

##### 6.2 物理设备的命名规则

###### 系统内核中的udev设备管理器的硬件名称规范

```
IDE设备			/dev/hd[a-d]
SCSI/SATA/U盘	 /dev/sd[a-p]
软驱				/dev/fd[0-1]
打印机			   /dev/lp[0-15]
光驱				/dev/cdrom
鼠标				/dev/mouse
磁带机			   /dev/st0或/dev/ht0
```

一台主机上可以有多块硬盘，系统采用a～p来代表16块不同的硬盘（默认从a开始分配）

###### 硬盘的分区编号：

> 主分区或扩展分区的编号从1开始，到4结束；
>
> 逻辑分区从编号5开始。

分析一下/dev/sda5这个设备文件名称包含哪些信息，如图：

![硬盘命名规则](pics\硬盘命名规则.png)

###### 硬盘分区相关知识

```
硬盘设备是由大量的扇区组成的，每个扇区的容量为512字节。
其中第一个扇区最重要，它里面保存着主引导记录与分区表信息。
就第一个扇区来讲，主引导记录需要占用446字节，分区表为64字节，结束符占用2字节；
其中分区表中每记录一个分区信息就需要16字节，这样一来最多只有4个分区信息可以写到第一个扇区中，这4个分区就是4个主分区。第一个扇区中的数据信息如图
```

![硬盘的扇区](pics\硬盘的扇区.png)

```
第一个扇区最多只能创建出4个分区？于是为了解决分区个数不够的问题，可以将第一个扇区的分区表中16字节（原本要写入主分区信息）的空间（称之为扩展分区）拿出来指向另外一个分区。也就是说，扩展分区其实并不是一个真正的分区，而更像是一个占用16字节分区表空间的指针—一个指向另外一个分区的指针。

因此，用户一般会选择使用3个主分区加1个扩展分区的方法，然后在扩展分区中创建出数个逻辑分区，从而来满足多分区（大于4个）的需求。当然，就目前来讲大家只要明白为什么主分区不能超过4个就足够了。主分区、扩展分区、逻辑分区可以像下图那样来规划。
```

![逻辑分区](pics\逻辑分区.png)

##### 6.3 文件系统与数据资料

###### 文件系统

Linux系统支持数十种的文件系统，而最常见的文件系统如下所示。

**Ext3**：是一款日志文件系统，能够在系统异常宕机时避免文件系统资料丢失，并能自动修复数据的不一致与错误。然而，当硬盘容量较大时，所需的修复时间也会很长，而且也不能百分之百地保证资料不会丢失。它会把整个磁盘的每个写入动作的细节都预先记录下来，以便在发生异常宕机后能回溯追踪到被中断的部分，然后尝试进行修复。

**Ext4**：Ext3的改进版本，作为RHEL 6系统中的默认文件管理系统，它支持的存储容量高达1EB（1EB=1,073,741,824GB），且能够有无限多的子目录。另外，Ext4文件系统能够批量分配block块，从而极大地提高了读写效率。

**XFS**：是一种高性能的日志文件系统，而且是RHEL 7中默认的文件管理系统，它的优势在发生意外宕机后尤其明显，即可以快速地恢复可能被破坏的文件，而且强大的日志功能只用花费极低的计算和存储性能。并且它最大可支持的存储容量为18EB，这几乎满足了所有需求。

###### 硬盘分区并格式化后文件的存储方式inode表格block、实际内容block

Linux系统中有一个名为super block的“硬盘地图”。Linux并不是把文件内容直接写入到这个“硬盘地图”里面，而是在里面记录着整个文件系统的信息。因为如果把所有的文件内容都写入到这里面，它的体积将变得非常大，而且文件内容的查询与写入速度也会变得很慢。Linux只是把每个文件的权限与属性记录在inode中，而且每个文件占用一个独立的**inode表格**，该表格的大小默认为128字节，里面记录着如下信息：

> 该文件的访问权限（read、write、execute）；
>
> 该文件的所有者与所属组（owner、group）；
>
> 该文件的大小（size）；
>
> 该文件的创建或内容修改时间（ctime）；
>
> 该文件的最后一次访问时间（atime）；
>
> 该文件的修改时间（mtime）；
>
> 文件的特殊权限（SUID、SGID、SBIT）；
>
> 该文件的真实数据地址（point）。

而文件的**实际内容**则保存在**block**块中（大小可以是1KB、2KB或4KB），一个inode的默认大小仅为128B（Ext3），记录一个block则消耗4B。当文件的inode被写满后，Linux系统会自动分配出一个block块，专门用于像**inode**那样记录其他**block**块的信息，这样把各个block块的内容串到一起，就能够让用户读到完整的文件内容了。对于存储文件内容的block块，有下面两种常见情况（以4KB的block大小为例进行说明）。

> 情况1：文件很小（1KB），但依然会占用一个block，因此会潜在地浪费3KB。
>
> 情况2：文件很大（5KB），那么会占用两个block（5KB-4KB后剩下的1KB也要占用一个block）。

###### VFS（Virtual File System，虚拟文件系统）

众多的文件系统，为了使用户在读取或写入文件时不用关心底层的硬盘结构，Linux内核中的软件层为用户程序提供了一个VFS（Virtual File System，虚拟文件系统）接口，这样用户实际上在操作文件时就是统一对这个虚拟文件系统进行操作了。

![虚拟文件系统VFS](pics\虚拟文件系统VFS.png)

##### 6.4 挂载硬件设备

当用户需要使用硬盘设备或分区中的数据时，需要先将其与一个已存在的目录文件进行关联，而这个关联动作就是“挂载”。

###### mount命令

用于挂载文件系统，格式为“mount 文件系统 挂载目录”。

-a	挂载所有在/etc/fstab中定义的文件系统
-t	指定文件系统的类型

例如，要把设备/dev/sdb2挂载到/backup目录，只需要在mount命令中填写设备与挂载目录参数就行，系统会自动去判断要挂载文件的类型，因此只需要执行下述命令即可：

```
[root@linuxprobe ~]# mount /dev/sdb2 /backup
```

###### /etc/fstab文件

虽然按照上面的方法执行mount命令后就能立即使用文件系统了，但系统在重启后挂载就会失效，也就是说我们需要每次开机后都手动挂载一下。这肯定不是我们想要的效果，如果想让硬件设备和目录永久地进行自动关联，就必须把挂载信息按照指定的填写格式

```
“设备文件 挂载目录 格式类型 权限选项 是否备份 是否自检”
```

写入到/etc/fstab文件中。

```
设备文件		一般为设备的路径+设备名称，也可以写唯一识别码（UUID，Universally Unique Identifier）
挂载目录		指定要挂载到的目录，需在挂载前创建好
格式类型		指定文件系统的格式，比如Ext3、Ext4、XFS、SWAP、iso9660（此为光盘设备）等
权限选项		若设置为defaults，则默认权限为：rw, suid, dev, exec, auto, nouser, async
是否备份		若为1则开机后使用dump进行磁盘备份，为0则不备份
是否自检		若为1则开机后自动进行磁盘自检，为0则不自检
```

如果想将文件系统为ext4的硬件设备/dev/sdb2在开机后自动挂载到/backup目录上，并保持默认权限且无需开机自检，就需要在/etc/fstab文件中写入下面的信息，这样在系统重启后也会成功挂载。

```
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Wed May 4 19:26:23 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rhel-root / xfs defaults 1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot xfs defaults 1 2
/dev/mapper/rhel-swap swap swap defaults 0 0
/dev/cdrom /media/cdrom iso9660 defaults 0 0 
/dev/sdb2 /backup ext4 defaults 0 0
```

###### umount命令

用于撤销已经挂载的设备文件，格式为“umount [挂载点/设备文件]”。我们挂载文件系统的目的是为了使用硬件资源，而卸载文件系统就意味不再使用硬件的设备资源；相对应地，挂载操作就是把硬件设备与目录进行关联的动作，因此卸载操作只需要说明想要取消关联的设备文件或挂载目录的其中一项即可，一般不需要加其他额外的参数。我们来尝试手动卸载掉/dev/sdb2设备文件：

```
[root@linuxprobe ~]# umount /dev/sdb2
```

##### 6.5 添加硬盘设备

在虚拟机中模拟添加了硬盘设备后就应该能看到抽象成的硬盘设备文件了。按照前文讲解的udev服务命名规则，第二个被识别的SCSI设备应该会被保存为/dev/sdb，这个就是硬盘设备文件了。但在开始使用该硬盘之前还需要进行分区操作，例如从中取出一个2GB的分区设备以供后面的操作使用。

###### 6.5.1 fdisk命令

fdisk命令用于管理磁盘分区，格式为“fdisk  [磁盘名称]”，它提供了集添加、删除、转换分区等功能于一身。

这条命令的参数是交互式的

```
m	查看全部可用的参数
n	添加新的分区
d	删除某个分区信息
l	列出所有可用的分区类型
t	改变某个分区的类型
p	查看分区表信息
w	保存并退出
q	不保存直接退出
```

第1步：我们首先使用fdisk命令来尝试管理/dev/sdb硬盘设备。在看到提示信息后输入**参数p**来查看硬盘设备内已有的分区信息，其中包括了硬盘的容量大小、扇区个数等信息：

```
[root@linuxprobe ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x47d24a34.
Command (m for help): p
Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x47d24a34
Device Boot Start End Blocks Id System
```

第2步：输入**参数n**尝试添加新的分区。系统会要求您是选择继续输入参数p来创建主分区，还是输入参数e来创建扩展分区。这里输入参数p来创建一个主分区：

```
Command (m for help): n
Partition type:
p primary (0 primary, 0 extended, 4 free)
e extended
Select (default p): p
```

第3步：在确认创建一个主分区后，系统要求您先输入**主分区的编号**。我们在前文得知，主分区的编号范围是1～4，因此这里输入默认的1就可以了。接下来系统会提示定义起始的扇区位置，这不需要改动，我们敲击回车键保留默认设置即可，系统会自动计算出最靠前的空闲扇区的位置。最后，系统会要求定义分区的结束扇区位置，这其实就是要去定义整个分区的大小是多少。我们不用去计算扇区的个数，只需要输入+2G即可创建出一个容量为2GB的硬盘分区。

```
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048):此处敲击回车
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-41943039, default 41943039): +2G
Partition 1 of type Linux and of size 2 GiB is set
```

第4步：再次使用参数p来查看硬盘设备中的分区信息。果然就能看到一个名称为/dev/sdb1、起始扇区位置为2048、结束扇区位置为4196351的主分区了。这时候千万不要直接关闭窗口，而应该敲击**参数w**后回车，这样分区信息才是真正的写入成功啦。

```
Command (m for help): p
Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x47d24a34
Device Boot Start End Blocks Id System
/dev/sdb1 2048 4196351 2097152 83 Linux
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
Syncing disks.
```

第5步：在上述步骤执行完毕之后，Linux系统会自动把这个硬盘主分区抽象成/dev/sdb1设备文件。我们可以使用**file命令查看该文件的属性**，但是刘遄老师在讲课和工作中发现，有些时候系统并没有自动把分区信息同步给Linux内核，而且这种情况似乎还比较常见（但不能算作是严重的bug）。我们可以输入**partprobe**命令手动将分区信息同步到内核，而且一般推荐连续两次执行该命令，效果会更好。如果使用这个命令都无法解决问题，那么就重启计算机吧，这个杀手锏百试百灵，一定会有用的。

```
[root@linuxprobe ]# file /dev/sdb1
/dev/sdb1: cannot open (No such file or directory)
[root@linuxprobe ]# partprobe
[root@linuxprobe ]# partprobe
[root@linuxprobe ]# file /dev/sdb1
/dev/sdb1: block special
```

第6步：**格式化**。在Linux系统中用于格式化操作的命令**mkfs**。这条命令很有意思，因为在Shell终端中输入mkfs名后再敲击两下用于补齐命令的Tab键，会有如下所示的效果：

```
[root@linuxprobe ~]# mkfs
mkfs mkfs.cramfs mkfs.ext3 mkfs.fat mkfs.msdos mkfs.xfs
mkfs.btrfs mkfs.ext2 mkfs.ext4 mkfs.minix mkfs.vfat
```

对！这个mkfs命令很贴心地把常用的文件系统名称用后缀的方式保存成了多个命令文件，用起来也非常简单—mkfs.文件类型名称。例如要格式分区为XFS的文件系统，则命令应为**mkfs.xfs /dev/sdb1**。

```
[root@linuxprobe ~]# mkfs.xfs /dev/sdb1
meta-data=/dev/sdb1 isize=256gcount=4, agsize=131072 blks
 = sectsz=512 attr=2, projid32bit=1
 = crc=0
data = bsize=4096 blocks=524288, imaxpct=25
 = sunit=0 swidth=0 blks
naming =version 2 bsize=4096 ascii-ci=0 ftype=0
log =internal log bsize=4096 blocks=2560, version=2
 = sectsz=512 sunit=0 blks, lazy-count=1
realtime =none extsz=4096 blocks=0, rtextents=0
```

第7步：**挂载**。

> 首先是创建一个用于挂载设备的挂载点目录；
>
> 然后使用mount命令将存储设备与挂载点进行关联；
>
> 最后使用df -h命令来查看挂载状态和硬盘使用量信息。

```
[root@linuxprobe ~]# mkdir /newFS
[root@linuxprobe ~]# mount /dev/sdb1 /newFS/
[root@linuxprobe ~]# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/mapper/rhel-root 18G 3.5G 15G 20% /
devtmpfs 905M 0 905M 0% /dev
tmpfs 914M 140K 914M 1% /dev/shm
tmpfs 914M 8.8M 905M 1% /run
tmpfs 914M 0 914M 0% /sys/fs/cgroup
/dev/sr0 3.5G 3.5G 0 100% /media/cdrom
/dev/sda1 497M 119M 379M 24% /boot
/dev/sdb1 2.0G 33M 2.0G 2% /newFS
```

###### 6.5.2 du命令

既然存储设备已经顺利挂载，接下来就可以尝试通过挂载点目录向存储设备中写入文件了。在写入文件之前，先介绍一个用于**查看文件数据占用量的du命令**，其格式为“du [选项] [文件]”。

该命令就是用来查看一个或多个文件占用了多大的硬盘空间。还可以使用du -sh /*命令来查看在Linux系统根目录下所有一级目录分别占用的空间大小。

下面，我们先从某些目录中复制过来一批文件，然后查看这些文件总共占用了多大的容量：

```
[root@linuxprobe ~]# cp -rf /etc/* /newFS/
[root@linuxprobe ~]# ls /newFS/
abrt hosts pulse
adjtime hosts.allow purple
aliases hosts.deny qemu-ga
aliases.db hp qemu-kvm
alsa idmapd.conf radvd.conf
alternatives init.d rc0.d
anacrontab inittab rc1.d
………………省略部分输入信息………………
[root@linuxprobe ~]# du -sh /newFS/
33M /newFS/
```

使用mount命令挂载的设备文件会在系统下一次重启的时候失效。如果想让这个设备文件的**挂载永久有效**，则需要把挂载的信息写入到配置文件中：

```
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Wed May 4 19:26:23 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rhel-root / xfs defaults 1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot xfs defaults 1 2
/dev/mapper/rhel-swap swap swap defaults 0 0
/dev/cdrom /media/cdrom iso9660 defaults 0 0 
/dev/sdb1 /newFS xfs defaults 0 0
```

###### 6.6 添加交换分区

SWAP（交换）分区是一种通过在硬盘中预先划分一定的空间，然后将把内存中暂时不常用的数据临时存放到硬盘中，以便腾出物理内存空间让更活跃的程序服务来使用的技术，其设计目的是为了解决真实物理内存不足的问题。但由于交换分区毕竟是通过硬盘设备读写数据的，速度肯定要比物理内存慢，所以只有当真实的物理内存耗尽后才会调用交换分区的资源。

交换分区的创建过程与前文讲到的挂载并使用存储设备的过程非常相似。在对/dev/sdb存储设备进行分区操作前，有必要先说一下交换分区的划分建议：在生产环境中，交换分区的大小一般为真实物理内存的1.5～2倍，为了让大家更明显地感受交换分区空间的变化，这里取出一个大小为5GB的主分区作为交换分区资源。在分区创建完毕后保存并退出即可：

```
[root@linuxprobe ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0xb3d27ce1.
Command (m for help): n
Partition type:
p primary (1 primary, 0 extended, 3 free)
e extendedSelect (default p): p
Partition number (2-4, default 2): 
First sector (4196352-41943039, default 4196352): 此处敲击回车
Using default value 4196352
Last sector, +sectors or +size{K,M,G} (4196352-41943039, default 41943039): +5G
Partition 2 of type Linux and of size 5 GiB is set
Command (m for help): p
Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xb0ced57f
 Device Boot Start End Blocks Id System
/dev/sdb1 2048 4196351 2097152 83 Linux
/dev/sdb2 4196352 14682111 5242880 83 Linux
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

使用SWAP分区专用的格式化命令mkswap，对新建的主分区进行格式化操作：

```
[root@linuxprobe ~]# mkswap /dev/sdb2
Setting up swapspace version 1, size = 5242876 KiB
no label, UUID=2972f9cb-17f0-4113-84c6-c64b97c40c75
```

使用swapon命令把准备好的SWAP分区设备正式挂载到系统中。我们可以使用free -m命令查看交换分区的大小变化（由2047MB增加到7167MB）：

```
[root@linuxprobe ~]# free -m
total used free shared buffers cached
Mem: 1483 782 701 9 0 254
-/+ buffers/cache: 526 957
Swap: 2047 0 2047
[root@linuxprobe ~]# swapon /dev/sdb2
[root@linuxprobe ~]# free -m
total used free shared buffers cached
Mem: 1483 785 697 9 0 254
-/+ buffers/cache: 530 953
Swap: 7167 0 7167
```

为了能够让新的交换分区设备在重启后依然生效，需要按照下面的格式将相关信息写入到配置文件中，并记得保存：

```
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Wed May 4 19:26:23 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rhel-root / xfs defaults 1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot xfs defaults 1 2
/dev/mapper/rhel-swap swap swap defaults 0 0
/dev/cdrom /media/cdrom iso9660 defaults 0 0 
/dev/sdb1 /newFS xfs defaults 0 0 
/dev/sdb2 swap swap defaults 0 0 
```

##### 6.7 磁盘容量配额

root管理员就需要使用磁盘容量配额服务来限制某位用户或某个用户组针对特定文件夹可以使用的最大硬盘空间或最大文件个数，一旦达到这个最大值就不再允许继续使用。可以使用quota命令进行磁盘容量配额管理，从而限制用户的硬盘可用容量或所能创建的最大文件个数。quota命令还有软限制和硬限制的功能。

> 软限制：当达到软限制时会提示用户，但仍允许用户在限定的额度内继续使用。
>
> 硬限制：当达到硬限制时会提示用户，且强制终止用户的操作。

RHEL 7系统中已经安装了**quota磁盘容量配额服务程序包**，但存储设备却默认没有开启对quota的支持，此时**需要手动编辑配置文件**，**让**RHEL 7系统中的**/boot目录能够支持quota磁盘配额技术**。另外，对于学习过早期的Linux系统，或者具有RHEL 6系统使用经验的读者来说，这里需要特别注意。早期的Linux系统要想让硬盘设备支持quota磁盘容量配额服务，使用的是usrquota参数，而RHEL 7系统使用的则是uquota参数。在重启系统后使用mount命令查看，即可发现/boot目录已经支持quota磁盘配额技术了：

```
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Wed May 4 19:26:23 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rhel-root / xfs defaults 1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot xfs defaults,uquota 1 2
/dev/mapper/rhel-swap swap swap defaults 0 0
/dev/cdrom /media/cdrom iso9660 defaults 0 0 
/dev/sdb1 /newFS xfs defaults 0 0 
/dev/sdb2 swap swap defaults 0 0 
[root@linuxprobe Desktop]# mount | grep boot
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
[root@linuxprobe ~]# reboot
[root@linuxprobe ~]# mount | grep boot
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,usrquota)
```

**磁盘容量配额使用quota经典案例**：

编写脚本/app/bin/adduser.sh，创建用户myquota1，myquota2，myquota3，myquota4，myquota5,密码都为centos。都位于myquotagrp这个组。这五个用户有一个共享目录/app/home/myquota，并且只有myquotagrp这个组的所有成员对这个共享目录拥有全部权限，其他人没有任何权限。

```bash
  1 #!/bin/bash
  2 groupadd myquotagrp
  3 for num in {1..5}
  4 do
  5     useradd -d /app/home/myquota$num -G myquotagrp myquota$num
  6     echo "centos" |passwd --stdin myquota$num
  7 done
  8 mkdir /app/myquota
  9 chgrp myquotagrp /app/myquota
 10 chmod 2770 /app/myquota
```

接下来创建一个用于检查quota磁盘容量配额效果的用户tom，并针对/boot目录增加其他人的写权限，保证用户能够正常写入数据：

```
[root@linuxprobe ~]# useradd tom
[root@linuxprobe ~]# chmod -Rf o+w /boot
```

###### 6.7.1 xfs_quota命令

xfs_quota命令，是一个专门针对XFS文件系统来管理quota磁盘容量配额服务而设计的命令，格式为“xfs_quota [参数] 配额 文件系统”。

```
-c参数用于以参数的形式设置要执行的命令
-x参数是专家模式，让运维人员能够对quota服务进行更多复杂的配置
```

接下来我们使用xfs_quota命令来设置用户tom对/boot目录的quota磁盘容量配额。具体的限额控制包括：硬盘使用量的软限制和硬限制分别为3MB和6MB；创建文件数量的软限制和硬限制分别为3个和6个。

```
[root@linuxprobe ~]# xfs_quota -x -c 'limit bsoft=3m bhard=6m isoft=3 ihard=6 tom' /boot
[root@linuxprobe ~]# xfs_quota -x -c report /boot
User quota on /boot (/dev/sda1)   Blocks
User ID		Used 	Soft 	Hard 	Warn/Grace
---------- --------------------------------------------------
root    	95084 	0 		0 		00 [--------]
tom 		0 		3072 	6144 	00 [--------]
```

当配置好上述的各种软硬限制后，尝试切换到这个普通用户，然后分别尝试创建一个体积为5MB和8MB的文件。可以发现，在创建8MB的文件时受到了系统限制：

```
[root@linuxprobe ~]# su - tom
[tom@linuxprobe ~]$ dd if=/dev/zero of=/boot/tom bs=5M count=1
1+0 records in
1+0 records out
5242880 bytes (5.2 MB) copied, 0.123966 s, 42.3 MB/s
[tom@linuxprobe ~]$ dd if=/dev/zero of=/boot/tom bs=8M count=1
dd: error writing ‘/boot/tom’: Disk quota exceeded
1+0 records in
0+0 records out
6291456 bytes (6.3 MB) copied, 0.0201593 s, 312 MB/s
```

###### 6.7.2 edquota命令

edquota命令用于编辑用户的quota配额限制，格式为“edquota [参数] [用户] ”。

在为用户设置了quota磁盘容量配额限制后，可以使用edquota命令按需修改限额的数值。

```
-u参数	表示要针对哪个用户进行设置；
-g参数	表示要针对哪个用户组进行设置。
```

edquota命令会调用Vi或Vim编辑器来让root管理员修改要限制的具体细节。下面把用户tom的硬盘使用量的硬限额从5MB提升到8MB：

```
[root@linuxprobe ~]# edquota -u tom
Disk quotas for user tom (uid 1001):
 Filesystem blocks soft hard inodes soft hard
 /dev/sda1 6144 3072 8192 1 3 6
[root@linuxprobe ~]# su - tom
Last login: Mon Sep 7 16:43:12 CST 2017 on pts/0
[tom@linuxprobe ~]$ dd if=/dev/zero of=/boot/tom bs=8M count=1
1+0 records in
1+0 records out
8388608 bytes (8.4 MB) copied, 0.0268044 s, 313 MB/s
[tom@linuxprobe ~]$ dd if=/dev/zero of=/boot/tom bs=10M count=1
dd: error writing ‘/boot/tom’: Disk quota exceeded
1+0 records in
0+0 records out
8388608 bytes (8.4 MB) copied, 0.167529 s, 50.1 MB/s
```

##### 6.8 软硬方式链接

Linux系统中的“快捷方式”。在Windows系统中，快捷方式就是指向原始文件的一个链接文件，可以让用户从不同的位置来访问原始的文件；原文件一旦被删除或剪切到其他地方后，会导致链接文件失效。但是，这个看似简单的东西在Linux系统中可不太一样。

在Linux系统中存在硬链接和软连接两种文件。

**硬链接（hard link）：**可以将它理解为一个“指向原始文件inode的指针”，系统不为它分配独立的inode和文件。所以，硬链接文件与原始文件其实是同一个文件，只是名字不同。我们每添加一个硬链接，该文件的inode连接数就会增加1；而且只有当该文件的inode连接数为0时，才算彻底将它删除。换言之，由于硬链接实际上是指向原文件inode的指针，因此即便原始文件被删除，依然可以通过硬链接文件来访问。需要注意的是，由于技术的局限性，我们不能跨分区对目录文件进行链接。

**软链接（也称为符号链接[symbolic link]）：**仅仅包含所链接文件的路径名，因此能链接目录文件，也可以跨越文件系统进行链接。但是，当原始文件被删除后，链接文件也将失效，从这一点上来说与Windows系统中的“快捷方式”具有一样的性质。

###### **ln命令**

ln命令用于创建链接文件，格式为“ln [选项] 目标”。

在使用ln命令时，是否添加-s参数，将创建出性质不同的两种“快捷方式”。因此如果没有扎实的理论知识和实践经验做铺垫，尽管能够成功完成实验，但永远不会明白为什么会成功。

```
-s	创建“符号链接”（如果不带-s参数，则默认创建硬链接）
-f	强制创建文件或目录的链接
-i	覆盖前先询问
-v	显示创建链接的过程
```

创建一个类似于Windows系统中快捷方式的软链接。当原始文件被删除后，就无法读取新建的链接文件了。

```
[root@linuxprobe ~]# echo "Welcome to linuxprobe.com" > readme.txt
[root@linuxprobe ~]# ln -s readme.txt readit.txt
[root@linuxprobe ~]# cat readme.txt 
Welcome to linuxprobe.com
[root@linuxprobe ~]# cat readit.txt 
Welcome to linuxprobe.com
[root@linuxprobe ~]# ls -l readme.txt 
-rw-r--r-- 1 root root 26 Jan 11 00:08 readme.txt
[root@linuxprobe ~]# rm -f readme.txt 
[root@linuxprobe ~]# cat readit.txt 
cat: readit.txt: No such file or directory
```

创建一个硬链接，即相当于针对原始文件的硬盘存储位置创建了一个指针，这样一来，新创建的这个硬链接就不再依赖于原始文件的名称等信息，也不会因为原始文件的删除而导致无法读取。同时可以看到创建硬链接后，原始文件的硬盘链接数量增加到了2。

```
[root@linuxprobe ~]# echo "Welcome to linuxprobe.com" > readme.txt
[root@linuxprobe ~]# ln readme.txt readit.txt
[root@linuxprobe ~]# cat readme.txt 
Welcome to linuxprobe.com
[root@linuxprobe ~]# cat readit.txt 
Welcome to linuxprobe.com
[root@linuxprobe ~]# ls -l readme.txt 
-rw-r--r-- 2 root root 26 Jan 11 00:13 readme.txt
[root@linuxprobe ~]# rm -f readme.txt 
[root@linuxprobe ~]# cat readit.txt 
Welcome to linuxprobe.com
```

### 第七章 使用RAID与LVM磁盘阵列技术

深入讲解各个常用**RAID（Redundant Array of Independent Disks，独立冗余磁盘阵列）**技术方案的特性

通过实际部署**RAID 10、RAID 5+备份盘**等方案来更直观地查看RAID的强大效果，以便进一步满足生产环境对硬盘设备的IO读写速度和数据冗余备份机制的需求。

考虑到用户可能会动态调整存储资源，本章还将介绍**LVM（Logical Volume Manager，逻辑卷管理器）**的部署、扩容、缩小、快照以及卸载删除的相关知识。

读者在学完本章内容后，便可以在企业级生产环境中灵活运用RAID和LVM来满足对存储资源的高级管理需求了。

##### 7.1 RAID磁盘冗余阵列

1988年，加利福尼亚大学伯克利分校首次提出并定义了RAID技术的概念。RAID技术通过把多个硬盘设备组合成一个容量更大、安全性更好的磁盘阵列，并把数据切割成多个区段后分别存放在各个不同的物理硬盘设备上，然后利用分散读写技术来提升磁盘阵列整体的性能，同时把多个重要数据的副本同步到不同的物理硬盘设备上，从而起到了非常好的数据冗余备份效果。

RAID技术的设计初衷是减少因为采购硬盘设备带来的费用支出，但是与数据本身的价值相比较，现代企业更看重的则是RAID技术所具备的冗余备份机制以及带来的硬盘吞吐量的提升。RAID不仅**降低了硬盘设备损坏后丢失数据的几率**，**还提升了硬盘设备的读写速度**，所以它在绝大多数运营商或大中型企业中得以广泛部署和应用。

目前已有的RAID磁盘阵列的方案至少有十几种，而[刘遄](https://www.linuxprobe.com/)老师接下来会详细讲解RAID 0、RAID 1、RAID 5与RAID 10这4种最常见的方案。

RAID 0技术能够有效地提升硬盘数据的吞吐速度，但是不具备数据备份和错误修复能力。

RAID 1技术虽然十分注重数据的安全性，但是因为是在多块硬盘设备中写入了相同的数据，因此硬盘设备的利用率得以下降，从理论上来说，硬盘空间的真实可用率只有50%，由三块硬盘设备组成的RAID 1磁盘阵列的可用率只有33%左右。

RAID5技术是把硬盘设备的数据奇偶校验信息保存到其他硬盘设备中。RAID 5磁盘阵列组中数据的奇偶校验信息并不是单独保存到某一块硬盘设备中，而是存储到除自身以外的其他每一块硬盘设备上。RAID 5技术实际上没有备份硬盘中的真实数据信息，而是当硬盘设备出现问题后**通过奇偶校验信息来尝试重建损坏的数据**。RAID这样的技术特性“妥协”地兼顾了硬盘设备的读写速度、数据安全性与存储成本问题。

![raid5](pics\raid5.gif)

RAID 10技术是RAID 1+RAID 0技术的一个“组合体”。需要至少4块硬盘来组建，其中先分别两两制作成RAID 1磁盘阵列，以保证数据的安全性；然后再对两个RAID 1磁盘阵列实施RAID 0技术，进一步提高硬盘设备的读写速度。这样从理论上来讲，只要坏的不是同一组中的所有硬盘，那么最多可以损坏50%的硬盘设备而不丢失数据。由于RAID 10技术继承了RAID 0的高读写速度和RAID 1的数据安全性，在不考虑成本的情况下RAID 10的性能都超过了RAID 5，因此当前成为广泛使用的一种存储技术。

![raid-10-1024x508](pics\raid-10-1024x508.png)

###### 7.1.1 部署磁盘阵列

首先，需要在虚拟机中添加4块硬盘设备来制作一个RAID 10磁盘阵列

![虚拟机添加硬盘](pics\虚拟机添加硬盘.jpg)

**mdadm命令**用于管理[Linux系统](https://www.linuxprobe.com/)中的软件RAID硬盘阵列，格式为：

```
mdadm [模式] <RAID设备名称> [选项] [成员设备名称]

-a	检测设备名称
-n	指定设备数量
-l	指定RAID级别
-C	创建
-v	显示过程
-f	模拟设备损坏
-r	移除设备
-Q	查看摘要信息
-D	查看详细信息
-S	停止RAID磁盘阵列
```

其中，-C参数代表创建一个RAID阵列卡；-v参数显示创建的过程，同时在后面追加一个设备名称/dev/md0，这样/dev/md0就是创建后的RAID磁盘阵列的名称；-a yes参数代表自动创建设备文件；-n 4参数代表使用4块硬盘来部署这个RAID磁盘阵列；而-l 10参数则代表RAID 10方案；最后再加上4块硬盘设备的名称就搞定了。

```
[root@linuxprobe ~]# mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sdb /dev/sdc /dev/sdd /dev/sde
mdadm: layout defaults to n2
mdadm: layout defaults to n2
mdadm: chunk size defaults to 512K
mdadm: size set to 20954624K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```

其次，把制作好的RAID磁盘阵列格式化为ext4格式。

```
[root@linuxprobe ~]# mkfs.ext4 /dev/md0
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
2621440 inodes, 10477312 blocks
523865 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2157969408
320 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
4096000, 7962624
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

再次，创建挂载点然后把硬盘设备进行挂载操作。挂载成功后可看到可用空间为40GB。

```
[root@linuxprobe ~]# mkdir /RAID
[root@linuxprobe ~]# mount /dev/md0 /RAID
[root@linuxprobe ~]# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/mapper/rhel-root 18G 3.0G 15G 17% /
devtmpfs 905M 0 905M 0% /dev
tmpfs 914M 84K 914M 1% /dev/shm
tmpfs 914M 8.9M 905M 1% /run
tmpfs 914M 0 914M 0% /sys/fs/cgroup
/dev/sr0 3.5G 3.5G 0 100% /media/cdrom
/dev/sda1 497M 119M 379M 24% /boot
/dev/md0 40G 49M 38G 1% /RAID
```

最后，查看/dev/md0磁盘阵列的详细信息，并把挂载信息写入到配置文件中，使其永久生效。

```
[root@linuxprobe ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Tue May 5 07:43:26 2017
Raid Level : raid10
Array Size : 41909248 (39.97 GiB 42.92 GB)
Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
Raid Devices : 4
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Tue May 5 07:46:59 2017
State : clean
Active Devices : 4
Working Devices : 4
Failed Devices : 0
Spare Devices : 0
Layout : near=2
Chunk Size : 512K
Name : localhost.localdomain:0 (local to host localhost.localdomain)
UUID : cc9a87d4:1e89e175:5383e1e8:a78ec62c
Events : 17
Number Major Minor RaidDevice State
0 8 16 0 active sync /dev/sdb
1 8 32 1 active sync /dev/sdc
2 8 48 2 active sync /dev/sdd
3 8 64 3 active sync /dev/sde
[root@linuxprobe ~]# echo "/dev/md0 /RAID ext4 defaults 0 0" >> /etc/fstab
```

在磁盘阵列使用完成后，需要删除磁盘阵列，先取消磁盘挂载，再输入"mdadm -S /dev/md0"删除建立的磁盘阵列，最后删除分区。

```
[root@linuxprobe Desktop]# umount /RAID
[root@linuxprobe Desktop]# mdadm -S /dev/md0
mdadm: stopped /dev/md0

删除启动设置挂载阵列/dev/md0
[root@linuxprobe Desktop]# vim /etc/fstab 
```

###### 7.1.2 损坏磁盘阵列及修复

首先确认有一块物理硬盘设备出现损坏不能再继续正常使用后，应该使用mdadm命令来予以移除之后查看下RAID磁盘阵列组的状态已经被改变：

```
[root@linuxprobe ~]# mdadm /dev/md0 -f /dev/sdb
mdadm: set /dev/sdb faulty in /dev/md0
[root@linuxprobe ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Fri May 8 08:11:00 2017
Raid Level : raid10
Array Size : 41909248 (39.97 GiB 42.92 GB)
Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
Raid Devices : 4
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Fri May 8 08:27:18 2017
State : clean, degraded
Active Devices : 3
Working Devices : 3
Failed Devices : 1
Spare Devices : 0
Layout : near=2
Chunk Size : 512K
Name : linuxprobe.com:0 (local to host linuxprobe.com)
UUID : f2993bbd:99c1eb63:bd61d4d4:3f06c3b0
Events : 21
Number Major Minor RaidDevice State
0 0 0 0 removed
1 8 32 1 active sync /dev/sdc
2 8 48 2 active sync /dev/sdd
3 8 64 3 active sync /dev/sde
0 8 16 - faulty /dev/sdb
```

在RAID 10级别的磁盘阵列中，当RAID 1磁盘阵列中存在一个故障盘时并不影响RAID 10磁盘阵列的使用。当购买了新的硬盘设备后再使用mdadm命令来予以替换即可，在此期间我们可以在/RAID目录中正常地创建或删除文件。由于我们是在虚拟机中模拟硬盘，所以先重启系统，然后再把新的硬盘添加到RAID磁盘阵列中。

```
[root@linuxprobe ~]# umount /RAID
[root@linuxprobe ~]# mdadm /dev/md0 -a /dev/sdb
[root@linuxprobe ~]# mdadm -D /dev/md0
/dev/md0:
 Version : 1.2
 Creation Time : Mon Jan 30 00:08:56 2017
 Raid Level : raid10
 Array Size : 41909248 (39.97 GiB 42.92 GB)
 Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
 Raid Devices : 4
 Total Devices : 4
 Persistence : Superblock is persistent
 Update Time : Mon Jan 30 00:19:53 2017
 State : clean 
 Active Devices : 4
Working Devices : 4
 Failed Devices : 0
 Spare Devices : 0
 Layout : near=2
 Chunk Size : 512K
 Name : localhost.localdomain:0 (local to host localhost.localdomain)
 UUID : d3491c05:cfc81ca0:32489f04:716a2cf0
 Events : 56
 Number Major Minor RaidDevice State
 4 8 16 0 active sync /dev/sdb
 1 8 32 1 active sync /dev/sdc
 2 8 48 2 active sync /dev/sdd
 3 8 64 3 active sync /dev/sde
[root@linuxprobe ~]# mount -a
```

###### 7.1.3 磁盘阵列+备份盘

部署RAID 5磁盘阵列时，至少需要用到3块硬盘，还需要再加一块备份硬盘，所以总计需要在虚拟机中模拟4块硬盘设备

现在创建一个RAID 5磁盘阵列+备份盘。在下面的命令中，参数-n 3代表创建这个RAID 5磁盘阵列所需的硬盘数，参数-l 5代表RAID的级别，而参数-x 1则代表有一块备份盘。当查看/dev/md0（即RAID 5磁盘阵列的名称）磁盘阵列的时候就能看到有一块备份盘在等待中了。

```
[root@linuxprobe ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /dev/sdc /dev/sdd /dev/sde
mdadm: layout defaults to left-symmetric
mdadm: layout defaults to left-symmetric
mdadm: chunk size defaults to 512K
mdadm: size set to 20954624K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
[root@linuxprobe ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Fri May 8 09:20:35 2017
Raid Level : raid5
Array Size : 41909248 (39.97 GiB 42.92 GB)
Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
Raid Devices : 3
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Fri May 8 09:22:22 2017
State : clean
Active Devices : 3
Working Devices : 4
Failed Devices : 0
Spare Devices : 1
Layout : left-symmetric
Chunk Size : 512K
Name : linuxprobe.com:0 (local to host linuxprobe.com)
UUID : 44b1a152:3f1809d3:1d234916:4ac70481
Events : 18
Number Major Minor RaidDevice State
0 8 16 0 active sync /dev/sdb
1 8 32 1 active sync /dev/sdc
4 8 48 2 active sync /dev/sdd
3 8 64 - spare /dev/sde
```

现在将部署好的RAID 5磁盘阵列格式化为ext4文件格式，然后挂载到目录上，之后就可以使用了。

```
[root@linuxprobe ~]# mkfs.ext4 /dev/md0
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
2621440 inodes, 10477312 blocks
523865 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2157969408
320 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
4096000, 7962624
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
[root@linuxprobe ~]# echo "/dev/md0 /RAID ext4 defaults 0 0" >> /etc/fstab
[root@linuxprobe ~]# mkdir /RAID
[root@linuxprobe ~]# mount -a
```

最后是见证奇迹的时刻！我们再次把硬盘设备/dev/sdb移出磁盘阵列，然后迅速查看/dev/md0磁盘阵列的状态，就会发现备份盘已经被自动顶替上去并开始了数据同步。RAID中的这种备份盘技术非常实用，可以在保证RAID磁盘阵列数据安全性的基础上进一步提高数据可靠性，所以，如果公司不差钱的话还是再买上一块备份盘以防万一。

```
[root@linuxprobe ~]# mdadm /dev/md0 -f /dev/sdb
mdadm: set /dev/sdb faulty in /dev/md0
[root@linuxprobe ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Fri May 8 09:20:35 2017
Raid Level : raid5
Array Size : 41909248 (39.97 GiB 42.92 GB)
Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
Raid Devices : 3
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Fri May 8 09:23:51 2017
State : active, degraded, recovering
Active Devices : 2
Working Devices : 3
Failed Devices : 1
Spare Devices : 1
Layout : left-symmetric
Chunk Size : 512K
Rebuild Status : 0% complete
Name : linuxprobe.com:0 (local to host linuxprobe.com)
UUID : 44b1a152:3f1809d3:1d234916:4ac70481
Events : 21
Number Major Minor RaidDevice State
3 8 64 0 spare rebuilding /dev/sde
1 8 32 1 active sync /dev/sdc
4 8 48 2 active sync /dev/sdd
0 8 16 - faulty /dev/sdb
```

##### 7.2 LVM逻辑卷管理器

LVM（逻辑卷管理器）是硬盘设备资源管理技术，可以允许用户对硬盘资源进行动态调整。

初衷是为了解决硬盘设备在创建分区后不易修改分区大小的缺陷。尽管对传统的硬盘分区进行强制扩容或缩容从理论上来讲是可行的，但是却可能造成数据的丢失。而LVM技术是在硬盘分区和文件系统之间添加了一个逻辑层，它提供了一个抽象的卷组，可以把多块硬盘进行卷组合并。这样一来，用户不必关心物理硬盘设备的底层架构和布局，就可以实现对硬盘分区的动态调整。LVM的技术架构如图

![逻辑卷](pics\逻辑卷.png)

**物理卷（PV）**处于LVM中的最底层，可以将其理解为物理硬盘、硬盘分区或者RAID磁盘阵列，这都可以。**卷组（VG）**建立在物理卷之上，一个卷组可以包含多个物理卷，而且在卷组创建之后也可以继续向其中添加新的物理卷。**逻辑卷（LV）**是用卷组中空闲的资源建立的，并且逻辑卷在建立后可以动态地扩展或缩小空间。这就是LVM的核心理念。

###### 7.2.1 部署逻辑卷

部署LVM时，需要逐个配置物理卷、卷组和逻辑卷。

```
功能/命令	  物理卷管理		卷组管理		逻辑卷管理
扫描			pvscan		 vgscan			lvscan
建立			pvcreate	 vgcreate		lvcreate
显示			pvdisplay	 vgdisplay		lvdisplay
删除			pvremove	 vgremove		lvremove
扩展						 vgextend		lvextend
缩小						 vgreduce		lvreduce
```

为了避免多个实验之间相互发生冲突，请大家自行将虚拟机还原到初始状态，并在虚拟机中添加两块新硬盘设备，然后开机。

在虚拟机中添加两块新硬盘设备的目的，是为了更好地演示LVM理念中用户无需关心底层物理硬盘设备的特性。我们先对这两块新硬盘进行创建物理卷的操作，可以将该操作简单理解成让硬盘设备支持LVM技术，或者理解成是把硬盘设备加入到LVM技术可用的硬件资源池中，然后对这两块硬盘进行卷组合并，卷组的名称可以由用户来自定义。接下来，根据需求把合并后的卷组切割出一个约为150MB的逻辑卷设备，最后把这个逻辑卷设备格式化成EXT4文件系统后挂载使用。

**第1步**：让新添加的两块硬盘设备支持LVM技术。

```
[root@linuxprobe ~]# pvcreate /dev/sdb /dev/sdc
 Physical volume "/dev/sdb" successfully created
 Physical volume "/dev/sdc" successfully created
```

**第2步**：把两块硬盘设备加入到storage卷组中，然后查看卷组的状态。

```
[root@linuxprobe ~]# vgcreate storage /dev/sdb /dev/sdc
 Volume group "storage" successfully created
[root@linuxprobe ~]# vgdisplay
--- Volume group ---
 VG Name storage
 System ID 
 Format lvm2
 Metadata Areas 2
 Metadata Sequence No 1
 VG Access read/write
 VG Status resizable
 MAX LV 0
 Cur LV 0
 Open LV 0
 Max PV 0
 Cur PV 2
 Act PV 2
 VG Size 39.99 GiB
 PE Size 4.00 MiB
 Total PE 10238
 Alloc PE / Size 0 / 0  Free PE / Size 10238 / 39.99 GiB
 VG UUID KUeAMF-qMLh-XjQy-ArUo-LCQI-YF0o-pScxm1
………………省略部分输出信息………………
```

**第3步**：切割出一个约为150MB的逻辑卷设备。

这里需要注意切割单位的问题。在对逻辑卷进行切割时有两种计量单位。第一种是以容量为单位，所使用的参数为-L。例如，使用-L 150M生成一个大小为150MB的逻辑卷。另外一种是以基本单元的个数为单位，所使用的参数为-l。每个基本单元的大小默认为4MB。例如，使用-l 37可以生成一个大小为37×4MB=148MB的逻辑卷。

```
[root@linuxprobe ~]# lvcreate -n vo -l 37 storage
 Logical volume "vo" created
[root@linuxprobe ~]# lvdisplay 
 --- Logical volume ---
 LV Path /dev/storage/vo
 LV Name vo
 VG Name storage
 LV UUID D09HYI-BHBl-iXGr-X2n4-HEzo-FAQH-HRcM2I
 LV Write Access read/write
 LV Creation host, time localhost.localdomain, 2017-02-01 01:22:54 -0500
 LV Status available
 # open 0
 LV Size 148.00 MiB
 Current LE 37
 Segments 1
 Allocation inherit
 Read ahead sectors auto
 - currently set to 8192
 Block device 253:2
………………省略部分输出信息………………
```

**第4步**：把生成好的逻辑卷进行格式化，然后挂载使用。

Linux系统会把LVM中的逻辑卷设备存放在/dev设备目录中（实际上是做了一个符号链接），同时会以卷组的名称来建立一个目录，其中保存了逻辑卷的设备映射文件（即/dev/卷组名称/逻辑卷名称）。

```
[root@linuxprobe ~]# mkfs.ext4 /dev/storage/vo 
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
38000 inodes, 151552 blocks
7577 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=33816576
19 block groups
8192 blocks per group, 8192 fragments per group
2000 inodes per group
Superblock backups stored on blocks: 
 8193, 24577, 40961, 57345, 73729
Allocating group tables: done 
Writing inode tables: done 
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done 
[root@linuxprobe ~]# mkdir /linuxprobe
[root@linuxprobe ~]# mount /dev/storage/vo /linuxprobe
```

**第5步**：查看挂载状态，并写入到配置文件，使其永久生效。

```
[root@linuxprobe ~]# df -h
 Filesystem Size Used Avail Use% Mounted on
 /dev/mapper/rhel-root 18G 3.0G 15G 17% /
 devtmpfs 905M 0 905M 0% /dev
 tmpfs 914M 140K 914M 1% /dev/shm
 tmpfs 914M 8.8M 905M 1% /run
 tmpfs 914M 0 914M 0% /sys/fs/cgroup
 /dev/sr0 3.5G 3.5G 0 100% /media/cdrom
 /dev/sda1 497M 119M 379M 24% /boot
 /dev/mapper/storage-vo 145M 7.6M 138M 6% /linuxprobe
 [root@linuxprobe ~]# echo "/dev/storage/vo /linuxprobe ext4 defaults 0 0" >> /etc/fstab
```

###### 7.2.2 扩容逻辑卷

用户在使用存储设备时感知不到设备底层的架构和布局，更不用关心底层是由多少块硬盘组成的，只要卷组中有足够的资源，就可以一直为逻辑卷扩容。扩展前请一定要记得卸载设备和挂载点的关联。

```
[root@linuxprobe ~]# umount /linuxprobe
```

**第1步**：把上一个实验中的逻辑卷vo扩展至290MB。

```
[root@linuxprobe ~]# lvextend -L 290M /dev/storage/vo
 Rounding size to boundary between physical extents: 292.00 MiB
 Extending logical volume vo to 292.00 MiB
 Logical volume vo successfully resized
```

**第2步**：检查硬盘完整性，并重置硬盘容量。

```
[root@linuxprobe ~]# e2fsck -f /dev/storage/vo
e2fsck 1.42.9 (28-Dec-2013)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/storage/vo: 11/38000 files (0.0% non-contiguous), 10453/151552 blocks
[root@linuxprobe ~]# resize2fs /dev/storage/vo
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/storage/vo to 299008 (1k) blocks.
The filesystem on /dev/storage/vo is now 299008 blocks long.
```

**第3步**：重新挂载硬盘设备并查看挂载状态。

```
[root@linuxprobe ~]# mount -a
[root@linuxprobe ~]# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/mapper/rhel-root 18G 3.0G 15G 17% /
devtmpfs 985M 0 985M 0% /dev
tmpfs 994M 80K 994M 1% /dev/shm
tmpfs 994M 8.8M 986M 1% /run
tmpfs 994M 0 994M 0% /sys/fs/cgroup
/dev/sr0 3.5G 3.5G 0 100% /media/cdrom
/dev/sda1 497M 119M 379M 24% /boot
/dev/mapper/storage-vo 279M 2.1M 259M 1% /linuxprobe
```

###### 7.2.3 缩小逻辑卷

相较于扩容逻辑卷，在对逻辑卷进行缩容操作时，其丢失数据的风险更大。所以在生产环境中执行相应操作时，一定要提前备份好数据。另外Linux系统规定，在对LVM逻辑卷进行缩容操作之前，要先检查文件系统的完整性（当然这也是为了保证我们的数据安全）。在执行缩容操作前记得先把文件系统卸载掉。

```
[root@linuxprobe ~]# umount /linuxprobe
```

**第1步**：检查文件系统的完整性。

```
[root@linuxprobe ~]# e2fsck -f /dev/storage/vo
e2fsck 1.42.9 (28-Dec-2013)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/storage/vo: 11/74000 files (0.0% non-contiguous), 15507/299008 blocks
```

**第2步**：把逻辑卷vo的容量减小到120MB。

```
[root@linuxprobe ~]# resize2fs /dev/storage/vo 120M
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/storage/vo to 122880 (1k) blocks.
The filesystem on /dev/storage/vo is now 122880 blocks long.
[root@linuxprobe ~]# lvreduce -L 120M /dev/storage/vo
 WARNING: Reducing active logical volume to 120.00 MiB
 THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vo? [y/n]: y
 Reducing logical volume vo to 120.00 MiB
 Logical volume vo successfully resized
```

**第3步**：重新挂载文件系统并查看系统状态。

```
[root@linuxprobe ~]# mount -a
[root@linuxprobe ~]# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/mapper/rhel-root 18G 3.0G 15G 17% /
devtmpfs 985M 0 985M 0% /dev
tmpfs 994M 80K 994M 1% /dev/shm
tmpfs 994M 8.8M 986M 1% /run
tmpfs 994M 0 994M 0% /sys/fs/cgroup
/dev/sr0 3.5G 3.5G 0 100% /media/cdrom
/dev/sda1 497M 119M 379M 24% /boot
/dev/mapper/storage-vo 113M 1.6M 103M 2% /linuxprobe
```

###### 7.2.4 逻辑卷快照

LVM还具备有“快照卷”功能，该功能类似于虚拟机软件的还原时间点功能。例如，可以对某一个逻辑卷设备做一次快照，如果日后发现数据被改错了，就可以利用之前做好的快照卷进行覆盖还原。LVM的快照卷功能有两个特点：

> 快照卷的容量必须等同于逻辑卷的容量；
>
> 快照卷仅一次有效，一旦执行还原操作后则会被立即自动删除。

首先查看卷组的信息。

```
[root@linuxprobe ~]# vgdisplay
 --- Volume group ---
 VG Name storage
 System ID 
 Format lvm2
 Metadata Areas 2
 Metadata Sequence No 4
 VG Access read/write
 VG Status resizable
 MAX LV 0
 Cur LV 1
 Open LV 1
 Max PV 0
 Cur PV 2
 Act PV 2
 VG Size 39.99 GiB
 PE Size 4.00 MiB
 Total PE 10238
 Alloc PE / Size 30 / 120.00 MiB Free PE / Size 10208 / 39.88 GiB
 VG UUID CTaHAK-0TQv-Abdb-R83O-RU6V-YYkx-8o2R0e
………………省略部分输出信息………………
```

通过卷组的输出信息可以清晰看到，卷组中已经使用了120MB的容量，空闲容量还有39.88GB。接下来用重定向往逻辑卷设备所挂载的目录中写入一个文件。

```
[root@linuxprobe ~]# echo "Welcome to Linuxprobe.com" > /linuxprobe/readme.txt
[root@linuxprobe ~]# ls -l /linuxprobe
total 14
drwx------. 2 root root 12288 Feb 1 07:18 lost+found
-rw-r--r--. 1 root root 26 Feb 1 07:38 readme.txt
```

**第1步**：使用-s参数生成一个快照卷，使用-L参数指定切割的大小。另外，还需要在命令后面写上是针对哪个逻辑卷执行的快照操作。

```
[root@linuxprobe ~]#  lvcreate -L 120M -s -n SNAP /dev/storage/vo
 Logical volume "SNAP" created
[root@linuxprobe ~]# lvdisplay
--- Logical volume ---
 LV Path /dev/storage/SNAP
 LV Name SNAP
 VG Name storage
 LV UUID BC7WKg-fHoK-Pc7J-yhSd-vD7d-lUnl-TihKlt
 LV Write Access read/write
 LV Creation host, time localhost.localdomain, 2017-02-01 07:42:31 -0500
 LV snapshot status active destination for vo
 LV Status available
 # open 0
 LV Size 120.00 MiB
 Current LE 30
 COW-table size 120.00 MiB
 COW-table LE 30
 Allocated to snapshot 0.01%
 Snapshot chunk size 4.00 KiB
 Segments 1
 Allocation inherit
 Read ahead sectors auto
 - currently set to 8192
 Block device 253:3
………………省略部分输出信息………………
```

**第2步**：在逻辑卷所挂载的目录中创建一个100MB的垃圾文件，然后再查看快照卷的状态。可以发现存储空间占的用量上升了。

```
[root@linuxprobe ~]# dd if=/dev/zero of=/linuxprobe/files count=1 bs=100M
1+0 records in
1+0 records out
104857600 bytes (105 MB) copied, 3.35432 s, 31.3 MB/s
[root@linuxprobe ~]# lvdisplay
 --- Logical volume ---
 LV Path /dev/storage/SNAP
 LV Name SNAP
 VG Name storage
 LV UUID BC7WKg-fHoK-Pc7J-yhSd-vD7d-lUnl-TihKlt
 LV Write Access read/write
 LV Creation host, time localhost.localdomain, 2017-02-01 07:42:31 -0500
 LV snapshot status active destination for vo
 LV Status available
 # open 0
 LV Size 120.00 MiB
 Current LE 30
 COW-table size 120.00 MiB
 COW-table LE 30
 Allocated to snapshot 83.71%
 Snapshot chunk size 4.00 KiB
 Segments 1
 Allocation inherit
 Read ahead sectors auto
 - currently set to 8192
 Block device 253:3
```

**第3步**：为了校验SNAP快照卷的效果，需要对逻辑卷进行快照还原操作。在此之前记得先卸载掉逻辑卷设备与目录的挂载。

```
[root@linuxprobe ~]# umount /linuxprobe
[root@linuxprobe ~]# lvconvert --merge /dev/storage/SNAP
 Merging of volume SNAP started.
 vo: Merged: 21.4%
 vo: Merged: 100.0%
 Merge of snapshot into logical volume vo has finished.
 Logical volume "SNAP" successfully removed
```

**第4步**：快照卷会被自动删除掉，并且刚刚在逻辑卷设备被执行快照操作后再创建出来的100MB的垃圾文件也被清除了。

```
[root@linuxprobe ~]# mount -a
[root@linuxprobe ~]# ls /linuxprobe/
lost+found readme.txt
```

###### 7.2.5 删除逻辑卷

当生产环境中想要重新部署LVM或者不再需要使用LVM时，则需要执行LVM的删除操作。为此，需要提前备份好重要的数据信息，然后**依次删除逻辑卷、卷组、物理卷设备**，这个顺序不可颠倒。

**第1步**：取消逻辑卷与目录的挂载关联，删除配置文件中永久生效的设备参数。

```
[root@linuxprobe ~]# umount /linuxprobe
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Fri Feb 19 22:08:59 2017
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/rhel-root / xfs defaults 1 1
UUID=50591e35-d47a-4aeb-a0ca-1b4e8336d9b1 /boot xfs defaults 1 2
/dev/mapper/rhel-swap swap swap defaults 0 0
/dev/cdrom /media/cdrom iso9660 defaults 0 0 
/dev/storage/vo /linuxprobe ext4 defaults 0 0
```

**第2步**：删除逻辑卷设备，需要输入y来确认操作。

```
[root@linuxprobe ~]# lvremove /dev/storage/vo 
Do you really want to remove active logical volume vo? [y/n]: y
 Logical volume "vo" successfully removed
```

**第3步**：删除卷组，此处只写卷组名称即可，不需要设备的绝对路径。

```
[root@linuxprobe ~]# vgremove storage
 Volume group "storage" successfully removed
```

**第4步**：删除物理卷设备。

```
[root@linuxprobe ~]# pvremove /dev/sdb /dev/sdc
 Labels on physical volume "/dev/sdb" successfully wiped
 Labels on physical volume "/dev/sdc" successfully wiped
```

在上述操作执行完毕之后，再执行lvdisplay、vgdisplay、pvdisplay命令来查看LVM的信息时就不会再看到信息了（前提是上述步骤的操作是正确的）。

### 第8章 Iptables与Firewalld防火墙

带领读者从理论层面和实际层面正确地认识在这两款防火墙之间的关系。

本章将分别使用iptables、firewall-cmd、firewall-config和TCP Wrappers等防火墙策略配置服务来完成数十个根据真实工作需求而设计的防火墙策略配置实验。

##### 8.1 防火墙管理工具

防火墙虽然有**软件**或**硬件**之分，但主要功能都是依据策略对穿越防火墙自身的流量进行过滤。

防火墙策略可以**基于流量的源目地址**、**端口号**、**协议**、**应用**等信息来定制，然后防火墙使用预先定制的策略规则监控出入的流量，若流量与某一条策略规则相匹配，则执行相应的处理，反之则丢弃。这样一来，就可以保证仅有合法的流量在企业内网和外部公网之间流动了。

在RHEL 7系统中，firewalld防火墙取代了iptables防火墙。

其实，iptables与firewalld都不是真正的防火墙，它们都只是用来定义防火墙策略的防火墙管理工具而已，或者说，它们只是一种服务。

> iptables服务会把配置好的防火墙策略交由内核层面的netfilter网络过滤器来处理
>
> firewalld服务则是把配置好的防火墙策略交由内核层面的nftables包过滤框架来处理。

![iptables处理数据流经防火墙流程](pics\iptables处理数据流经netfilter流程.png)

大家甚至可以不用完全掌握本章介绍的内容，只要在这多个防火墙管理工具中任选一款并将其学透，就足以满足日常的工作需求了。

##### 8.2 Iptables

###### 8.2.1 策略与规则链

防火墙会从上至下的顺序来读取配置的策略规则，在找到匹配项后就立即结束匹配工作并去执行匹配项中定义的行为（即放行或阻止）。如果在读取完所有的策略规则之后没有匹配项，就去执行默认的策略。

一般而言，防火墙策略规则的设置有两种：一种是“通”（即放行），一种是“堵”（即阻止）。

iptables服务把用于处理或过滤流量的策略条目称之为规则，多条规则可以组成一个规则链，而规则链则依据数据包处理位置的不同进行分类，具体如下：

> 在进行路由选择前处理数据包（PREROUTING）；
>
> 处理流入的数据包（INPUT）；
>
> 处理流出的数据包（OUTPUT）；
>
> 处理转发的数据包（FORWARD）；
>
> 在进行路由选择后处理数据包（POSTROUTING）。

一般来说，从内网向外网发送的流量一般都是可控且良性的，因此我们使用最多的就是INPUT规则链，该规则链可以增大黑客人员从外网入侵内网的难度。

> ACCEPT（允许流量通过）
>
> REJECT（拒绝流量通过）：拒绝并且反馈
>
> LOG（记录日志信息）
>
> DROP（拒绝流量通过）：丢弃不反馈

当把Linux系统中的防火墙策略设置为REJECT拒绝动作后，流量发送方会看到端口不可达的响应：

```
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.
From 192.168.10.10 icmp_seq=1 Destination Port Unreachable
From 192.168.10.10 icmp_seq=2 Destination Port Unreachable
From 192.168.10.10 icmp_seq=3 Destination Port Unreachable
From 192.168.10.10 icmp_seq=4 Destination Port Unreachable
--- 192.168.10.10 ping statistics ---
4 packets transmitted, 0 received, +4 errors, 100% packet loss, time 3002ms
```

而把Linux系统中的防火墙策略修改成DROP拒绝动作后，流量发送方会看到响应超时的提醒。但是流量发送方无法判断流量是被拒绝，还是接收方主机当前不在线：

```
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.

--- 192.168.10.10 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3000ms
```

###### 8.2.2 基本的命令参数

iptables是一款基于命令行的防火墙策略管理工具，具有大量参数，如“四表五链”的理论概念。

iptables命令可以根据流量的源地址、目的地址、传输协议、服务类型等信息进行匹配，一旦匹配成功，iptables就会根据策略规则所预设的动作来处理这些流量。

注意：防火墙策略规则的匹配顺序是从上至下的，因此要把较为严格、优先级较高的策略规则放到前面，以免发生错误。

```
-P	设置默认策略
-F	清空规则链
-L	查看规则链
-A	在规则链的末尾加入新规则
-I num	在规则链的头部加入新规则
-D num	删除某一条规则
-s	匹配来源地址IP/MASK，加叹号“!”表示除这个IP外
-d	匹配目标地址
-i 网卡名称	匹配从这块网卡流入的数据
-o 网卡名称	匹配从这块网卡流出的数据
-p	匹配协议，如TCP、UDP、ICMP
--dport num	匹配目标端口号
--sport num	匹配来源端口号
```

**在iptables命令后添加-L参数查看已有的防火墙规则链：**

```
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
ACCEPT all -- anywhere anywhere ctstate RELATED,ESTABLISHED
………………省略部分输出信息………………
```

**在iptables命令后添加-F参数清空已有的防火墙规则链：**

```
[root@linuxprobe ~]# iptables -F
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
………………省略部分输出信息………………
```

**把INPUT规则链的默认策略设置为拒绝：**

```
[root@linuxprobe ~]# iptables -P INPUT DROP
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy DROP)
target prot opt source destination 
…………省略部分输出信息………………
```

当把INPUT链设置为默认拒绝后，就要往里面写入允许策略了，否则所有流入的数据包都会被默认拒绝掉，注意规则链的**默认策略拒绝动作只能是DROP**，而**不能是REJECT**。

**向INPUT链中添加允许ICMP流量进入的策略规则：**

在日常运维工作中，经常会使用ping命令来检查对方主机是否在线，而向防火墙的INPUT规则链中添加一条允许ICMP流量进入的策略规则就默认允许了这种ping命令检测行为。

```
[root@linuxprobe ~]# iptables -I INPUT -p icmp -j ACCEPT
```

**删除INPUT规则链中刚刚加入的那条策略（允许ICMP流量），并把默认策略设置为允许：**

```
[root@linuxprobe ~]# iptables -D INPUT 1
[root@linuxprobe ~]# iptables -P INPUT ACCEPT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination
```

**将INPUT规则链设置为只允许指定网段的主机访问本机的22端口，拒绝来自其他所有主机的流量**：

```
[root@linuxprobe ~]# iptables -I INPUT -s 192.168.10.0/24 -p tcp --dport 22 -j ACCEPT
[root@linuxprobe ~]# iptables -A INPUT -p tcp --dport 22 -j REJECT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
ACCEPT tcp -- 192.168.10.0/24 anywhere tcp dpt:ssh
REJECT tcp -- anywhere anywhere tcp dpt:ssh reject-with icmp-port-unreachable
………………省略部分输出信息………………
```

再次重申，**防火墙策略规则是按照从上到下的顺序匹配的**，因此**一定要把允许动作放到拒绝动作前面**，否则所有的流量就将被拒绝掉，从而导致任何主机都无法访问我们的服务。

**向INPUT规则链中添加拒绝所有人访问本机12345端口的策略规则**：

```
[root@linuxprobe ~]# iptables -I INPUT -p tcp --dport 12345 -j REJECT
[root@linuxprobe ~]# iptables -I INPUT -p udp --dport 12345 -j REJECT
[root@linuxprobe ~]# iptables -L
Chain INPUT (policy ACCEPT)
target prot opt source destination 
REJECT udp -- anywhere anywhere udp dpt:italk reject-with icmp-port-unreachable
REJECT tcp -- anywhere anywhere tcp dpt:italk reject-with icmp-port-unreachable
ACCEPT tcp -- 192.168.10.0/24 anywhere tcp dpt:ssh
REJECT tcp -- anywhere anywhere tcp dpt:ssh reject-with icmp-port-unreachable
………………省略部分输出信息………………
```

**向INPUT规则链中添加拒绝192.168.10.5主机访问本机80端口（Web服务）的策略规则**：

```
[root@linuxprobe ~]# iptables -I INPUT -p tcp -s 192.168.10.5 --dport 80 -j REJECT
```

**向INPUT规则链中添加拒绝所有主机访问本机1000**～**1024****端口的策略规则**：

```
[root@linuxprobe ~]# iptables -A INPUT -p tcp --dport 1000:1024 -j REJECT
[root@linuxprobe ~]# iptables -A INPUT -p udp --dport 1000:1024 -j REJECT
```

**保存iptables防火墙规则**：使用iptables命令配置的防火墙规则默认会在系统下一次重启时失效，如果想让配置的防火墙策略永久生效，还要执行保存命令：

```
[root@linuxprobe ~]# service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables: [ OK ]
```

##### 8.3 Firewalld

RHEL 7系统中集成了多款防火墙管理工具，其中firewalld（Dynamic Firewall Manager of Linux systems，Linux系统的动态防火墙管理器）服务是默认的防火墙配置管理工具，它拥有基于CLI（命令行界面）和基于GUI（图形用户界面）的两种管理方式。

firewalld支持动态更新技术并加入了**区域（zone）**的概念。简单来说，区域就是firewalld预先准备了几套防火墙策略集合（**策略模板**），用户可以根据生产场景的不同而选择合适的策略集合，从而实现防火墙策略之间的快速切换。

例如，我们有一台笔记本电脑，每天都要在办公室、咖啡厅和家里使用。按常理来讲，这三者的安全性按照由高到低的顺序来排列，应该是家庭、公司办公室、咖啡厅。当前，我们希望为这台笔记本电脑指定如下防火墙策略规则：在家中允许访问所有服务；在办公室内仅允许访问文件共享服务；在咖啡厅仅允许上网浏览。在以往，我们需要频繁地手动设置防火墙策略规则，而现在只需要预设好区域集合，然后只需轻点鼠标就可以自动切换了，从而极大地提升了防火墙策略的应用效率。firewalld中常见的区域名称（默认为public）以及相应的策略规则：

 firewalld中常用的区域名称及策略规则

| 区域     | 默认规则策略                                                 |
| -------- | ------------------------------------------------------------ |
| trusted  | 允许所有的数据包                                             |
| home     | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh、mdns、ipp-client、amba-client与dhcpv6-client服务相关，则允许流量 |
| internal | 等同于home区域                                               |
| work     | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh、ipp-client与dhcpv6-client服务相关，则允许流量 |
| public   | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh、dhcpv6-client服务相关，则允许流量 |
| external | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh服务相关，则允许流量 |
| dmz      | 拒绝流入的流量，除非与流出的流量相关；而如果流量与ssh服务相关，则允许流量 |
| block    | 拒绝流入的流量，除非与流出的流量相关                         |
| drop     | 拒绝流入的流量，除非与流出的流量相关                         |

###### 8.3.1 终端管理工具

firewall-cmd是firewalld防火墙配置管理工具的CLI（命令行界面）版本。它的参数一般都是以“长格式”来提供的，也就是说，现在除了能用Tab键自动补齐命令或文件名等内容之外，还可以用Tab键来补齐下表所示的长格式参数了（这太棒了）。

 firewall-cmd命令中使用的参数以及作用

| 参数                          | 作用                                                 |
| ----------------------------- | ---------------------------------------------------- |
| --get-default-zone            | 查询默认的区域名称                                   |
| --set-default-zone=<区域名称> | 设置默认的区域，使其永久生效                         |
| --get-zones                   | 显示可用的区域                                       |
| --get-services                | 显示预先定义的服务                                   |
| --get-active-zones            | 显示当前正在使用的区域与网卡名称                     |
| --add-source=                 | 将源自此IP或子网的流量导向指定的区域                 |
| --remove-source=              | 不再将源自此IP或子网的流量导向某个指定区域           |
| --add-interface=<网卡名称>    | 将源自该网卡的所有流量都导向某个指定区域             |
| --change-interface=<网卡名称> | 将某个网卡与区域进行关联                             |
| --list-all                    | 显示当前区域的网卡配置参数、资源、端口以及服务等信息 |
| --list-all-zones              | 显示所有区域的网卡配置参数、资源、端口以及服务等信息 |
| --add-service=<服务名>        | 设置默认区域允许该服务的流量                         |
| --add-port=<端口号/协议>      | 设置默认区域允许该端口的流量                         |
| --remove-service=<服务名>     | 设置默认区域不再允许该服务的流量                     |
| --remove-port=<端口号/协议>   | 设置默认区域不再允许该端口的流量                     |
| --reload                      | 让“永久生效”的配置规则立即生效，并覆盖当前的配置规则 |
| --panic-on                    | 开启应急状况模式                                     |
| --panic-off                   | 关闭应急状况模式                                     |

使用firewalld配置的防火墙策略默认为**运行时（Runtime）模式**，又称为当前生效模式，而且随着系统的重启会失效。如果想让配置策略一直存在，就需要使用**永久（Permanent）模式**了，方法就是在用firewall-cmd命令正常设置防火墙策略时添加**--permanent参数**，这样配置的防火墙策略就可以永久生效了。但是，使用它设置的策略只有在系统重启之后才能自动生效。如果想让配置的策略立即生效，需要手动执行**firewall-cmd --reload**命令。

查看firewalld服务当前所使用的区域：

```
[root@linuxprobe ~]# firewall-cmd --get-default-zone
public
```

查询eno16777728网卡在firewalld服务中的区域：

```
[root@linuxprobe ~]# firewall-cmd --get-zone-of-interface=eno16777728
public
```

把firewalld服务中eno16777728网卡的默认区域修改为external，并在系统重启后生效。分别查看当前与永久模式下的区域名称：

```
[root@linuxprobe ~]# firewall-cmd --permanent --zone=external --change-interface=eno16777728
success
[root@linuxprobe ~]# firewall-cmd --get-zone-of-interface=eno16777728
public
[root@linuxprobe ~]# firewall-cmd --permanent --get-zone-of-interface=eno16777728
external
```

把firewalld服务的当前默认区域设置为public：

```
[root@linuxprobe ~]# firewall-cmd --set-default-zone=public
success
[root@linuxprobe ~]# firewall-cmd --get-default-zone 
public
```

启动/关闭firewalld防火墙服务的应急状况模式，阻断一切网络连接（当远程控制服务器时请慎用）：

```
[root@linuxprobe ~]# firewall-cmd --panic-on
success
[root@linuxprobe ~]# firewall-cmd --panic-off
success
```

查询public区域是否允许请求SSH和HTTPS协议的流量：

```
[root@linuxprobe ~]# firewall-cmd --zone=public --query-service=ssh
yes
[root@linuxprobe ~]# firewall-cmd --zone=public --query-service=https
no
```

把firewalld服务中请求HTTPS协议的流量设置为永久允许，并立即生效：

```
[root@linuxprobe ~]# firewall-cmd --zone=public --add-service=https
success
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --add-service=https
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```

把firewalld服务中请求HTTP协议的流量设置为永久拒绝，并立即生效：

```
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --remove-service=http 
success
[root@linuxprobe ~]# firewall-cmd --reload 
success
```

把在firewalld服务中访问8080和8081端口的流量策略设置为允许，但仅限当前生效：

```
[root@linuxprobe ~]# firewall-cmd --zone=public --add-port=8080-8081/tcp
success
[root@linuxprobe ~]# firewall-cmd --zone=public --list-ports 
8080-8081/tcp
```

把原本访问本机888端口的流量转发到22端口，要且求当前和长期均有效：

> 流量转发命令格式为firewall-cmd --permanent --zone=**<区域>** --add-forward-port=port=<源端口号>:proto=**<协议>**:toport=**<目标端口号>**:toaddr=**<目标IP地址>**

```
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.10.10
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```

在客户端使用ssh命令尝试访问192.168.10.10主机的888端口：

```
[root@client A ~]# ssh -p 888 192.168.10.10
The authenticity of host '[192.168.10.10]:888 ([192.168.10.10]:888)' can't be established.
ECDSA key fingerprint is b8:25:88:89:5c:05:b6:dd:ef:76:63:ff:1a:54:02:1a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[192.168.10.10]:888' (ECDSA) to the list of known hosts.
root@192.168.10.10's password:此处输入远程root管理员的密码
Last login: Sun Jul 19 21:43:48 2017 from 192.168.10.10
```

firewalld中的富规则表示更细致、更详细的防火墙策略配置，它可以针对系统服务、端口号、源地址和目标地址等诸多信息进行更有针对性的策略配置。它的优先级在所有的防火墙策略中也是最高的。比如，我们可以在firewalld服务中配置一条富规则，使其拒绝192.168.10.0/24网段的所有用户访问本机的ssh服务（22端口）：

```
[root@linuxprobe ~]# firewall-cmd --permanent --zone=public --add-rich-rule="rule family="ipv4" source address="192.168.10.0/24" service name="ssh" reject"
success
[root@linuxprobe ~]# firewall-cmd --reload
success
```

在客户端使用ssh命令尝试访问192.168.10.10主机的ssh服务（22端口）：

```
[root@client A ~]# ssh 192.168.10.10
Connecting to 192.168.10.10:22...
Could not connect to '192.168.10.10' (port 22): Connection failed.
```

###### 8.3.2 图形管理工具

firewall-config的界面和功能具体如下。

> **1：**选择运行时（Runtime）模式或永久（Permanent）模式的配置。
>
> **2**：可选的策略集合区域列表。
>
> **3**：常用的系统服务列表。
>
> **4**：当前正在使用的区域。
>
> **5**：管理当前被选中区域中的服务。
>
> **6**：管理当前被选中区域中的端口。
>
> **7**：开启或关闭SNAT（源地址转换协议）技术。
>
> **8**：设置端口转发策略。
>
> **9**：控制请求icmp服务的流量。
>
> **10**：管理防火墙的富规则。
>
> **11**：管理网卡设备。
>
> **12**：被选中区域的服务，若勾选了相应服务前面的复选框，则表示允许与之相关的流量。
>
> **13**：firewall-config工具的运行状态。

![firewall-config界面](pics\firewall-config界面.png)

###### 8.3.4 TCP Wrappers 

基于应用层的设置，只能使用服务名称进行设置

防火墙设置四种方法

```
1. iptables
2. firewall-cmd
3. firewall-config
4. TCP Wrappers
```

TCP Wrappers由两个文件组成

```
/etc/hosts.allow	白名单
					允许的服务名称、IP地址段
					先匹配，优先级高于黑名单
/etc/hosts.deny		黑名单
					拒绝的服务名称、IP地址段
					后匹配
```

hosts.deny举例，加入拒绝ssh服务

```
sshd:192.168.10.
或者
sshd:192.168.10.0/255.255.255.0
```

hosts.allow举例

```
sshd:
```



##### 8.4 服务的访问控制列表

TCP Wrappers是RHEL 7系统中默认启用的一款流量监控程序，它能够根据来访主机的地址与本机的目标服务程序作出允许或拒绝的操作。换句话说，Linux系统中其实有两个层面的防火墙，第一种是前面讲到的基于TCP/IP协议的流量过滤工具，而TCP Wrappers服务则是能允许或禁止Linux系统提供服务的防火墙，从而在更高层面保护了Linux系统的安全运行。

TCP Wrappers服务的防火墙策略由两个控制列表文件所控制

> 允许控制列表文件（/etc/hosts.allow），如果匹配到相应的允许策略则放行流量
>
> 如果没有匹配，则去进一步匹配拒绝控制列表文件（/etc/hosts.deny），若找到匹配项则拒绝该流量
>
> 如果这两个文件全都没有匹配到，则默认放行流量

CP Wrappers服务的控制列表文件中常用的参数

| 客户端类型     | 示例                       | 满足示例的客户端列表               |
| -------------- | -------------------------- | ---------------------------------- |
| 单一主机       | 192.168.10.10              | IP地址为192.168.10.10的主机        |
| 指定网段       | 192.168.10.                | IP段为192.168.10.0/24的主机        |
| 指定网段       | 192.168.10.0/255.255.255.0 | IP段为192.168.10.0/24的主机        |
| 指定DNS后缀    | .linuxprobe.com            | 所有DNS后缀为.linuxprobe.com的主机 |
| 指定主机名称   | www.linuxprobe.com         | 主机名称为www.linuxprobe.com的主机 |
| 指定所有客户端 | ALL                        | 所有主机全部包括在内               |

在配置TCP Wrappers服务时需要遵循两个原则：

1. 编写拒绝策略规则时，填写的是服务名称，而非协议名称；
2. 建议先编写拒绝策略规则，再编写允许策略规则，以便直观地看到相应的效果。

下面编写拒绝策略规则文件，禁止访问本机sshd服务的所有流量（无须/etc/hosts.deny文件中修改原有的注释信息）：

```
[root@linuxprobe ~]# vim /etc/hosts.deny
#
# hosts.deny This file contains access rules which are used to
# deny connections to network services that either use
# the tcp_wrappers library or that have been
# started through a tcp_wrappers-enabled xinetd.
#
# The rules in this file can also be set up in
# /etc/hosts.allow with a 'deny' option instead.
#
# See 'man 5 hosts_options' and 'man 5 hosts_access'
# for information on rule syntax.
# See 'man tcpd' for information on tcp_wrappers
sshd:*
[root@linuxprobe ~]# ssh 192.168.10.10
ssh_exchange_identification: read: Connection reset by peer
```

接下来，在允许策略规则文件中添加一条规则，使其放行源自192.168.10.0/24网段，访问本机sshd服务的所有流量。可以看到，服务器立刻就放行了访问sshd服务的流量，效果非常直观：

```
[root@linuxprobe ~]# vim /etc/hosts.allow
#
# hosts.allow This file contains access rules which are used to
# allow or deny connections to network services that
# either use the tcp_wrappers library or that have been
# started through a tcp_wrappers-enabled xinetd.
#
# See 'man 5 hosts_options' and 'man 5 hosts_access'
# for information on rule syntax.
# See 'man tcpd' for information on tcp_wrappers
sshd:192.168.10.

[root@linuxprobe ~]# ssh 192.168.10.10
The authenticity of host '192.168.10.10 (192.168.10.10)' can't be established.
ECDSA key fingerprint is 70:3b:5d:37:96:7b:2e:a5:28:0d:7e:dc:47:6a:fe:5c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.10.10' (ECDSA) to the list of known hosts.
root@192.168.10.10's password: 
Last login: Wed May 4 07:56:29 2017
[root@linuxprobe ~]# 
```

### 第9章 使用ssh服务管理远程主机

本章讲解了如何使用**nmtui命令**配置网络参数，以及通过**nmcli命令**查看网络信息并管理网络会话服务，从而让您能够在不同工作场景中快速地切换网络运行参数；还讲解了如何手工绑定**mode6模式双网卡**，实现网络的负载均衡。

本章还深入介绍了**SSH协议**与**sshd服务**程序的理论知识、**Linux系统的远程管理**方法以及**在系统中配置服务程序**的方法，并采用实验的形式演示了**使用基于密码验证的sshd服务**程序进行远程登录，以及**使用screen服务**程序远程管理Linux系统的不间断会话等技术。

当读者掌握了本章的内容之后，也就完全具备了对Linux系统进行配置管理的知识。

##### 9.1 配置网卡服务

###### 9.1.1 配置网卡参数

配置网络并确保网络的连通性是学习部署Linux服务之前的最后一个重要知识点。

在RHEL 7系统中有至少5种网络的配置方法。这里教给大家的是使用**nmtui命令**来配置网络。

在服务器主机的网络配置信息中填写IP地址192.168.10.10/24。

只需使用Vim编辑器将网卡配置文件中的ONBOOT参数修改成yes，这样在系统重启后网卡就被激活了。

```
[root@linuxprobe ~]# vim /etc/sysconfig/network-scripts/ifcfg-eno16777736
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=eno16777736
UUID=ec77579b-2ced-481f-9c09-f562b321e268
ONBOOT=yes
IPADDR0=192.168.10.10
HWADDR=00:0C:29:C4:A4:09
PREFIX0=24
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
```

要想让服务程序获取到最新的配置文件，需要手动重启相应的服务，之后就可以看到网络畅通了：

```
[root@linuxprobe ~]# systemctl restart network
[root@linuxprobe ~]# ping -c 4 192.168.10.10
PING 192.168.10.10 (192.168.10.10) 56(84) bytes of data.
64 bytes from 192.168.10.10: icmp_seq=1 ttl=64 time=0.056 ms
```

###### 9.1.2 创建网络会话

RHEL和CentOS系统默认使用**NetworkManager**来提供网络服务，这是一种动态管理网络配置的守护进程，能够让网络设备保持连接状态。可以使用**nmcli命令**来管理Network Manager服务。nmcli是一款基于命令行的网络配置工具，功能丰富，参数众多。它可以轻松地查看网络信息或网络状态：

```
[root@linuxprobe ~]# nmcli connection show
NAME UUID TYPE DEVICE 
eno16777736 ec77579b-2ced-481f-9c09-f562b321e268 802-3-ethernet eno16777736 
[root@linuxprobe ~]# nmcli con show eno16777736
connection.id: eno16777736
connection.uuid: ec77579b-2ced-481f-9c09-f562b321e268
connection.interface-name: --
```

另外，RHEL7系统支持网络会话功能，允许用户在多个配置文件中快速切换（非常类似于firewalld防火墙服务中的区域技术）。如果我们在公司网络中使用笔记本电脑时需要手动指定网络的IP地址，而回到家中则是使用DHCP自动分配IP地址。这就需要麻烦地频繁修改IP地址，但是使用了网络会话功能后一切就简单多了—只需在不同的使用环境中激活相应的网络会话，就可以实现网络配置信息的自动切换了。

可以使用nmcli命令并按照“**connection add con-name xxxx type xxxx ifname xxxx**”的格式来创建网络会话。假设将公司网络中的网络会话称之为company，将家庭网络中的网络会话称之为house，现在依次创建各自的网络会话。

使用con-name参数指定公司所使用的网络会话名称company，然后依次用ifname参数指定本机的网卡名称（千万要以实际环境为准，不要照抄书上的eno16777736），用autoconnect no参数设置该网络会话默认不被自动激活，以及用ip4及gw4参数手动指定网络的IP地址：

```
[root@linuxprobe ~]# nmcli connection add con-name company ifname eno16777736 autoconnect no type ethernet ip4 192.168.10.10/24 gw4 192.168.10.1
Connection 'company' (86c71220-0057-419e-b615-38f4014cfdee) successfully added.

[root@linuxprobe ~]# nmcli connection add con-name house type ethernet ifname eno16777736
Connection 'house' (44acf0a7-07e2-40b4-94ba-69ea973090fb) successfully added.
```

在成功创建网络会话后，可以使用nmcli命令查看创建的所有网络会话：

```
[root@linuxprobe ~]# nmcli connection show
NAME UUID TYPE DEVICE 
house        44acf0a7-07e2-40b4-94ba-69ea973090fb 802-3-ethernet -- 
company      86c71220-0057-419e-b615-38f4014cfdee 802-3-ethernet -- 
eno16777736  ec77579b-2ced-481f-9c09-f562b321e268 802-3-ethernet eno16777736 
```

能自动通过DHCP获取到IP地址了。

```
[root@linuxprobe ~]# nmcli connection up house 
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
[root@linuxprobe ~]# ifconfig
eno1677773628: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
 inet 192.168.100.128 netmask 255.255.255.0 broadcast 192.168.100.255
```

如果大家使用的是虚拟机，请把虚拟机系统的网卡（网络适配器）切换成桥接模式 ???

###### 9.1.3 绑定两块网卡

一般来讲，生产环境必须提供7×24小时的网络传输服务。借助于网卡绑定技术，不仅可以提高网络传输速度，更重要的是，还可以确保在其中一块网卡出现故障时，依然可以正常提供网络服务。假设我们对两块网卡实施了绑定技术，这样在正常工作中它们会共同传输数据，使得网络传输的速度变得更快；而且即使有一块网卡突然出现了故障，另外一块网卡便会立即自动顶替上去，保证数据传输不会中断。

**第1步**：在虚拟机系统中再添加一块网卡设备，请确保两块网卡都处在同一个网络连接中（即网卡模式相同），如图9-10和图9-11所示。处于相同模式的网卡设备才可以进行网卡绑定，否则这两块网卡无法互相传送数据。

**第2步**：使用Vim文本编辑器来配置网卡设备的绑定参数。网卡绑定的理论知识类似于前面学习的RAID硬盘组，我们需要对参与绑定的网卡设备逐个进行“初始设置”。需要注意的是，这些原本独立的网卡设备此时需要被配置成为一块“从属”网卡，服务于“主”网卡，不应该再有自己的IP地址等信息。在进行了初始设置之后，它们就可以支持网卡绑定。

```
[root@linuxprobe ~]# vim /etc/sysconfig/network-scripts/ifcfg-eno16777736
TYPE=Ethernet
BOOTPROTO=none
ONBOOT=yes
USERCTL=no
DEVICE=eno16777736
MASTER=bond0
SLAVE=yes
[root@linuxprobe ~]# vim /etc/sysconfig/network-scripts/ifcfg-eno33554968
TYPE=Ethernet
BOOTPROTO=none
ONBOOT=yes
USERCTL=no
DEVICE=eno33554968
MASTER=bond0
SLAVE=yes
```

**第3步**：让Linux内核支持网卡绑定驱动。常见的网卡绑定驱动有三种模式—mode0、mode1和mode6。下面以绑定两块网卡为例，讲解使用的情景。

> mode0（平衡负载模式）：平时两块网卡均工作，且自动备援，但需要在与服务器本地网卡相连的交换机设备上进行端口聚合来支持绑定技术。
>
> mode1（自动备援模式）：平时只有一块网卡工作，在它故障后自动替换为另外的网卡。
>
> mode6（平衡负载模式）：平时两块网卡均工作，且自动备援，无须交换机设备提供辅助支持。

比如有一台用于提供NFS或者samba服务的文件服务器，它所能提供的最大网络传输速度为100Mbit/s，但是访问该服务器的用户数量特别多，那么它的访问压力一定很大。在生产环境中，网络的可靠性是极为重要的，而且网络的传输速度也必须得以保证。针对这样的情况，比较好的选择就是mode6网卡绑定驱动模式了。因为mode6能够让两块网卡同时一起工作，当其中一块网卡出现故障后能自动备援，且无需交换机设备支援，从而提供了可靠的网络传输保障。

下面使用Vim文本编辑器创建一个用于网卡绑定的驱动文件，使得绑定后的bond0网卡设备能够支持绑定技术（bonding）；同时定义网卡以mode6模式进行绑定，且出现故障时自动切换的时间为100毫秒。

```
[root@linuxprobe ~]# vim /etc/modprobe.d/bond.conf
alias bond0 bonding
options bond0 miimon=100 mode=6
```

**第4步**：重启网络服务后网卡绑定操作即可成功。正常情况下只有bond0网卡设备才会有IP地址等信息：

```
[root@linuxprobe ~]# systemctl restart network
[root@linuxprobe ~]# ifconfig
bond0: flags=5187<UP,BROADCAST,RUNNING,MASTER,MULTICAST> mtu 1500
inet 192.168.10.10 netmask 255.255.255.0 broadcast 192.168.10.255
inet6 fe80::20c:29ff:fe9c:637d prefixlen 64 scopeid 0x20<link>
ether 00:0c:29:9c:63:7d txqueuelen 0 (Ethernet)
RX packets 700 bytes 82899 (80.9 KiB)
RX errors 0 dropped 6 overruns 0 frame 0
TX packets 588 bytes 40260 (39.3 KiB)
TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

eno16777736: flags=6211<UP,BROADCAST,RUNNING,SLAVE,MULTICAST> mtu 1500
ether 00:0c:29:9c:63:73 txqueuelen 1000 (Ethernet)
RX packets 347 bytes 40112 (39.1 KiB)
RX errors 0 dropped 6 overruns 0 frame 0
TX packets 263 bytes 20682 (20.1 KiB)
TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

eno33554968: flags=6211<UP,BROADCAST,RUNNING,SLAVE,MULTICAST> mtu 1500
ether 00:0c:29:9c:63:7d txqueuelen 1000 (Ethernet)
RX packets 353 bytes 42787 (41.7 KiB)
RX errors 0 dropped 0 overruns 0 frame 0
TX packets 325 bytes 19578 (19.1 KiB)
TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0
```



> Linux系统中的一切都是文件
>
> 部署服务就是在修改服务的配置文件
>
> 要运行最新的参数，需要重启对应的服务，顺手将配置的服务加入到启动项中，保证重启后依然有效

