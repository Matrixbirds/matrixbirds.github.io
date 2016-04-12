title: Linux中的process和thread
date: 2016-04-11 22:56:18
tags:
  - linux
  - computer multitasking
---

#### 什么是computer multitasking?

`computer multitasking`俗称多任务处理。是指计算机同时处理多个任务的能力。
多任务的实现方式，一般是运行第一个任务的一部分代码，保存上下文，再执行第二个任务，保存上下文。
然后恢复第一个程序的上下文继续执行……


#### Linux里的Process  

##### `Process`俗称进程。是一个装载程序代码的容器。
进程还包括了一系列资源：`打开文件`、`待定的信号量`、`内部的内核数据`、
`处理器状态`、`内存地址空间`、`线程`、`数据区块：包含了很多全局变量`  

##### 如何通过`Processes`(多个进程)实现`computer multitasking`?
`Cooperative multitasking`

##### `Thread`俗称线程。是进程的一部分。
