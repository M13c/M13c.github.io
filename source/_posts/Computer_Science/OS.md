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

## 同步机制

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

### 同步硬件（忙等）

+ test_and_set 指令

    原子型命令

    {% asset_img 33.png %}
    示例
    {% asset_img 34.png %}

+ compare_and_swap 指令

    {% asset_img 35.png %}

    这俩会有不断循环，没解决内存可见性

### 信号量（Semaphore）

+ 定义

{% asset_img 36.png %}

+ Implementation with no Busy waiting 

    {% asset_img 37.png %}
    {% asset_img 38.png %}

    大于0，代表无阻塞的调用wait的次数，被保护的这个资源可以同时被多少个进程访问
    小于0，代表有多少的个进程阻塞在等待队列
    
### 管程（Monitors）

+ 定义
    {% asset_img 39.png %}
    1. 变量都为私有变量
    2. 同一时刻只有一个线程在管程中
    
+ 条件变量
    {% asset_img 40.png %}
    实际上是一个等待队列
    
+ 带有条件变量的管程
    {% asset_img 41.png %}

+ 例子
    ```cpp
    int count = 8;
    condition x;

    void borrow (int n) {
        while (count < n) {
            x.wait();
        }
        count -= n;
    }

    void returm (int n) {
        count += n;
        //x.signal();
        x.signalAll();
    }
    ```
    
### 活性（Liveness）

+ 死锁
    {% asset_img 42.png %}

+ 饥饿
    {% asset_img 43.png %}
    
## 同步案例

### 生产者-消费者问题（Bounded-Buffer Problem）

+ Semaphore 做法
    ```cpp
    Item buffer[N];
    int in = 0, out = 0;
    int count = 0;

    Semaphore empty = N, full = 0, mutex = 1;

    void put (o) {
        empty.wait();
        
        mutex.wait();
        buffer[in] = o;
        in = (in + 1) % N;
        count++;
        mutex.signal();
        
        full.signal();
    }

    void get () {
        full.wait();
        
        mutex.wait();
        o = buffer[out];
        out = (out + 1) % N;
        count--;
        mutex.signal();
        
        empty.signal();
        return o;
    }
    ```
    假设5个消费者，N = 6，4个消费者
    empty 范围 [-5, 6]
    full 范围 [-4, 6]
    mutex 范围 [(-5), 1]
    
+ Monitors做法

    ```cpp
    Item buffer[N];
    int in = 0, out = 0;
    int count = 0;
    
    condition notFull, notEmpty;
    
    put (o) {
        while (count == N) {
            notFull.wait();
        }
        buffer[in] = o;
        in = (in + 1) % N;
        count++;
        notEmpty.signal();
    }
    
    get () {
        while (count == 0) {
            notFull.wait();
        }
        o = buffer[out];
        out = (out + 1) % N;
        count--;
        notFull.signal();
        return o;
    }
    ```
### 读者-写者问题(Readers-Writers Problem)

{% asset_img 44.png%}

+ Semaphore 做法

    ```cpp
    Semaphore mutex = 1, ,
    int cnt = 0;

    beginWrite() {
        mutex.wait();
    }

    endWrite() {
        mutex.signal();
    }

    beginRead() {
        x.wait();
        cnt++;
        if (cnt == 1) {
            mutex.wait();
        }
        x.signal();
    }

    endRead() {
        x.wait();
        cnt--;
        if (cnt == 0) {
            mutex.signal();
        }
        x.signal();
    }
    ```
    这个解法对读者是优先的
    
+ Monitors做法

    ```cpp
    condition r, w;
    int rc = 0, wc = 0;//读写者的计数器

    beginWrite() {
        while (rc > 0 || wc > 0) {
            w.wait();
        }
        wc++;
    }

    endWrite() {
        wc--;
        w.signal();
        r.signalAll();//要唤醒所有读者
    }

    beginRead() {
        while(wc > 0) {
            r.wait();
        }
        rc++;
    }

    endRead() {
        if (rc == 0) {
            w.signal();
        }
    }
    ```

### 哲学家进餐问题(Dining-Philosophers Problem)

{% asset_img 45.png%}


+ Semaphore 做法

    ```cpp
    Semaphore C[S] = {1,1,1,1,1};

    P(i) {
        C[i].wait();
        C[(i+1) % 5].wait();
        eat();
        C[i].signal();
        C[(i+1) % 5].signal();
    } 
    ```

    死锁问题
    
+ Monitors做法
```cpp
condition r[5];
int c[S] = {1,1,1,1,1};

beginEat(i) {
    while (c[i] == 0 || c[(i + 1) % 5] == 0) {
        r[i].wait();
    }
    c[i] = 0;
    c[(i + 1) % 5] = 0;
}

endEat(i) {
    c[i] = 1;
    c[(i + 1) % 5] = 1;
    r[(i + 4) % 5].signal();
    r[(i + 1) % 5].signal();
}
```

## 死锁

### 系统模型

{% asset_img 46.png%}

+ 信号量版死锁

{% asset_img 47.png%}

+ 资源分配图

{% asset_img 48.png%}
{% asset_img 49.png%}
死锁一定会出现环，有环不一定有死锁
{% asset_img 50.png%}
{% asset_img 51.png%}

+ 死锁的性质
{% asset_img 52.png%}

### 死锁预防

利用性质破坏死锁
{% asset_img 53.png%}

抢占和非抢占的区别，资源是否可以保存并且可以重新启用
{% asset_img 54.png%}

### 死锁避免

+ 安全状态
{% asset_img 55.png%}

+ 银行家算法
{% asset_img 56.png%}

### 死锁检测

+ 单个实例资源 可以用资源分配图来检测是否有死锁

+ 多个实例资源
{% asset_img 57.png%}

## 内存管理

### 背景
+ 地址绑定

    可能发生在编译、链接、装入、执行阶段
    {% asset_img 58.png%}

+ 保护
    {% asset_img 59.png%}
    
+ 逻辑地址和物理地址

+ MMU

+ 动态加载与动态链接

### 连续内存分配

+ 
    {% asset_img 60.png%}
    {% asset_img 61.png%}
    
+ 碎片
    {% asset_img 62.png%}

### gc 垃圾回收机制

+ 引用计数
    循环引用无法回收
    
+ 标记-清除
    需要冻结进程，有时间消耗
    
### 分页

> 独立的逻辑地址空间

{% asset_img 63.png%}
{% asset_img 64.png%}   
{% asset_img 65.png%}

+ Translation Look-Aside Buffer （TLB）

TLB是在CPU Cache中，页表在内存中
Page-table base register (PTBR) 指向页表
Page-table length register (PTLR) 表示页表长度
每个进程一个页表

{% asset_img 66.png%}

+ 内存保护

{% asset_img 67.png%}
{% asset_img 68.png%}

+ 多级页表

{% asset_img 69.png%}

### 共享内存

不同的进程页表指向同一物理地址帧号
实际会让不同进程相同页号存放该同一物理地址帧号

### 分段

## 虚拟内存 

### Demand paging （请求分页）

+ 有效位
    如果有物理帧和逻辑页对应就是v，没有就是i
    {% asset_img 70.png%}
    
    发生缺页的时候，操作系统接管去补页
    {% asset_img 71.png%}
    
+ 缺页率
    {% asset_img 72.png%}
    
### 页面置换

+ dirty bit
    标志该页是否被修改过

+ 先进先出算法
    {% asset_img 73.png%}
    
+ 最优算法
    替换出最近不会使用的页，预测
    {% asset_img 74.png%}
    
+ Least Recently Used (LRU) Algorithm
    替换出最近不会使用的页，过去
    {% asset_img 75.png%}
    局部性原理，历史总是惊人的相似  

### 分配帧

全局分配，局部分配

### 抖动

在进程没有足够的页时，缺页率会很高，发生抖动

## 大容量储存设备

### 磁盘

{% asset_img 76.png%}

读写以块为单位，

CPU不能直接读取（对所有的大容量储存设备都一样），必须读取到内存

### 固态硬盘

