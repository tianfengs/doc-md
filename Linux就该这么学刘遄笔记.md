Linux运维工程师从0基础到实践 20课（小时）

### 第0章 学习方法和红帽系统

##### 许可证

![Linux就该这么学01](D:\maidan\doc笔记md\pics\Linux就该这么学01.png)

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

Shell是命令行工具，也称为终端或壳，**充当人与内核(硬件)的翻译官**。红帽系统默认的命令行终端是Bash（Bourne-Again SHell）解释器。

Bash优势：

1. 通过上下键调用过往命令
2. 命令或参数可以用tab补全
3. 强大的批处理脚本
4. 具有实用的环境变量功能。

```
命令名称 【命令参数】 【命令对象】
```

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