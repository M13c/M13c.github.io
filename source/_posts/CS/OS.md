---
title: 操作系统
tags: ['操作系统']
mathjax: true
date: 2024-03-15 21:46:17
categories: CS
excerpt: 操作系统课堂笔记
---

# Processes

+ 内存排布

{% asset_img 1.png %}

+ 进程的状态 (State)

{% asset_img 2.png %}

+ 进程控制器 (Process Control Block, PCB)

{% asset_img 3.png %}

+ 进程调度器 (Process Scheduler)

{% asset_img 4.png %}

+ 上下文切换 (context switch)

进程停止时要保存something到PCB中，继续进行要恢复

something有PC，寄存器...

继续进行时要把PCB中的something恢复到内存中

一次上下文切换大约几十微秒，占用时间片时间千分之几

{% asset_img 5.png %}

+ 挂起 (suspended)

与ready和wating的进程相比，挂起的进程的资源可能会被回收，存储在内存的东西被写到磁盘

+ 进程操作

    1. 创建进程 (Process Creation)
    fork() 会新建一个子进程，并用父进程初始化子进程
    
    {% asset_img 6.png %}
    
    2. 进程终止
    
        主动终止 exit() 
    
        异常终止 signal
    
        SIGTERM (Ctrl + C)
    
        SIGABRT abort()
    
        SIGSEGV 访问了不能访问的资源，指针
    
        SIGKILL=9 直接终止 kill -9 pid
    
+ 进程通信(Interprocess Communication)

{% asset_img 7.png %}

消息传递: blocking(阻塞，同步),non-blocking(非阻塞,异步)

# Threads

{% asset_img 8.png %}