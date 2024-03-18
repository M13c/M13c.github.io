---
title: 网络安全基础知识-用户组管理
tags: ['用户组管理']
mathjax: true
date: 2024-03-18 19:02:49
categories: Cyberspace_Security
excerpt: b站千峰课程笔记 Day03
---

## 一、服务器版本介绍

+ Windows服务器系统：win2000 win2003 win2008 win2012
+ Linux服务器系统：Redhat CentOS

## 二、用户管理

### 1.用户概述

1. 每个用户登录系统后，拥有不同的权限

2. 每个账户有自己唯一的SID（安全标识符）

    >用户SID：S-1-5-21-426206823-2579496042-14852678-500
    >
    >系统SID：S-1-5-21-426206823-2579496042-14852678
    >
    >SID= 系统标识 + UID，其中用户UID：500，即windows系统管理员administrator的UID。
    >
    >UID：用户ID，windows系统管理员administrator的UID为500，普通用户的UID从1000开始。
    >
    >查看SID值：whoami /user

3. 账户密码存储位置：
    >c:\windows\system32\config\SAM #暴力破解/撞库

    {% notel 注意 %}
    Hash算法加密不可逆，只能暴力破解或者撞库，暴力破解可以用工具，输入其手机号、身份证号、手机号，社会工程学
    {% endnotel %}

4. Windows系统上，默认密码最长有效期为42天，进行安全加固

### 2. 内置账户（系统自带）
1. 给人使用的账户：

    >administrator       #管理员账户
    >
    >guest               #来宾账户 == 权限最低
    
2. 给系统使用的计算机服务组件相关的系统账户：

    >system              #系统账户 == 权限至高无上
    >
    >local services      #本地服务账户 == 权限等于普通用户
    >
    >network services    #网络服务账户 == 权限等于普通用户

### 3. 配置文件（Linux 称作 家）

每个用户都有自己的配置文件（家目录），在用户第一次登录时自动产生，路径时：

>win7/win2008        路径： C:\用户\
>
>xp/win2003          路径： C:\Documents and Settings\

{% notel 总结%}
%userprofile% = C:\Users\* = C:\用户\* = 用户配置文件 = 用户家目录
{% endnotel %}

### 4. 用户管理命令

>net user                       #查看用户列表
>
>net user 用户名                 #查看用户详细信息
>
>net user 用户名 旧密码 新密码                #给用户修改密码，只有管理员可以使用
>
>net user 用户名 密码 /add        #新建用户，密码
>
>net user 用户名 /del            #删除用户
>
>net user 用户名 /active:yes/no  #激活或禁用账户

## 二、组管理
### 1. 组概述
1. 组的作用：简化权限的赋予过程

2. 赋权限方式：

    + 通过对用户所在的组赋权限： 用户—组—赋权限
    + 直接对用户赋权限：用户—赋权限

### 2. 内置组
内置组的权限默认已经被系统赋予

>(1) administrators  #管理员组
>
>(2) guests          #来宾组
>
>(3) users           #普通用户组，默认新建用户都属于该组
>
>(4) network         #网络配置组
>
>(5) print           #打印机组
>
>(6) Remote Desktop  #远程桌面组

### 3. 组管理命令
>(1) net localgroup               #查看组列表
>
>(2) net localgroup 组名           #查看该组的成员
>
>(3) net localgroup 组名 /add      #创建新的用户组，本身没权限，需手动赋权限
>
>(4) net localgroup 组名 /del      #删除该用户组
>
>(5) net localgroup 组名 用户 /add  #将用户添加到该组中
>
>(6) net localgroup 组名 用户 /del  #将用户踢出该组

## 服务器远程管理

### 1. 远程管理类型

windows远程管理有2种类型：

+ 远程桌面（图形化远程管理）：mstsc工具连接服务，端口3389
+ telnet（命令行远程管理）：telnet 服务器ip连接服务，端口23

### 2. 远程桌面协议RDP（图形化）

步骤

>(1) 通信：配置网络，实现客户机和服务器ping互通。xp和2003在net1中，10.1.1.1和10.1.1.2
>(2) 开启服务：服务器开启远程管理服务。我的电脑—属性—远程—远程桌面—启用
>(3) 连接服务：客户机使用服务。开始—运行—输入mstsc打开远程连接工具，输入主机IP，
>(4) 登录服务：输入主机服务器IP，输入用户账号、密码。

{% notel 注意 %}
如果使用非管理员账户（如普通用户）登录远程，需要在服务器上将用户加入到远程桌面内置组Remote Desktop Users中
{% endnotel %}

### 3. Telnet（命令行）

步骤

>(1) 打开服务器上telnet服务：win+r—输入services.msc—找到telnet—禁用改成自动—应用—启用
>(2) 将用户加入TelnetClients组：net localgroup TelnetClients 用户名 /add
>(3) 客户机xp连接服务，输入服务器的IP 及用户账号、密码。
输入telnet 10.1.1.2 — 回车 — 输入用户账号和密码

漏洞

>(1) 判断公司是哪个网段，通过IP和子网掩码判断网段
>(2) 扫描该网段1-254所有端口，看哪个IP开放了23端口
>(3) telnet的密码不加密，账号和密码可以破解，可以中间劫持流量获取

{% notel blue 视频地址 %}
[视频地址](https://www.bilibili.com/video/BV1Lf4y1t7Mc?p=16&vd_source=f6750243303df70ef9861eee3a2e11e8)
{% endnotel %}