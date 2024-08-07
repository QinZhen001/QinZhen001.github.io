---
layout:     post
title:      "linux相关"
date:       2024-04-18 10:30:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Linux
---

> “Yeah It's on. ”





[linux下文件的复制、移动与删除](https://www.cnblogs.com/qiuhong10/p/7940410.html)

[Linux中ps命令详解](https://blog.csdn.net/tanga842428/article/details/52742360)

[Bash 常用命令](https://github.com/judasn/Linux-Tutorial/blob/master/markdown-file/Bash.md)

[网页链接](https://github.com/CyC2018/CS-Notes/blob/97a21c792753c2b7a6a74e8c7ef5875f7d95bc26/notes/Linux.md)

[Linux 命令搜索](https://wangchujiang.com/linux-command/list.html)


* Linux 严格 区分大小写

* **Linux 中所有内容以文件形式保存，包括硬件**

* Linux 中一切内容皆为文件

* Linux不靠扩展名区分文件类型，它靠的是权限来区分



## Union FS

Union FS（Unified File System）是一种文件系统层叠技术，可以将不同文件系统挂载到同一个目录下，形成一个统一的虚拟文件系统。它通过将多个目录合并成一个逻辑上的目录，实现对这些目录进行统一管理和访问。

在Linux中，Union FS可以通过在mount命令中使用unionfs参数来挂载多个文件系统。挂载多个文件系统的过程中，Union FS会将这些文件系统的文件和目录按照指定的优先级进行合并，用户可以像访问一个普通文件系统一样访问这些合并后的文件和目录。

Union FS提供了以下几种层叠策略：

1. Union：将多个文件系统的文件和目录合并，如果有冲突则使用最近挂载的文件系统中的文件。
2. **Overlay：将一个文件系统作为上层文件系统，其他文件系统作为下层文件系统，上层文件系统的文件会覆盖下层文件系统中相同路径的文件。(docker 中使用)**
3. Aufs：类似于Overlay，但可以将文件系统以只读或读写的方式挂载。

Union FS在Linux系统中被广泛应用于容器技术中，可以实现将多个文件系统合并到一个容器镜像中，为容器提供独立的文件系统环境。它还可以用于管理系统中的分层文件系统，提供更灵活的文件管理和访问方式。





## namespace

在Linux中，namespace是一种将系统资源隔离开的机制。它可以将一组系统资源（例如进程、网络、文件系统、IPC等）视为独立的单元，在不同的namespace中运行，互相之间不会产生干扰。

在Linux中，有以下几种类型的namespace：

1. PID Namespace：用于隔离进程ID，每个进程在不同的PID namespace中都有唯一的ID，使得进程无法感知到其他namespace中的进程存在。
2. Network Namespace：用于隔离网络资源，每个network namespace都有自己的网络设备、IP地址、路由表等，使得不同的network namespace之间拥有独立的网络栈。（Docker 默认采用 veth 的方式将 container 中的虚拟网卡同 host 上的一个 docker bridge: docker0 连接在一起。）
3. Mount Namespace：用于隔离文件系统资源，每个mount namespace可以有不同的文件系统挂载点和文件系统层次结构。
4. UTS Namespace：用于隔离主机名和域名，每个UTS namespace可以拥有不同的主机名和域名。
5. IPC Namespace：用于隔离进程间通信资源，每个IPC namespace都有独立的共享内存、信号量和消息队列。
6. User Namespace：用于隔离用户和用户组，每个user namespace都有自己独立的用户和用户组ID，使得不同的user namespace中的用户和用户组互相不可见。

通过使用这些namespace，可以将不同的进程隔离开，使得它们在不同的环境中运行，提高系统的安全性和稳定性。

---

Linux 内核代码中 Namespace 的实现

```c
// 进程数据结构
truct task_struct {
...
/* namespaces */
struct nsproxy *nsproxy;
...
}



struct nsproxy {
  atomic_t count;
  struct uts_namespace *uts_ns;
  struct ipc_namespace *ipc_ns;
  struct mnt_namespace *mnt_ns;
  struct pid_namespace
  *pid_ns_for_children;
  struct net *net_ns;
}
```





## cgroup

cgroup（Control Group）是一个Linux内核特性，用于限制、控制和隔离进程组的资源使用。它可以对进程组分配和管理资源，如CPU、内存、磁盘I/O等，以实现资源的可控制、可监视、可限制和可隔离。

通过使用cgroup，可以将一组相关的进程组织在同一个cgroup中，并为其分配一定的资源。这样可以实现资源的分配和管理，确保不同进程组不会互相影响，提高系统的稳定性和性能。

cgroup提供了一套API和命令行工具，使用户可以创建、管理和监视cgroup。用户可以通过设置cgroup参数，限制进程组的资源使用，例如设置CPU使用率、内存限制和IO带宽限制等。

cgroup的应用场景非常广泛，特别适用于容器技术（如Docker）中的资源隔离和管理。它可以帮助在容器环境中实现对各个容器的资源控制，保证各个容器之间的运行不会相互干扰。同时，也可以用于虚拟化环境中，以实现对虚拟机的资源管理和隔离。

总之，cgroup是Linux内核提供的一个强大的资源控制和分配机制，可以实现进程组的资源管理、限制和隔离，为系统的稳定性和性能提供支持。



## Iptables

Iptables是一个用于Linux系统的防火墙工具，用于配置网络过滤规则和NAT（网络地址转换）功能。它允许管理员根据IP地址、端口和协议设置规则，以控制进出网络的数据流。Iptables可以提供网络安全和访问控制，用于防止未经授权的访问、拦截恶意流量等。



## nstenter 命令

"nstenter"命令用于在指定的命名空间中执行命令。它允许用户在不切换命名空间的情况下执行命令，以便可以在特定的命名空间中查看和操作对应的资源。命名空间是Linux内核中一种用于将系统资源分组和隔离的机制。使用"nstenter"命令，可以进入指定的命名空间并执行命令，以便在不改变自己所处的命名空间的情况下检查和管理其他命名空间中的资源。

## systemd

systemd 是一个 Linux 系统初始化和服务管理的软件套件，它被广泛应用于诸多现代 Linux 发行版中，如 Red Hat、Fedora、Debian 等。systemd 可以有效地改善系统启动速度，提供了简洁易用的服务管理功能，同时具备强大的日志记录与分析能力。systemd 的设计目标是替代传统的 System V 初始化系统（SysVinit），并提供更高效、更可靠、更可扩展的系统初始化和管理方案。



## 网桥设备

网桥设备是用于连接多个局域网（LAN）的设备。它的主要功能是传输和转发数据包，从而使不同的局域网之间能够相互通信和交换数据。

具体来说，网桥设备工作在OSI（开放式系统互联）模型的第二层，即数据链路层。它通过读取数据包的目标MAC地址，决定将数据包转发到目标局域网上的特定端口。当一个数据包从一个局域网传输到另一个局域网时，网桥设备会自动学习并记录已连接设备的MAC地址，并在未来的数据传输中使用这些信息进行转发。



# 基础




### linux进程状态

1.  运行(正在运行或在运行队列中等待) 
2.  中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号) 
3.  不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生) 
4.  僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放) 
5.  停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行)



### linux常用目录

| 目录  | **内容**                                                     |
| ----- | ------------------------------------------------------------ |
| /bin  | 系统有很多放置执行文件的目录，但是/bin目录比较特殊。**/bin放置的是在单用户维护模式下还能够被操作的命令**。在/bin下面的命令可以被root与一般用户使用。 |
| /etc  | 系统主要的配置文件几乎都放置在这个目录里，例如人员的账号密码文件、各种服务的起始文件等。一般来说，这个目录下的各个文件属性是可以让一般用户查阅的。但只有root用户有权限修改。**FHS标准建议不要放置可执行文件（binary）在这个目录中。** |
| /home | **这是系统默认的用户文件夹（home directory）**。在创建一个一般用户时，默认的用户主文件夹都会放置到这里。主文件夹有两种代号：~：代表目前这个用户的主文件夹 ~ahao:代表用户ahao的主文件夹 |
| /root | **系统管理员（root）的主文件夹**。之所以放在这里，是因为如果进入单用户维护模式而仅挂载根目录时，该目录就能拥有root的主文件夹，所以将root的主文件夹与根目录放置在同一块分区内。 |
| /sys  | 这个目录跟/proc非常相似，也是一个虚拟文件系统，主要记录与内核相关的信息。包括目前已加载的内核模块与内核检测到的硬件设备信息等。该目录同样不占用硬盘容量。 |
| /tmp  | 这是一般用户或者时正在执行的程序暂时放置文件的地方。这个目录任何人都能访问并且创建数据，但是只用root和数据的使用者才能删除所创建的数据。 |
| /usr  | （Unix software Resource ） Unix软件资源                     |

> /usr不是user的缩写，其实usr是Unix Software Resource的缩写， 也就是Unix操作系统软件资源所放置的目录，而不是用户的数据；所有系统默认的软件都会放置到/usr, 系统安装完时，这个目录会占用最多的硬盘容量



1. /root ：/root等同于root用户bai。

2. du/home：/home等同于/home/当前的普通用户名。



###  linux硬链接和软连接

[https://xzchsia.github.io/2020/03/05/linux-hard-soft-link/](https://xzchsia.github.io/2020/03/05/linux-hard-soft-link/)

原理上，硬链接和源文件的inode节点号相同，两者互为硬链接。软连接和源文件的inode节点号不同，进而指向的block也不同，软连接block中存放了源文件的路径名。 

**实际上，硬链接和源文件是同一份文件，而软连接是独立的文件，类似于快捷方式，存储着源文件的位置信息便于指向。** 

使用限制上，不能对目录创建硬链接，不能对不同文件系统创建硬链接，不能对不存在的文件创建硬链接；可以对目录创建软连接，可以跨文件系统创建软连接，可以对不存在的文件创建软连接。



### linux文件权限

例子：

-rw-r--r--.  1  root  root 

> 第一个root是所有者 第二个root是所属组
>
> . 代表ACL权限
>
> 1 代表引用计数

* 第一位代表文件类型（-文件    d目录   l软连接文件）

* 后面每三位为一组  (第一组是所有者 第二组是所属组 第三组是所有人)
  * rw-  u所有者
  * r--  g所属组
  * r--  o其他人

* r读  w写  x执行




### ps

* -e   显示所有进程
* -f    全格式
* -h   不显示标题
* -l    长格式
* -w  宽输出
* -a    显示终端上的所有进程，包括其他用户的进程
* -r    只显示正在运行的进程
* -u 　以用户为主的格式来显示程序状况
* -x     显示所有程序，不以终端机来区分

常用：  ps -ef   和 ps-aux     


### netstat

netstat命令用来打印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况。


* -p或--programs：显示正在使用Socket的程序识别码和程序名称；
* -l或--listening：显示监控中的服务器的Socket；
* -n或--numeric：直接使用ip地址，而不通过域名服务器；

----


* netstat -a     #列出所有端口
* netstat -at    #列出所有tcp端口
* netstat -au    #列出所有udp端口  

----------

常用： netstat -ntpl



###  ls



#### ls颜色区分

ls命令显示的文件可能会出现颜色

* 白色：表示普通文bai件
* 蓝色：表示du目录
* 绿色：表示可执行文件
* 红色：表示压缩文件
* 浅蓝色：链接文件
* 红色闪烁：表示链接的文件有问题
* 黄色：表示设备文件
* 灰色：表示其他文件








### rm -rf

删除文件夹的命令 使用rm -rf 目录名字 命令即可

* -r 就是向下递归，不管有多少级目录，一并删除
* -f 就是直接强行删除，不作任何提示的意思



### tar

**.tar.gz是一个压缩包**，**.tar只是打包而没有压缩**

#### tar后的zxvf

* z：通过gzip支持压缩或解压缩。还有其他的压缩或解压缩方式，比如j表示bzip2的方式。

* x：解压缩。c：是压缩。 

* v：在压缩或解压缩过程中显示正在处理的文件名

* f：**f后面必须跟上要处理的文件名**。也就是说你不能写成这样 tar -zxfv zhcon-0.2.5.tar.gz 



-----



打包并压缩文件：“tar -czvf 压缩包名.tar.gz 文件名”

解压并展开压缩包：“tar -xzvf 压缩包名.tar.gz”







### cat 

查看文件内容

* at     由第一行开始显示内容，并将所有内容输出
* tac     从最后一行倒序显示内容，并将所有内容输出
* more    根据窗口大小，一页一页的现实文件内容
* less    和more类似，但其优点可以往前翻页，而且进行可以搜索字符
* head    只显示头几行
* tail    只显示最后几行
* nl      类似于cat -n，显示时输出行号
* tailf   类似于tail -f 


cat的功能是将文件从第一行开始连续的将内容输出在屏幕上。但是cat并不常用，原因是当文件大，行数比较多时，屏幕无法全部容下时，只能看到一部分内容。

cat语法：cat [-n]  文件名 （-n ： 显示时，连行号一起输出）

----------



more的功能是将文件从第一行开始，根据输出窗口的大小，适当的输出文件内容。当一页无法全部输出时，可以用“回车键”向下翻行，用“空格键”向下翻页。退出查看页面，请按“q”键。另外，more还可以配合管道符“|”（pipe）使用，例如:ls -al | more



more的语法：more 文件名

* Enter 向下n行，需要定义，默认为1行； 
* Ctrl f 向下滚动一屏； 
* 空格键 向下滚动一屏； 
* Ctrl b 返回上一屏； 
* = 输出当前行的行号； 
* :f 输出文件名和当前行的行号； 
* v 调用vi编辑器； 
* ! 命令 调用Shell，并执行命令； 
* q 退出more





### df

df命令用于显示磁盘分区上的可使用的磁盘空间。默认显示单位为KB。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。


* -a或--all：包含全部的文件系统；
* --block-size=<区块大小>：以指定的区块大小来显示区块数目；
* **-h或--human-readable：以可读性较高的方式来显示信息；**
* -H或--si：与-h参数相同，但在计算时是以1000 Bytes为换算单位而非1024 Bytes；
* -i或--inodes：显示inode的信息；
* -k或--kilobytes：指定区块大小为1024字节；
* -l或--local：仅显示本地端的文件系统；
* -m或--megabytes：指定区块大小为1048576字节；
* --no-sync：在取得磁盘使用信息前，不要执行sync指令，此为预设值；
* -P或--portability：使用POSIX的输出格式；
* --sync：在取得磁盘使用信息前，先执行sync指令；
* -t<文件系统类型>或--type=<文件系统类型>：仅显示指定文件系统类型的磁盘信息；
* -T或--print-type：显示文件系统的类型；
* -x<文件系统类型>或--exclude-type=<文件系统类型>：不要显示指定文件系统类型的磁盘信息；
* --help：显示帮助；
* --version：显示版本信息。

**使用-h选项以KB以上的单位来显示，可读性高**





> df 记录的是通过文件系统获取到的文件的大小，他比du强的地方就是能够看到已经删除的文件，而且计算大小的时候，把这一部分的空间也加上更精确，du 命令是用户级的程序，它不考虑Meta Data，而df命令则查看文件系统的磁盘分配图并考虑Meta Data。



### du

显示每个文件和目录的磁盘使用空间  => 文件的大小

* du -sh * 查看当前目录bai下的文件夹大小

> -s选项，可以统计该目录下所有子目录和子文件总大小



### apk

[https://wangchujiang.com/linux-command/c/apk.html](https://wangchujiang.com/linux-command/c/apk.html)

Alpine Linux 下的包管理工具

Alpine Linux是一个面向安全和资源效率的轻量级Linux发行版。它具有小巧、简单和高效的设计理念，旨在用于嵌入式系统、虚拟化环境和容器化应用。Alpine Linux的目标是提供一个小巧但功能齐全的Linux环境，同时保持最小的软件包集合和随之而来的系统开销。它采用了musl libc和busybox工具集，以减少资源消耗和系统复杂性，并提供了一种可靠和安全的应用程序运行环境。



### free 

 查看可用的内存

free -h



### openssl

[https://wangchujiang.com/linux-command/c/openssl.html](https://wangchujiang.com/linux-command/c/openssl.html)

**OpenSSL** 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用

```bash
// 例子：
openssl rand -base64 10
```





### curl

curl命令是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称curl为下载工具。作为一款强力工具，curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征。做网页处理流程和数据检索自动化，curl可以祝一臂之力。



curl(选项)(参数)

####  选项



* -a/--append	上传文件时，附加到目标文件
* -A/--user-agent `<string>`	设置用户代理发送给服务器
* -anyauth	可以使用“任何”身份验证方法
* -b/--cookie `<name=string/file>`	cookie字符串或文件读取位置
  * --basic	使用HTTP基本验证
* -B/--use-ascii	使用ASCII /文本传输
* -c/--cookie-jar `<file>`	操作结束后把cookie写入到这个文件中
* -C/--continue-at `<offset>`	断点续转
* -d/--data` <data>`	HTTP POST方式传送数据
  * --data-ascii `<data>`	以ascii的方式post数据
  * --data-binary `<data>`	以二进制的方式post数据
  * --negotiate	使用HTTP身份验证
  * --digest	使用数字身份验证
  * --disable-eprt	禁止使用EPRT或LPRT
  * --disable-epsv	禁止使用EPSV
* -D/--dump-header `<file>`	把header信息写入到该文件中
  * --egd-file `<file>`	为随机数据(SSL)设置EGD socket路径
  * --tcp-nodelay	使用TCP_NODELAY选项
* -e/--referer	来源网址
* -E/--cert `<cert[:passwd]>`	客户端证书文件和密码 (SSL)
  * --cert-type `<type>`	证书文件类型 (DER/PEM/ENG) (SSL)
  * --key `<key>`	私钥文件名 (SSL)
  * --key-type `<type>`	私钥文件类型 (DER/PEM/ENG) (SSL)
  * --pass `<pass>`	私钥密码 (SSL)
  * --engine `<eng>`	加密引擎使用 (SSL). "--engine list" for list
  * --cacert `<file>`	CA证书 (SSL)
  * --capath `<directory>`	CA目录 (made using c_rehash) to verify peer against (SSL)
  * --ciphers `<list>`	SSL密码
  * --compressed	要求返回是压缩的形势 (using deflate or gzip)
  * --connect-timeout `<seconds>`	设置最大请求时间
  * --create-dirs	建立本地目录的目录层次结构
  * --crlf	上传是把LF转变成CRLF
* -f/--fail	连接失败时不显示http错误
  * --ftp-create-dirs	如果远程目录不存在，创建远程目录
  * --ftp-method [multicwd/nocwd/singlecwd]	控制CWD的使用
  * --ftp-pasv	使用 PASV/EPSV 代替端口
  * --ftp-skip-pasv-ip	使用PASV的时候,忽略该IP地址
  * --ftp-ssl	尝试用 SSL/TLS 来进行ftp数据传输
  * --ftp-ssl-reqd	要求用 SSL/TLS 来进行ftp数据传输
* -F/--form `<name=content>`	模拟http表单提交数据
  * --form-string `<name=string>`	模拟http表单提交数据





### wget

[https://www.cnblogs.com/ftl1012/p/9265699.html](https://www.cnblogs.com/ftl1012/p/9265699.html)

> "wget" 这个名称来源于 “World Wide Web” 与 “get” 的结合。

 wget是一个下载文件的工具，它用在命令行下。对于Linux用户是必不可少的工具，我们经常要下载一些软件或从远程服务器恢复备份到本地服务器。

 wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理。所谓的**自动下载**是指，wget可以在用户退出系统的之后在后台执行。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成

  wget 可以跟踪HTML页面上的链接依次下载来创建远程服务器的本地版本，完全重建原始站点的目录结构。这又常被称作”递归下载”。

  wget 非常稳定，它在带宽很窄的情况下和不稳定网络中有很强的适应性.如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。



#### wget下载太慢问题 

[https://blog.csdn.net/luhengchang/article/details/81365484](https://blog.csdn.net/luhengchang/article/details/81365484?utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-BlogCommendFromBaidu-4.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-BlogCommendFromBaidu-4.nonecase)

使用mwget



### grep

[https://www.runoob.com/linux/linux-comm-grep.html](https://www.runoob.com/linux/linux-comm-grep.html)

Linux grep 命令用于查找文件里符合条件的字符串。

grep 指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，预设 grep 指令会把含有范本样式的那一列显示出来。若不指定任何文件名称，或是所给予的文件名为 **-**，则 grep 指令会从标准输入设备读取数据。

在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行

```bash
grep test *file 
```





### file

该命令用来识别文件类型，也可用来辨别一些文件的编码格式。它是通过查看文件的头部信息来获取文件类型，而不是像Windows通过扩展名来确定文件类型的。

```\
$ file main.c 
main.c: C source, ASCII text
```

```bash
$ file README.md 
README.md: UTF-8 Unicode text
```

```bash
file librte_runtime_nodejs.node
librte_runtime_nodejs.node: Mach-O 64-bit dynamically linked shared library arm64
```

file 可以查看.node文件的arch架构





### zip 和 unzip

[https://www.runoob.com/linux/linux-comm-unzip.html](https://www.runoob.com/linux/linux-comm-unzip.html)

```bash
zip -r myfile.zip ./*
将当前目录下的所有文件和文件夹全部压缩成myfile.zip文件,－r表示递归压缩子目录下所有文件.
```



#### zip文件不需要外层包裹

```bash
zip -r app.zip * .[^.]*
```

> app.zip 为文件名

这样app.zip解压后就会直接平铺在当前文件夹

**但是mac的双击解压会有默认操作，还是会在外层包裹一层，但实际上这个命令已经生效了。**





### echo命令

echo命令的功能是在显示器上显示一段文字，一般起到一个提示的作用。


该命令的一般格式为： echo [ -n ] 字符串

其中选项n表示输出文字后不换行；字符串能加引号，也能不加引号。用echo命令输出加引号的字符串时，将字符串原样输出；用echo命令输出不加引号的字符串时，将字符串中的各个单词作为字符串输出，各字符串之间用一个空格分割。



#### [echo $0](https://superuser.com/questions/1445395/how-does-echo-0-work)

$0 is the name of the running process. If you use it inside of a shell then it will return the name of the shell. If you use it inside of a script, it will be the name of the script.




### $HOME

$HOME这个是一个环境变量，它代表的是当前登录的用户的主文件夹的意思。（就是家目录的那个）


### mv命令

mv命令来为文件或目录改名或将文件由一个目录移入另一个目录中。该命令等同于DOS系统下的ren和move命令的组合。它的使用权限是所有用户。


**mv [options] 源文件或目录 目标文件或目录**



**利用mv命令重命名**

将文件test.txt重命名为wbk.txt：

mv test.txt wbk.txt


#### [options]主要参数

* －i：交互方式操作。如果mv操作将导致对已存在的目标文件的覆盖，此时系统询问是否重写，要求用户回答”y”或”n”，这样可以避免误覆盖文件。
* －f：禁止交互操作。mv操作要覆盖某个已有的目标文件时不给任何指示，指定此参数后i参数将不再起作用。




### ~和/的区别

* /是指根目录：就是所有目录最顶层的目录
* ~是当前用户的主目录：如果是root用户就是/root/目录， 如果是其他用户就是/home/下用户名命名的用户 

```bash
# 举个例子
~ 等价于 /Users/qz
```





### systemctl

[https://blog.csdn.net/skh2015java/article/details/94012643](https://blog.csdn.net/skh2015java/article/details/94012643)

Linux 服务管理两种方式service和systemctl

systemd是Linux系统最新的初始化系统(init),作用是提高系统的启动速度，尽可能启动较少的进程，尽可能更多进程并发启动。

systemd对应的进程管理命令是systemctl



#### 查看端口是否可用

```bash
lsof -i:80
```

> 其中，80为端口号





### export

export will make the variable appear in the environment of subsequently executed commands

```bash
export AZURE_TTS_KEY=123
```

获取某一个环境变量

```bash
echo $AZURE_TTS_KEY
```





### printenv

查看当前环境变量





# 补充



### 查看当前所在目录的全路径

使用pwd命令  **（全称是Print Working Directory）**





### *当前脚本所在目录*

举个例子：

```bash
"$(dirname "$0")/husky.sh"
```

- $0 表示当前脚本的路径和名称。
- dirname 表示获取一个路径的目录部分。
- $(...) 是命令替换的语法，可以将命令的执行结果作为字符串返回。

----

最后拼接上 "/husky.sh"，即表示在当前脚本所在目录下找到名为 "husky.sh" 的脚本文件。





### 前台进程和后台进程



 **LINUX后台进程也叫守护进程（Daemon）, 是运行在后台的一种特殊进程。它独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件 ** 



两种进程的主要区别：

1. 前台进程用户可以操作,后台进程用户不能操作(除了把它关闭). 

2. 前台进程不全是由计算机自动控制,后台进程全都是由计算机自动控制. 

3. 后台进程一般用作系统服务，可以用crontab提交(Linux下），编辑或者删除相应得作业.



主要特征:

1. 前台进程可以以窗口,对话匡的形式在系统中显示.后台进程不行.

2. 在任务栏中点亮的进程都可以称为前台进程.没点亮的为后台进程.

3. 前台进程和后台进程有时候可以互相转换.(linux终端中执行的命令后加上&表示后台命令)

4. 一般把较为费时间的命令转到后台执行.



### 库的安装

码的安装一般由3个步骤组成：配置（configure）、编译（make）、安装（make install）

#### Configure

Configure是一个可执行脚本，它有很多选项，使用命令./configure –help输出详细的选项列表，如下

```bash
-bash-3.00# ./configure --help
Usage: configure [options] [host]
Options: [defaults in brackets after descriptions]
Configuration:
--cache-file=FILE     cache test results in FILE
--help             print this message
--no-create         do not create output files
--quiet, --silent     do not print `checking...' messages
--version           print the version of autoconf that created configure
Directory and file names:
--prefix=PREFIX       install architecture-independent files in PREFIX
                [/usr/local]
--exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
                [same as prefix]
--bindir=DIR         user executables in DIR [EPREFIX/bin]
……….(省略若干)
```

注意：

安装源码库之前需要安装这个

yum groupinstall "Development tools"



#### make

make命令是一个构建工具，用于自动化构建和编译代码。

它基于Makefile文件来控制和管理源代码的构建过程。





### CentOS 下源码安装 Node.js



1、下载源码，你需要在https://nodejs.org/en/download/下载最新的Nodejs版本

```bash
cd /usr/local/src/
wget http://nodejs.org/dist/v12.18.1/node-v12.18.1.tar.gz
```

2、解压源码

```bash
tar zxvf node-v12.18.1.tar.gz
```

3、 编译安装

```bash
cd node-v12.18.1
./configure --prefix=/usr/local/node/node-v12.18.1
make
make install
```

> ./configure是源代码安装的第一步，主要的作用是对即将安装的软件进行配置，检查当前的环境是否满足要安装软件的依赖关系，但并不是所有的tar包都是源代码的包



### 安装C++ compiler

出现下面这个WARNING 导致node安装不成功

```bash
WARNING: C++ compiler (CXX=g++, 4.8.5) too old, need g++ 6.3.0 or clang++ 8.0.0
```





### EPEL

**EPEL的全称叫 Extra Packages for Enterprise Linux** 。EPEL是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。装上了 EPEL之后，就相当于添加了一个第三方源。

如果你知道rpmfusion.org的话，拿 rpmfusion 做比较还是很恰当的，rpmfusion 主要为桌面发行版提供大量rpm包，而EPEL则为服务器版本提供大量的rpm包，而且大多数rpm包在官方 repository 中是找不到的\**。

另外一个特点是**绝大多数rpm包要比官方repository 的rpm包版本要来得新**，比如我前些日子在CentOS上安装的php，RHEL为了稳定性还在延用5.1.6版，我记得这是去年上半年的版本，而php 的最新版本已经到5.3.2，如果在php5.1.6的基础上安装phpmyadmin，则会提示php版本过低，这时候，EPEL中提供的较新php rpm就能很方便的派上用场了。





### yum



#### yum查看安装了哪些软件包

```bash
yum list installed
```



#### 查找软件包 

```bash
yum list installed | grep php
```





### rpm

[https://www.cnblogs.com/ftl1012/p/rpm.html](https://www.cnblogs.com/ftl1012/p/rpm.html)



**rpm命令**是RPM软件包的管理工具。rpm原本是Red Hat Linux发行版专门用来管理Linux各项套件的程序，由于它遵循GPL规则且功能强大方便，因而广受欢迎。逐渐受到其他发行版的采用。RPM套件管理方式的出现，让Linux易于安装，升级，间接提升了Linux的适用度。



#### 查看库安装位置

例子：

```bash
rpm -ql nginx
```











### wget 、yum和rpm区别

[https://www.cnblogs.com/applelife/p/10474704.html](https://www.cnblogs.com/applelife/p/10474704.html)

wget 类似于迅雷，是一种下载工具，

通过HTTP、HTTPS、FTP三个最常见的TCP/IP协议下载，并可以使用HTTP代理名字是World Wide Web”与“get”的结合。

yum: 是redhat, centos 系统下的软件安装方式，基于Linux，

全称为 Yellow dog Updater, Modified，是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器

基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包。

rpm: 软件管理;  redhat的软件格式 rpm 

r=redhat  p=package  m=management

用于安装 卸载 .rpm软件

串联下：

使用wget下载一个 rpm包, 然后用 rpm -ivh xxx.rpm 安装这个软件，嫌麻烦的话，就

可以直接用 yum install sqoop  来自动下载和安装依赖的rpm软件。







### 孤儿进程与僵尸进程

[https://www.cnblogs.com/anker/p/3271773.html](https://www.cnblogs.com/anker/p/3271773.html)

* **孤儿进程：一个父进程退出，而它的一个或多个子进程还在运行，那么那些子进程将成为孤儿进程。孤儿进程将被init进程(进程号为1)所收养，并由init进程对它们完成状态收集工作。**
* 僵尸进程：一个进程使用fork创建子进程，如果子进程退出，而父进程并没有调用wait或waitpid获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中。这种进程称之为僵死进程。





### 堡垒机

[堡垒机](https://www.huaweicloud.com/product/cbh.html)，即在一个特定的网络环境下，为了保障网络和数据不受来自外部和内部用户的入侵和破坏，而运用各种技术手段实时收集和监控网络环境中每一个组成部分的系统状态、安全事件、网络活动，以便集中报警、及时处理及审计定责。

堡垒机能够集中管理资产权限，全程记录操作数据，实时还原运维场景，助力企业用户构建云上统一、安全、高效运维通道；保障云端运维工作权限可管控、操作可审计、合规可遵从。在一个特定的网络环境下，为了保障网络和数据不受来自外部和内部用户的入侵和破坏，而运用各种技术手段实时收集和监控网络环境中每一个组成部分的系统状态、安全事件、网络活动，以便集中报警、及时处理及审计定责。

从功能上讲，它综合了核心系统运维和安全审计管控两大主干功能，从技术实现上讲，通过切断终端计算机对网络和服务器资源的直接访问，而采用协议代理的方式，接管了终端计算机对网络和服务器的访问。形象地说，终端计算机对目标的访问，均需要经过运维安全审计的翻译。能够拦截非法访问，和恶意攻击，对不合法命令进行命令阻断，过滤掉所有对目标设备的非法访问行为，并对内部人员误操作和非法操作进行审计监控，以便事后责任追踪。



### busybox

BusyBox是一个开源项目，它提供了一个精简的可执行程序集合，包含了许多的常用工具和一些Linux命令。BusyBox的目标是将许多常用的Unix工具和命令打包进一个小的可执行文件中，从而节省空间和提高效率。它可以在嵌入式系统中使用，通过作为可执行文件，为系统提供文件操作、网络操作、系统管理和维护等功能。BusyBox通常用于嵌入式Linux系统，如嵌入式设备、路由器等。



### systemd

> system daemon

[https://www.freedesktop.org/wiki/Software/systemd/](https://www.freedesktop.org/wiki/Software/systemd/)

`systemd` is a suite of basic building blocks for a Linux system

It provides a system and service manager that runs as PID 1 and starts the rest of the system. `systemd` provides aggressive parallelization capabilities, uses socket and D-Bus activation for starting services, offers on-demand starting of daemons, keeps track of processes using Linux control groups, maintains mount and automount points, and implements an elaborate transactional dependency-based service control logic



### Signal (IPC)

**Signals** are standardized messages sent to a running [program](https://en.wikipedia.org/wiki/Computer_program) to trigger specific behavior, such as quitting or error handling. They are a limited form of [inter-process communication](https://en.wikipedia.org/wiki/Inter-process_communication) (IPC), typically used in [Unix](https://en.wikipedia.org/wiki/Unix), [Unix-like](https://en.wikipedia.org/wiki/Unix-like), and other [POSIX](https://en.wikipedia.org/wiki/POSIX)-compliant operating systems.



#### SIGTERM

> Signal terminate
>
> 进程优雅退出一般利用SIGTERM信号。

[https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM)

The SIGTERM signal is sent to a process to request its **termination**. Unlike the SIGKILL signal, it can be caught and interpreted or ignored by the process. This allows the process to perform nice termination releasing resources and saving state if appropriate. SIGINT is nearly identical to SIGTERM.

SIGTERM是一个软件中断信号，用来请求进程正常终止。当收到SIGTERM信号时，进程可以做一些清理工作，然后优雅地退出。

与之相反，SIGKILL信号（进程终止信号）是一个不能被忽略、阻塞或者捕获的信号，一旦进程接收到SIGKILL信号，就会立即被终止，无法进行清理工作。

因此，为了保证进程能够优雅退出，一般会先发送SIGTERM信号给进程，然后等待一段时间（例如几秒钟），如果进程仍未退出，则再发送SIGKILL信号强制终止进程。这样可以给进程一个机会先进行清理工作，避免数据丢失或其他问题。



### file descriptor

[https://wiyi.org/linux-file-descriptor.html](https://wiyi.org/linux-file-descriptor.html)

file descriptor(以下简称fd)又叫文件描述符，他是一个抽象的指示符，用一个整数表示(非负整数)。它指向了由系统内核维护的一个file table中的某个条目(entry)。

本文描述的所有场景仅限于类unix系统环境，在windows中这玩意叫file handle(臭名昭著的翻译: 句柄)。





# 问题



### make问题：make[1] entering directory



执行 **make distclean** 命令。





### make问题：g++: Command not found



[https://www.cnblogs.com/kerrycode/p/4748606.html](https://www.cnblogs.com/kerrycode/p/4748606.html)



```bash
yum install "gcc-c++.x86_64"
```







### 使用yum出现Loaded plugins: fastestmirror

[https://blog.csdn.net/weixin_37282478/article/details/82152239](https://blog.csdn.net/weixin_37282478/article/details/82152239)





1.修改插件配置文件



```bash
 vim  /etc/yum/pluginconf.d/fastestmirror.conf
```



```bash
[main]
enabled=0   //由 1 改成0 ，禁用该插件
verbose=0
always_print_best_host = true
socket_timeout=3
#  Relative paths are relative to the cachedir (and so works for users as well
# as root).
hostfilepath=timedhosts.txt
maxhostfileage=10
maxthreads=15
#exclude=.gov, facebook
#include_only=.nl,.de,.uk,.ie
```





2.修改yum 配置文件



```bash
[root@localhost yum.repos.d]# vim /etc/yum.conf

9 plugins=0  //不使用插件
```





3. 清除缓存并重新构建yum 源



```bash
[root@localhost yum.repos.d]# yum clean all

[root@localhost yum.repos.d]# yum makecache
```







### yum: Maybe run: yum groups mark install 



出现错误

Maybe run: yum groups mark install (see man yum)

Warning: Group basic-desktop does not have any packages to install.



解决：



```bash
#yum clean all
```





### set an environment variable

[https://unix.stackexchange.com/questions/56444/how-do-i-set-an-environment-variable-on-the-command-line-and-have-it-appear-in-c](https://unix.stackexchange.com/questions/56444/how-do-i-set-an-environment-variable-on-the-command-line-and-have-it-appear-in-c)

```bash
TEST=foo echo $TEST  
// 执行结果 ""
```

This is because the shell expands the variable in the command line **before** it actually runs the command and at that time the variable doesn't exist. If you use

shell会展开变量在他执行命令之前

改正：

```bash
TEST=foo; echo $TEST
// 输出 ”foo“
```

it will work.
