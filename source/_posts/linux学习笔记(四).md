---
title: Linux学习笔记(四)
categories:
  - 笔记
tags:
  - 学习笔记
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-03.jpg'
abbrlink: 63a9cc99
date: 2020-06-24 00:00:00
---
## 输入输出
使用命令的输出作为可执行文件的输入参数
`$ ./vulnerable `your_command_here` `
`$ ./vulnerable $(your_command_here)`
使用命令作为输入
`$ your_command_here | ./vulnerable`
将命令行输出写入文件
`$ your_command_here > filename`
使用文件作为输入
`$ ./vulnerable < filename`

## 核心转储
当程序运行的过程中异常终止或崩溃，操作系统会将程序当时的内存、寄存器状态、堆栈指针、内存管理信息等记录下来，保存在一个文件中，这种行为就叫做核心转储（Core Dump）。

## 会产生核心转储的信号

| Signal	| Action	| Comment |
| :-----	| :-----| :----- |
| SIGQUIT	| Core	| Quit from keyboard | 
| SIGILL	| Core	| Illegal Instruction | 
| SIGABRT	| Core	| Abort signal from abort | 
| SIGSEGV	| Core	| Invalid memory reference | 
| SIGTRAP	| Core	| Trace/breakpoint trap | 

## 开启核心转储
输入命令 `ulimit -c`，输出结果为` 0`，说明默认是关闭的。
输入命令 `ulimit -c unlimited `即可在当前终端开启核心转储功能。
如果想让核心转储功能永久开启，可以修改文件 `/etc/security/limits.conf`，增加一行：
```bash
#<domain>      <type>  <item>         <value>
*               soft    core            unlimited
```

## 修改转储文件保存路径
• 通过修改 `/proc/sys/kernel/core_uses_pid`，可以使生成的核心转储文件名变为 `core.[pid]` 的模式。
`# echo 1 > /proc/sys/kernel/core_uses_pid`
• 还可以修改 `/proc/sys/kernel/core_pattern` 来控制生成核心转储文件的保存位置和文件名格式。
`# echo /tmp/core-%e-%p-%t > /proc/sys/kernel/core_pattern`
此时生成的文件保存在` /tmp/` 目录下，文件名格式为` core-[filename]-[pid]-[time]`。

## 使用 gdb 调试核心转储文件
`gdb [filename] [core file]`
