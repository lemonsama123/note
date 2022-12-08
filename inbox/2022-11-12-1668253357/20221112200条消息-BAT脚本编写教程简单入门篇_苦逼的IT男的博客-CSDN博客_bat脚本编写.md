---
title: (200条消息) BAT脚本编写教程简单入门篇_苦逼的IT男的博客-CSDN博客_bat脚本编写
url: https://blog.csdn.net/daoming1112/article/details/77197558
clipped_at: 2022-11-12 19:42:37
category: default
tags: 
 - 无
---


# (200条消息) BAT脚本编写教程简单入门篇_苦逼的IT男的博客-CSDN博客_bat脚本编写

**批处理文件最常用的几个命令：**

echo表示显示此命令后的字符 

echo on  表示在此语句后所有运行的命令都显示命令行本身   
echo off 表示在此语句后所有运行的命令都不显示命令行本身  
@与echo off相像，但它是加在每个命令行的最前面，表示运行时不显示这一行的命令行（只能影响当前行）。  
call 调用另一个批处理文件（如果不用call而直接调用别的批处理文件，那么执行完那个批处理文件后将无法返回当前文件并执行当前文件的后续命令）。  
pause 运行此句会暂停批处理的执行并在屏幕上显示Press any key to continue...的提示，等待用户按任意键后继续  
rem 表示此命令后的字符为注释，不执行。

title BAT的标题

cls 清除屏幕

  

**开始例子：**

```bash
<span style="font-family:SimSun;font-size:14px;">@ECHO OFF
TITLE BAT脚本例子1
echo -----------枚举C盘目录下所有文件-----------
echo=
echo=
dir c:\*.*
rem 输出到文本文件
dir c:\*.* > example1.txt
echo=
echo=
echo --------------------------------------------
PAUSE</span>
```

echo= 表示输出空白行，关于空白行的输出还有其他方式，具体可参考网址：

[http://blog.sina.com.cn/s/blog\_4b466ad00101dfqu.html](http://blog.sina.com.cn/s/blog_4b466ad00101dfqu.html)

若输入PAUSE>NUL 则表示暂停且不提示“按下任意键继续”。

  

**设置字体颜色和窗体大小：**  

设置字体颜色：COLOR 02 （0代表背景色，2代表前景色）

常用的颜色有以下值：0 黑色，1蓝色，2 绿色，3 浅绿色，4红色，5紫色，6黄色，7白色，8灰色，9浅蓝，A浅绿，B浅蓝色，C浅红色，D浅紫色，E浅黄色，F亮白色）。

设置窗体大小：MODE CON: COLS=宽度 LINES=高度

  

**文件夹简单操作：**

```bash
<span style="font-family:SimSun;font-size:14px;">@ECHO OFF
TITLE BAT脚本例子2
COLOR A
echo -----------BAT脚本例子2-----------
echo=
echo=
echo  当前工作路径为：%cd%
rem 输出文件目录的树形目录
TREE /f >tree_list.txt
rem CD切换不同盘符时候需要加上/d
CD /D C:\
echo  当前工作路径为：%cd%
DIR
rem 创建目录bat_example2
MD bat_example2
DIR
rem 拷贝目录 /s /e /y 说明：在复制文件的同时也复制空目录或子目录，如果目标路径已经有相同文件了，使用覆盖方式而不进行提示
Xcopy C:\bat_example2 D:\bat_example2  /s /e /y

rem 删除目录bat_example2
rem RD /Q /S bat_example2
rem DIR
echo=
echo=
echo --------------------------------------------
PAUSE</span>
```

关于文件夹的其他操作，可参考网址：[http://www.jb51.net/article/11313.htm](http://www.jb51.net/article/11313.htm)  
  

**文件操作**

```bash
<span style="font-family:SimSun;font-size:14px;">@ECHO OFF
TITLE BAT脚本例子3
COLOR A
echo -----------BAT脚本例子3-----------
echo=
echo=
TYPE tree_list1.txt
rem 复制（合并）文件 /Y 表示目标路径存在该文件则不提示直接覆盖
COPY /Y tree_list2.txt + tree_list3.txt C:\

DEL tree_list4.txt /f /s /q /a 
rem /f 表示强制删除文件 
rem /s表示子目录都要删除该文件 
rem /q表示无声，不提示 
rem /a根据属性选择要删除的文件 

rem 需要特别注意的是：move不能跨分区移动文件夹
MOVE example3 example3_1
echo=
echo=
echo --------------------------------------------
PAUSE</span>
```

**网络命令**

```bash
<span style="font-size:14px;">@ECHO OFF
TITLE BAT脚本例子4
COLOR A
echo -----------BAT脚本例子4-----------
echo= 
PING www.baidu.com
echo=
echo -----------------------------------
IPCONFIG
echo=
echo -----------------------------------
ARP 
echo=
echo -----------------------------------
PAUSE</span>
```

**系统相关**

```bash
<span style="font-size:14px;">@ECHO OFF
TITLE BAT脚本例子5
COLOR A
echo -----------BAT脚本例子5-----------
echo= 
echo -----------显示计算机用户-----------
NET USER
echo=
echo -----------显示进程列表-----------
TASKLIST
echo=
echo -----------------------------------
PAUSE</span>
```

  
       最后总结，其实BAT主要是运用DOS命令，所以只要掌握好DOS命令，使用BAT就轻松多了。 当然，BAT实际运用并不只是这些简单的命令，还有比较复杂的语法，将在下一篇做介绍。

  

参考网址：[http://www.jb51.net/article/49627.htm](http://www.jb51.net/article/49627.htm)