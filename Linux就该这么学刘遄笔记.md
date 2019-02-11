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

mount -o remount,rw /sysroot     	#重新挂在根目录，并给它可读可写权限

chroot /sysroot					#切换根目录到/sysroot

echo "linuxprobe" | passwd --stdin root	#将root密码修改为：linuxprobe

touch /.autorelabel				# 如果开启了selinux，必须创建更新该目录。

​								# 修改此文件的三个时间戳为当前时间

exit								#从根分区中，退到小系统中

reboot

##### 1.5.5 下载vnc客户端，远程登录

##### 1.5.6 配置一下本机的yum仓库

```
[root@localhost Desktop]#vim /etc/yum.repos.d/rhel.repo

​```
[rhel]

name=rhel

baseurl=file:///media/cdrom

enabled=1

gpgcheck=0
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

Shell是命令行工具，也称为终端或壳。红帽系统默认的命令行终端是Bash（Bourne-Again SHell）解释器。

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

   

2. date命令

3. reboot命令

4. poweroff命令

5. wget命令

6. ps命令

7. top命令

8. pidof命令

9. kill命令

10. killall命令

##### 2.4 系统状态检测命令

1. ifconfig命令
2. uname命令
3. uptime命令
4. free命令
5. who命令
6. last命令
7. history命令
8. sosreport命令

##### 2.5 工作目录切换命令

1. pwd命令
2. cd命令
3. ls命令

##### 2.6 文本文件编辑命令

1. cat命令
2. more命令
3. head命令
4. tail命令
5. tr命令
6. wc命令
7. stat命令
8. cut命令
9. diff命令

##### 2.7 文件目录管理命令

1. touch命令
2. mkdir命令
3. cp命令
4. mv命令
5. rm命令
6. dd命令
7. file命令

##### 2.8 打包压缩与搜索命令

1. tar命令
2. grep命令
3. find命令

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
>   - /lib           /bin/和/sbin/中必须的依赖库
>   - /media    一些可以热插拔的介质（如CD-ROMs）的挂载点
>   - /mnt       临时挂载的文件系统
>   - /opt        可选的应用程序包
>   - /proc      将进程和内核信息已文件心事呈现的虚拟文件系统。在Linux中，与procfs mount（进程文件系统）对应
>   - /root      root用户的个人目录
>   - /run       

