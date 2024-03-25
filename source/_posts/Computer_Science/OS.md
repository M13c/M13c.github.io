---
title: 操作系统笔记
tags: ['操作系统']
mathjax: true
date: 2024-03-15 21:46:17
categories: Computer_Science
excerpt: 操作系统课堂笔记
---

## Processes

### 内存排布

{% asset_img 1.png %}

### 进程的状态 (State)

{% asset_img 2.png %}

### 进程控制器 (Process Control Block, PCB)

{% asset_img 3.png %}

### 进程调度器 (Process Scheduler)

{% asset_img 4.png %}

### 上下文切换 (context switch)

进程停止时要保存something到PCB中，继续进行要恢复

something有PC，寄存器,stack...

继续进行时要把PCB中的something恢复到内存中

一次上下文切换大约几十微秒，占用时间片时间千分之几

{% asset_img 5.png %}

### 挂起 (suspended)

与ready和wating的进程相比，挂起的进程的资源可能会被回收，存储在内存的东西被写到磁盘

### 进程操作

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
    
### 进程通信(Interprocess Communication)

{% asset_img 7.png %}

消息传递: blocking(阻塞，同步),non-blocking(非阻塞,异步)

## Threads

{% asset_img 8.png %}

进程是资源分配管理的最小单位，线程是调度的最小单位

### 多核编程

{% asset_img 9.png %}

### 用户线程和内核线程

+ 内核线程可以被抢占，用户线程不可以，用户进程主动释放CPU

+ 多对一

    绑定在同一内核线程的用户进程交替进行，因为只占用一个CPU核
    用户线程切换不需要内核参与，速度更快
    {% asset_img 10.png %}

### 进程创建和销毁(Pthreads)

{% asset_img 11.png %}
{% asset_img 12.png %}

### 线程池(Thread Pools)

在进程创建的时候，就创建一堆线程，让他们在获取任务的地方阻塞，当来一个任务，就从线程池中取出一个线程，完成这个任务之后，线程在放回线程池

{% asset_img 13.png %}

### 线程的问题

{% asset_img 14.png %}

## CPU调度

### 基本概念

进程的概念

{% asset_img 15.png %}

CPU密集型和IO密集型程序

{% asset_img 16.png %}

CPU burst一般很短

### CPU调度器

在上下文切换时发挥作用

{% asset_img 17.png %}

### 调度算法

+ 目标
{% asset_img 18.png %}

+ 先到先服务(FCFS) 
{% asset_img 19.png %}

+ 最短作业调度(Shortest-Job-First (SJF))
选择执行时间最短的作业

{% asset_img 20.png %}
{% asset_img 21.png %}
shortest-remaining-time-first
{% asset_img 23.png %}

有个问题：执行时间不一定能提前知道
可以预测这个线程的执行时间

{% asset_img 22.png %}

+ 轮转算法

时间片时间必须大于上下文切换时间

时间片越小，越倾向于短执行时间的作业

时间片越大，越类似于先来先服务算法

{% asset_img 24.png %}

+ 优先级调度

{% asset_img 25.png %}

{% asset_img 26.png %}

+ 优先级调度+轮转

{% asset_img 27.png %}

+ 多级队列

{% asset_img 28.png %}
{% asset_img 29.png %}


### 多核调度（Multiple-Processor Scheduling）

{% asset_img 30.png %}

+ 亲和性（Processor Affinity）

让线程保持在某一个CPU核上运行

## 互斥与同步

### race condition

并发线程对共享变量的使用引发错误

### 临界区问题（Critical Section Problem）

+ 临界区

临界区在同一时刻只允许被一个线程执行

{% asset_img 31.png %}

+ 解决措施的要求
{% asset_img 32.png %}

+ 基于中断的解决方法

    Entry section:  disable interrupts 进入时禁止中断
    Exit section:  enable  interrupts 结束时开启中断
    
    但是对于多核处理器来说，问题依然会有

+ 软件的解决（过时）

+ Peterson’s Solution（过时）

### 同步硬件

+ test_and_set 指令

原子型命令

{% asset_img 33.png %}
示例
{% asset_img 34.png %}

+ compare_and_swap 指令

{% asset_img 35.png %}
