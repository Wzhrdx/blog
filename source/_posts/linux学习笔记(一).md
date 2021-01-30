---
title: Linux学习笔记(一)
categories:
  - 笔记
tags:
  - 学习笔记
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-02.jpg'
abbrlink: 482898df
date: 2020-05-22 00:00:00
---
## 基础操作

| command	| |
| :-----	| :-----|
| ls                    | 用来显示目标列表                                                                               | 
| cd [path]        	    | 用来切换工作目录                                                                               | 
| pwd                   | 以绝对路径的方式显示用户当前工作目录                                                           | 
| man [command]      	| 查看Linux中的指令帮助、配置文件帮助和编程帮助等信息                                            | 
| apropos [whatever]  	| 在一些特定的包含系统命令的简短描述的数据库文件里查找关键字                                     | 
| echo [string]       	| 打印一行文本，参数“-e”可激活转义字符                                                           | 
| cat [file]            | 连接文件并打印到标准输出设备上                                                                 | 
| less [file]         	| 允许用户向前或向后浏览文字档案的内容                                                           | 
| mv [file1] [file2]  	| 用来对文件或目录重新命名，或者将文件从一个目录移到另一个目录                                   | 
| cp [file1] [file2]  	| 用来将一个或多个源文件或者目录复制到指定的目的文件或目录                                       | 
| rm [file]         	| 可以删除一个目录中的一个或多个文件或目录，也可以将某个目录及其下属的所有文件及其子目录均删除掉 | 
| ps                 	| 用于报告当前系统的进程状态 | 
| top                 	| 实时查看系统的整体运行情况 | 
| kill                  | 杀死一个进程 | 
| ifconfig           	| 查看或设置网络设备 | 
| ping                	| 查看网络上的主机是否工作 | 
| netstat             	| 显示网络连接、路由表和网络接口信息 | 
| nc(netcat)          	| 建立 TCP 和 UDP 连接并监听 | 
| su                  	| 切换当前用户身份到其他用户身份 | 
| touch [file]        	| 创建新的空文件 | 
| mkdir [dir]         	| 创建目录 | 
| chmod               	| 变更文件或目录的权限 | 
| chown               	| 变更某个文件或目录的所有者和所属组 | 
| nano / vim / emacs  	| 字符终端的文本编辑器 | 
| exit                	| 退出 shell | 
| 管道命令符 &#124;   	| 将一个命令的标准输出作为另一个命令的标准输入 | 

## Bash 快捷键

| command	| |
| :-----	| :-----|
| Up(Down)          	| 上（下）一条指令| 
| Ctrl + c          	| 终止当前进程| 
| Ctrl + z         	    | 挂起当前进程| 
| Ctrl + d          	| 删除光标处的字符| 
| Ctrl + l          	| 清屏| 
| Ctrl + a          	| 移动到命令行首| 
| Ctrl + e          	| 移动到命令行尾| 
| Ctrl + b          	| 按单词后移（向左）| 
| Ctrl + f          	| 按单词前移（向右）| 
| Ctrl + Shift + c  	| 复制| 
| Ctrl + Shift + v  	| 粘贴| 

## 进程管理
• top
可以实时动态地查看系统的整体运行情况。
• ps
用于报告当前系统的进程状态。可以搭配 kill 指令随时中断、删除不必要的程序。
查看某进程的状态：$ ps -aux | grep [file]，其中返回内容最左边的数字为进程号（PID）。
• kill
用来删除执行中的程序或工作。
删除进程某 PID 指定的进程：$ kill [PID]

## 如何使用变量

|command	| |
| :-----	| :-----|
| var=value         	| 给变量var赋值value| 
| $var, ${var}      	| 取变量的值| 
| 'string'          	| 非替换字符串| 
| "string"          	| 可替换字符串| 

