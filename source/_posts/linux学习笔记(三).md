---
title: Linux学习笔记(三)
date: 2020-06-5
categories:
  - 笔记
tags:
  - 学习笔记
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-04.jpg'
abbrlink: 99ea2396
---
## 文件描述符
在 Linux 系统中一切皆可以看成是文件，文件又分为：普通文件、目录文件、链接文件和设备文件。文件描述符（file descriptor）是内核管理已被打开的文件所创建的索引，使用一个非负整数来指代被打开的文件。
标准文件描述符如下：

| 文件描述符	| 用途	| stdio 流| 
| :-----	| :-----| :-----|
| 0	| 标准输入	| stdin| 
| 1	| 标准输出	| stdout| 
| 2	| 标准错误	| stderr| 

当一个程序使用 fork() 生成一个子进程后，子进程会继承父进程所打开的文件表，此时，父子进程使用同一个文件表，这可能导致一些安全问题。如果使用 vfork()，子进程虽然运行于父进程的空间，但拥有自己的进程表项。

## 输入输出
• 使用命令的输出作为可执行文件的输入参数
```bash
$ ./vulnerable `your_command_here` 或者
$ ./vulnerable $(your_command_here)
```
• 使用命令作为输入
```bash
$ your_command_here | ./vulnerable
```
• 将命令行输出写入文件
```bash
$ your_command_here > filename
```
• 使用文件作为输入
```bash
$ ./vulnerable < filename
```

## 字节序
目前计算机中采用两种字节存储机制：大端（Big-endian）和小端（Little-endian）。
`	MSB (Most Significan Bit/Byte)：最重要的位或最重要的字节。`
`	LSB (Least Significan Bit/Byte)：最不重要的位或最不重要的字节。`
Big-endian 规定 MSB 在存储时放在低地址，在传输时放在流的开始；LSB 存储时放在高地址，在传输时放在流的末尾。Little-endian 则相反。常见的 Intel 处理器使用 Little-endian，而 PowerPC 系列处理器则使用 Big-endian，另外 TCP/IP 协议和 Java 虚拟机的字节序也是 Big-endian。
例如十六进制整数 0x12345678 存入以 1000H 开始的内存中：
![图片](https://s1.ax1x.com/2020/06/25/NBD9je.png)
`图片来自firmianay`
我们在内存中实际地看一下，在地址`0xffffd584` 处有字符`1234`，在地址`0xffffd588`处有字符`5678`。
```bash
gdb-peda$ x/w 0xffffd584
0xffffd584:     0x34333231
gdb-peda$ x/4wb 0xffffd584
0xffffd584:     0x31    0x32    0x33    0x34
gdb-peda$ python print('\x31\x32\x33\x34')
1234
gdb-peda$ x/w 0xffffd588
0xffffd588:     0x38373635
gdb-peda$ x/4wb 0xffffd588
0xffffd588:     0x35    0x36    0x37    0x38
gdb-peda$ python print('\x35\x36\x37\x38')
5678
gdb-peda$ x/2w 0xffffd584
0xffffd584:     0x34333231      0x38373635
gdb-peda$ x/8wb 0xffffd584
0xffffd584:     0x31    0x32    0x33    0x34    0x35    0x36    0x37    0x38
gdb-peda$ python print('\x31\x32\x33\x34\x35\x35\x36\x37\x38')
123455678
db-peda$ x/s 0xffffd584
0xffffd584:     "12345678"
```
