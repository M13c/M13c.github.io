---
title: 网络安全基础知识-dos命令&批处理
tags: [dos命令, 批处理]
mathjax: true
date: 2024-03-17 13:51:23
categories: Cyberspace_Security
excerpt: b站千峰课程笔记 Day03
---

## 基本DOS命令

### 1. DOS命令
win+r—cmd—回车—打开DOS窗口 

### 2. 基本命令

1. clolor
>改变背景颜色：color f0 
>
>帮助：color ?

2. 清屏：
>cls

3. 配置IP地址：
>netsh interface ip set address 本地连接 static 10.1.1.1 255.255.255.0   #设置IP地址
>
>netsh interface ip set dns 本地连接 static 10.1.1.2   #设置DNS地址
>
>ipconfig /all   #查看IP

### 3. 目录相关命令

1. 浏览当前文件夹的内容：dir 
>dir 指定路径
>
>dir d:
>
>dir d:/pic
>
>dir /a      #浏览所有内容，包括隐藏文件

2. 切换分区，如
>c:
>
>d:
> 
>e:

3. 退到上级目录
>cd .. 

4. 进入文件夹
>cd 文件夹

5. 直接退出到根路径
> cd \

6. 补全路径
>Tab 键

### 4. 文件夹相关命令

1. 创建文件夹
>md 文件夹名

2. 删除
>rd 文件夹名     # 删除空文件夹
>
>rd 文件夹名 /s /q # 无提示删除非空文件夹
>
>rd . /s/q   # 无提示删除所有文件和文件夹

3. 路径
>相对路径：针对当前路径有效，如..\456
>
>绝对路径：从根目录开始写路径，如\123\345

### 5. 文件相关命令
1. echo
    >echo 内容 >> 文件 #添加的内容有限，只有一行
    >
    >echo 追加内容  >> [路径\ ]文件名.拓展名
    >
    >echo 覆盖内容 > [路径\ ]文件名.拓展名

    {% notel 注意%}
    \>>和>都可以将命令的输出内容输入到某文件中，若文件不存在，则同时创建该文件
    {% endnotel %}
    案例：修改hosts文件
    >echo 1.1.1.1 [www.baidu.com](http://www.baidu.com) >> c:\windows\system32\etc\hosts

2. 重定向符号：
> 1>   #正确输出
>
> 2>   #错误输出
>
> &2 错误输出通道
>
> \>>   #追加
>
> \>    #覆盖

    示例1
    >[root@redhat box]# ls a.txt b.txt  1>file.out 2>file.err 
    >
    >执行后,没有任何返回值. 原因是, 返回值都重定向到相应的文件中了,而不再前端显示 
    >
    >[root@redhat box]# cat file.out 
    >
    >结果：a.txt 
    >
    >[root@redhat box]# cat file.err 
    >
    >结果：ls: b.txt: No such file or directory 

    {% notel %}
    一般来说, "1>" 通常可以省略成 ">". 
    {% endnotel %}

    示例2
    >[root@redhat box]# ls a.txt b.txt 1>file.out  2>&1  
    >
    >[root@redhat box]# cat file.out 
    >
    >ls: b.txt: No such file or directory 
    >
    >a.txt 

    现在, 正确的输出和错误的输出都定向到了file.out这个文件中, 而不显示在前端. 

3. 浏览文件内容：
>type 文件名.扩展名

4. 删除文件
>del 文件名.扩展
>
>del *.txt        #删除所有txt文件
>
>del *.*          #删除所有文件
>
>del *.* /s/q     #无提示删除所有文件

5. 修改文件名
>ren 旧名 新名

6. 隐藏文件，修改文件或文件夹隐藏属性：
> attrib +h 文件全名/文件夹名     #隐藏文件或文件夹
>
> attrib +s +h +a 文件全名/文件夹名   #提升为被系统保护的文件

    注释：＋改为－，取消修改文件属性，＋a为只读属性

7. 复制文件
>copy [路径]源文件全名 目标路径[\新文件全名]

8. 移动文件
>move [路径]源文件全名 目标路径[\新文件全名]

### 6. 定时关机或重启

1. 命令：定时关机或重启
>shutdown -s -t 秒            #定时关机
>
>shutdown -s -f -t 秒         #定时强制关机
>
>shutdown -s -t 秒 -c “提示信息”  #定时强制关机，并提示信息
>
>shutdown -r -t 秒            #定时重启，加-f强制重启
>
>shutdown -a         #取消定时
>
>shutdown -l         #注销，通logoff命令相同

### 7. 其他
1. 分页显示：| more
>type 文件名.扩展名 | more
>
>dir c:\windows | more 

2. 创建文件两种方式：
>echo 编辑内容 >> 文件名.扩展名    #编写单行
>
>copy con 文件名.扩展名           #编写多行，ctrl+z结束编写 con是控制台

3. 快速生成一个空文件
>fsutil file createnew c:\windows\system.ini 4096000000

4. 修改关联：
>assoc .txt=exefile   #解绑：assoc .txt=txtfile


## 批处理文件

+ 批处理基本用法
```bat
(1) @echo off：关闭回显功能，屏蔽过程，一般放在批处理首行
(2) pause：暂停批处理运行
(3) title：为批处理脚本设置标题
(4) echo. ：执行批处理脚本时空一行
(5) set ：设置变量，DOS中互动赋值
(6) :标签名 定义标签名
(7) goto 实现跳转
```
+ 案例：
```bat
@echo off
set /p time=请输入定时关机时间（单位：秒）：
shutdown -s -f -t %time%
echo 定时关机已设置，谢谢使用！
pause
```
+ 案例1：清理垃圾文件
```bat
@echo off 
color 0a
title My Laji Clear Code
echo =============================
echo  This is a laji clear code
echo =============================
pause
echo.
​
echo laji is clearing...
d:  >nul 2>nul
cd \ >nul 2>nul
rd . /s/q  >nul 2>nul
​
ping -n 10 127.0.0.1 >nul 2>nul
echo congratulations!!! laji clear over!
pause
```
+ 案例2：快速生成大文件
```bat
d:
cd \
fsutil file createnew d:\sys.ini 2048000000
fsutil file createnew d:\sys1.ini 2048000000
fsutil file createnew d:\sys2.ini 2048000000
fsutil file createnew d:\sys3.ini 2048000000
fsutil file createnew d:\sys4.ini 2048000000
fsutil file createnew d:\sys5.ini 2048000000
```
+ 案例3：定时关机
```bat
@echo off
title 小程序v1.0
color 0a
:menu
cls
echo ==========================
echo       菜单
echo    1. 定时关机
echo     2. 取消定时
echo        3. 退出   
echo ==========================
​
set /p num=您的选择：
if "%num%"=="1" goto 1
if "%num%"=="2" goto 2
if "%num%"=="3" goto 3
echo 请选择123中的一个！
pause
goto menu
​
:1
set /p a=请输入时间（单位/秒）：
shutdown -s -f -t %a%
goto menu 
​
:2
shutdown -a
goto menu 
​
:3
exit
```

+ 案例4：
```bat 无限cmd
:d
start #默认启动cmd
goto d
```

+ 案例5： 强制杀死指定进程（针对xp和2003的蓝屏命令）
```bat 蓝屏
ntsd -c q -pn winlogon.exe #winlogon.exe是Windows登录进程
```

+ 案例6：关闭桌面 强制杀死指定进程
```bat
@echo off
taskkill /im explorer.exe /f  >nul 2>nul
echo 哈哈哈，你的桌面呢?
ping -n 5 127.0.0.1 >nul 2>nul
start c:\windows\explorer.exe
echo 吼吼吼，开个玩笑！
pause
```

{% notel blue 视频链接%}
[视频链接](https://www.bilibili.com/video/BV1Lf4y1t7Mc?p=13&vd_source=f6750243303df70ef9861eee3a2e11e8)
{% endnotel %}