---
layout:     post
title:      "Linux基础学习"
date:       2018-09-25 17:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Linux
---

> “Yeah It's on. ”


## 正文
[网页链接](https://github.com/CyC2018/CS-Notes/blob/97a21c792753c2b7a6a74e8c7ef5875f7d95bc26/notes/Linux.md)


许多程序需要开机启动。它们在Windows叫做"服务"（service），在Linux就叫做"守护进程"（daemon）

[linux下文件的复制、移动与删除](https://www.cnblogs.com/qiuhong10/p/7940410.html)


[Linux中ps命令详解](https://blog.csdn.net/tanga842428/article/details/52742360)


### linux上进程有5种状态: 

1.  运行(正在运行或在运行队列中等待) 
2.  中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号) 
3.  不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生) 
4.  僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放) 
5.  停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行)


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


### rm -rf
删除文件夹的命令 使用rm -rf 目录名字 命令即可

* -r 就是向下递归，不管有多少级目录，一并删除
* -f 就是直接强行删除，不作任何提示的意思


### 查看文件内容
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





### df 显示可使用的磁盘空间

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


### 查看可用的内存

free -h



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


### 查看当前所在目录的全路径

使用pwd命令  **（全称是Print Working Directory）**



### echo命令

echo命令的功能是在显示器上显示一段文字，一般起到一个提示的作用。


该命令的一般格式为： echo [ -n ] 字符串

其中选项n表示输出文字后不换行；字符串能加引号，也能不加引号。用echo命令输出加引号的字符串时，将字符串原样输出；用echo命令输出不加引号的字符串时，将字符串中的各个单词作为字符串输出，各字符串之间用一个空格分割。


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







## 补充





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







