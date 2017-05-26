---
title: Windbg  
date: 2017-02-08 13:04:19
tags: Windbg
---
## 命令(加载调试器)
```
命令：
.loadby sos mscorwks  .Net 3.5版本及以下
.loadby sos clr .Net 4.0
.loadc:\windows\microsoft.net\framework\v2.0.50727\sos.dll
说明：
如果服务器的.Net版本与本机不匹配需要服务器版本的mscordacwks.dll文件，如下：.sympath=
``` 
<!--more-->
##  基本操作指令
```
!help    sos指令帮助
!threads  显示所有线程
!threadpool(!tp)   显示程序池信息
~54s   查看54号线程
kb   显示当前线程的callstack
~*e !clrstack  看所有线程的堆栈信息
!dumpheap  显示托管堆的信息
!clrstack  显示调用栈
!dumpstackobjects 打印当前thread的stack中保存的所有托管的object
!dumpclass  打印出指定类型信息址上的类型定义
!eeheap   列举出当前进程中managedheap的统计信息
!dumpobj(!do)  显示一个对象的内容
!dumparray 显示数组
!syncblk   显示同步块
!runaway  显示线程cpu时间
!gcroot    跟踪对象内存引用
!gchandles   打印出当前所有的GCHandle统计信息
!objsize    打印出指定object
!pe    打印异常
vertarget  查看系统运行时间
```
 
## 内存泄露常用指令
```
 !dumpheap–stat  检查当前所有托管类型的统计信息
!dumpheap –mt 查看函数表地址中的各个对象信息
!do   查看某个对象的具体信息
!da   查看数组信息
!gcroot  查看哪个root了某个对象
 
使用例子：
!dumpheap –mt 79124418  79124418是指某个函数表的地址。
!do 0aca1000  查看0aca1000地址中的对象
!gcroot 0aca1000  查看谁root 了0aca1000对象
```
## 线程Hang住常用指令
```
 !threads   查看托管经程的统计信息
 !tp(threadpool)   查看一共设有多少个线程在运行
 !runawayf    查看各个线程的CPU使用情况   
~*e!clrstack 查看各个线程都在做什么
!synblk 查看AwareLock的Lock方法
   
 不常用指令：
 !ip2md 找到对应的module信息，后面参数为Callback中的EIP地址
 lmvm    查看DLL/EXE文件信息，参数为某个dll文件名称
 !savemodule   保存DLL到本场
   
 使用例子：
 !ip2md 0x09d30bao
 lmvmcommonobjects
 !savemodule 081f0000 d:\xiongli\commandobject.dll
``` 
## 内存指令：
```
 S    可以搜索内存
 r   显示寄存器的信息
 d    显示内存地址上的值
 e    命令修改内存地址上的值
 !address   命令显示某一地址上的页信息
 使用例子：
 1）使用d命令显示esp寄存器指向的内存，默认为byte
   d  esp
 2）用dd命令直接指定054efc14地址，第二个d表示用DWORD格式
 dd 054efc14
 3）用ed命令把054efc14地址上的值修改成11112222
 ed 054efc14  11112222
 4）再次用dd命令显示054efc14地址上的值，后面的L4参数表示长度为4个DWORD
 dd 054efc14  L4
 5）在内存中搜索sina.com
 s –u  0012ff40 L?8000000 “sina.com”
```
## 其他常用指令：
```
 lmf   列出当前进程中加载的所有DLL文件和对应的路径
 ~    显示线程信息和不同线程之间切换的值
 k    显示当前线程的callstack
 ~*k   结合~和k命令，来显示所有线程的callstack
```
 
 
## 生成DUMP包脚本
``` 

 
Set objShell =CreateObject("Wscript.Shell")
Dim InputPid
InputPid = InputBox("请输入Pid")
objCommand1 = """D:\ProgramFiles\Debugging Tools for Windows (x86)\adplus.vbs""" &" -hang -o d:\dump -quiet -p " & InputPid
objShell.Run(objCommand1)
 
保存成.vbs的文件
点开运行后，输入对应的进程PID，即可抓下当前进程的包。
```

## 附录
```
.loadby sos mscorwks	.Net 3.5版本及以下
.loadby sos clr	.Net 4.0
.load c:\windows\microsoft.net\framework\v2.0.50727\sos.dll	加载SOS文件
.sympath=	如果服务器的.Net版本与本机不匹配需要服务器版本的mscordacwks.dll文件
!help	sos指令帮助
vertarget	查看系统运行时间
!threads	显示所有线程
!threadpool(!tp)	显示程序池信息
~54s  	查看54号线程
kb 	显示当前线程的callstack
~*e !clrstack  	看所有线程的堆栈信息
!syncblk	显示同步块
!runaway	显示线程cpu时间
!pe	打印异常
!dumpheap	显示托管堆的信息
!dumpheap –stat	检查当前所有托管类型的统计信息
!dumpheap –mt 	查看函数表地址中的各个对象信息	!dumpheap –mt 79124418   79124418是指某个函数表的地址。
!clrstack	显示调用栈
!dumpstackobjects	打印当前thread的stack中保存的所有托管的object
!dumpclass	打印出指定类型信息址上的类型定义
!eeheap	列举出当前进程中managed heap的统计信息
!dumpobj(!do)	显示一个对象的内容	!do 0aca1000 查看0aca1000地址中的对象
!dumparray(!da)	显示数组
!gcroot	跟踪对象内存引用	!gcroot 0aca1000   查看谁root 了0aca1000对象
!gchandles	打印出当前所有的GC Handle统计信息
!objsize	打印出指定object
!ip2md	找到对应的module信息，后面参数为Callback中的EIP地址	!ip2md 0x09d30bao
lmvm	查看DLL/EXE文件信息，参数为某个dll文件名称	lmvm commonobjects
!savemodule	保存DLL到本场	!savemodule 081f0000 d:\xiongli\commandobject.dll
S	可以搜索内存	在内存中搜索sina.com: s  –u  0012ff40 L?8000000 “sina.com”
r	显示寄存器的信息
d	显示内存地址上的值	使用d命令显示esp寄存器指向的内存，默认为byte: d  esp
用dd命令直接指定054efc14地址，第二个d表示用DWORD格式: dd  054efc14
e	命令修改内存地址上的值	用ed命令把054efc14地址上的值修改成11112222: ed  054efc14  11112222
!address	命令显示某一地址上的页信息
lmf	列出当前进程中加载的所有DLL文件和对应的路径
~	显示线程信息和不同线程之间切换的值
k	显示当前线程的call stack
~*k	结合~和k命令，来显示所有线程的callstack
```