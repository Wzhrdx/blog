---
title: Linux学习笔记(二)
categories:
  - 笔记
tags:
  - 学习笔记
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-05.jpg'
abbrlink: e71903bd
date: 2020-05-28 00:00:00
---
## 根目录结构
```bash
$ uname -a
Linux manjaro 4.11.5-1-ARCH #1 SMP PREEMPT Wed Jun 14 16:19:27 CEST 2017 x86_64 GNU/Linux
$ ls -al /
drwxr-xr-x  17 root root  4096 Jun 28 20:17 .
drwxr-xr-x  17 root root  4096 Jun 28 20:17 ..
lrwxrwxrwx   1 root root     7 Jun 21 22:44 bin -> usr/bin
drwxr-xr-x   4 root root  4096 Aug 10 22:50 boot
drwxr-xr-x  20 root root  3140 Aug 11 11:43 dev
drwxr-xr-x 101 root root  4096 Aug 14 13:54 etc
drwxr-xr-x   3 root root  4096 Apr  8 19:59 home
lrwxrwxrwx   1 root root     7 Jun 21 22:44 lib -> usr/lib
lrwxrwxrwx   1 root root     7 Jun 21 22:44 lib64 -> usr/lib
drwx------   2 root root 16384 Apr  8 19:55 lost+found
drwxr-xr-x   2 root root  4096 Oct  1  2015 mnt
drwxr-xr-x  15 root root  4096 Jul 15 20:10 opt
dr-xr-xr-x 267 root root     0 Aug  3 09:41 proc
drwxr-x---   9 root root  4096 Jul 22 22:59 root
drwxr-xr-x  26 root root   660 Aug 14 21:08 run
lrwxrwxrwx   1 root root     7 Jun 21 22:44 sbin -> usr/bin
drwxr-xr-x   4 root root  4096 May 28 22:07 srv
dr-xr-xr-x  13 root root     0 Aug  3 09:41 sys
drwxrwxrwt  36 root root  1060 Aug 14 21:27 tmp
drwxr-xr-x  11 root root  4096 Aug 14 13:54 usr
drwxr-xr-x  12 root root  4096 Jun 28 20:17 var

```
由于不同的发行版会有略微的不同，我们这里使用的是基于 Arch 的发行版 Manjaro，以上就是根目录下的内容，我们介绍几个重要的目录：
• /bin、/sbin：链接到 /usr/bin，存放 Linux 一些核心的二进制文件，其包含的命令可在 shell 上运行。
• /boot：操作系统启动时要用到的程序。
• /dev：包含了所有 Linux 系统中使用的外部设备。需要注意的是这里并不是存放外部设备的驱动程序，而是一个访问这些设备的端口。
• /etc：存放系统管理时要用到的各种配置文件和子目录。
• /etc/rc.d：存放 Linux 启动和关闭时要用到的脚本。
• /home：普通用户的主目录。
• /lib、/lib64：链接到 /usr/lib，存放系统及软件需要的动态链接共享库。
• /mnt：这个目录让用户可以临时挂载其他的文件系统。
• /proc：虚拟的目录，是系统内存的映射。可直接访问这个目录来获取系统信息。
• /root：系统管理员的主目录。
• /srv：存放一些服务启动之后需要提取的数据。
• /sys：该目录下安装了一个文件系统 sysfs。该文件系统是内核设备树的一个直观反映。当一个内核对象被创建时，对应的文件和目录也在内核对象子系统中被创建。
• /tmp：公用的临时文件存放目录。
• /usr：应用程序和文件几乎都在这个目录下。
• /usr/src：内核源代码的存放目录。
• /var：存放了很多服务的日志信息

## UID 和 GID
Linux 是一个支持多用户的操作系统，每个用户都有 User ID(UID) 和 Group ID(GID)，UID 是对一个用户的单一身份标识，而 GID 则对应多个 UID。知道某个用户的 UID 和 GID 是非常有用的，一些程序可能就需要 UID/GID 来运行。可以使用 id 命令来查看：
```
$ id root
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),19(log)
$ id wzhrdx
uid=1000(wzhrdx) gid=1000(wzhrdx) groups=1000(wzhrdx),3(sys),7(lp),10(wheel),90(network),91(video),93(optical),95(storage),96(scanner),98(power),56(bumblebee)
```
UID 为 0 的 root 用户类似于系统管理员，它具有系统的完全访问权。我自己新建的用户 wzhrdx，其 UID 为 1000，是一个普通用户。GID 的关系存储在 /etc/group 文件中：

```
$ cat /etc/group
root:x:0:root
bin:x:1:root,bin,daemon
daemon:x:2:root,bin,daemon
sys:x:3:root,bin,firmy
......
```
所有用户的信息（除了密码）都保存在 /etc/passwd 文件中，而为了安全起见，加密过的用户密码保存在 /etc/shadow 文件中，此文件只有 root 权限可以访问。
`$ sudo cat /etc/shadow`
由于普通用户的权限比较低，这里使用 sudo 命令可以让普通用户以 root 用户的身份运行某一命令。使用 su 命令则可以切换到一个不同的用户：

```
$ whoami
wzhrdx
$ su root
$ whoami
root
```
whoami 用于打印当前有效的用户名称，shell 中普通用户以 $ 开头，root 用户以 # 开头。在输入密码后，我们已经从 wzhrdx 用户转换到 root 用户了。

## 权限设置
在　Linux 中，文件或目录权限的控制分别以读取、写入、执行 3 种一般权限来区分，另有 3 种特殊权限可供运用。
使用 ls -l [file] 来查看某文件或目录的信息：
$ ls -l /
lrwxrwxrwx   1 root root     7 Jun 21 22:44 bin -> usr/bin
drwxr-xr-x   4 root root  4096 Jul 28 08:48 boot
-rw-r--r--   1 root root 18561 Apr  2 22:48 desktopfs-pkgs.txt
第一栏从第二个字母开始就是权限字符串，权限表示三个为一组，依次是所有者权限、组权限、其他人权限。每组的顺序均为 rwx，如果有相应权限，则表示成相应字母，如果不具有相应权限，则用 - 表示。
• `r`：读取权限，数字代号为 “4”
• `w`：写入权限，数字代号为 “2”
• `x`：执行或切换权限，数字代号为 “1”
通过第一栏的第一个字母可知，第一行是一个链接文件 （l），第二行是个目录（d），第三行是个普通文件（-）。
用户可以使用 chmod 指令去变更文件与目录的权限。权限范围被指定为所有者（u）、所属组（g）、其他人（o）和所有人（a）。
• `-R`：递归处理，将指令目录下的所有文件及子目录一并处理；
• `<权限范围>+<权限设置>`：开启权限范围的文件或目录的该选项权限设置
• 如`$ chmod a+r [file]`：赋予所有用户读取权限
• `<权限范围>-<权限设置>`：关闭权限范围的文件或目录的该选项权限设置
• 如`$ chmod u-w [file]`：取消所有者写入权限
• `<权限范围>=<权限设置>`：指定权限范围的文件或目录的该选项权限设置；
• 如`$ chmod g=x [file]`：指定组权限为可执行
•   `$ chmod o=rwx [file]`：制定其他人权限为可读、可写和可执行
![图片来自firmianay](https://s1.ax1x.com/2020/06/25/NBB9wn.png) 
图片来自firmianay