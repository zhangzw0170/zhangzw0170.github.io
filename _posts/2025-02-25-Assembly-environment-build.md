---
title: 汇编语言编程环境搭建（DOSBox-0.74-3 + RadASM 2.2.1.6 + MASM32）
date: 2025-02-25 15:01:00 +0800
categories: [编程与算法, 汇编]
tags: [汇编语言, 环境搭建]
---

> 备注：当时的知乎 ID 为 **衍格**，原文发布在 [知乎](https://zhuanlan.zhihu.com/p/26223077747)

![封面](/assets/image/2025-02-25/cover.png)

## 更新日志

- 2025.03.17：增补了对于 DOSBox 中调试（debug）模式的相关操作
- 2025.03.17：增补了关于 DOSBox 分支版本 DOSBox-X 的相关介绍

## 前言

笔者在上微机原理课搭建汇编语言（Assembly）编程环境时踩了很多坑，在此写下自己的探索经验以供大家参考。这是笔者第一次写经验分享帖，如果对你有帮助，可以留下一个赞鼓励一下，希望你能在这篇教程中有所收获。

### 记号说明：

-   ./RadASM：RadASM 的安装目录
-   ./DOSBox：DOSBox 的安装目录
-   ./masm32：MASM32 SDK 的安装目录，**注意：在 ./RadASM 中有一个名为
    Masm 的文件夹，为避免混淆，后者将使用 ./RadASM/Masm 指代。**

## 一、准备工作：软件包下载与安装

### 1. RadASM：[RadASM assembler IDE](https://assembly.com.br/)

![RadASM 官网页面](/assets/image/2025-02-25/1.png)

其中必须下载 RadASM **软件本体**（第 1 个）和 RadASM **汇编包**（第 2 个，即 RadASM Assembly programming），视自身需要下载**语言支持包**（第 4 个，即 RadASM language pack，软件界面**原生支持中文**）和**帮助文档**（第 5 个，即 RadASM help file）。

由于软件包很小，所以不用担心网络问题导致下载耗时太长。

接下来我们开始安装 RadASM，**不建议在安装路径中出现中文**。首先解压软件本体 RadASM.zip ，然后将汇编包 Assembly.zip **解压后的文件**全部复制到 ./RadASM 中即可。

如果下载了语言包 RadLNG.zip 和帮助文档
RAHelp.zip，解压后会各自得到一个文件夹 Language 和
Help，将**这两个文件夹**复制到 ./RadASM 中即可。

### 2. DOSBox：[DOSBox, an x86 emulator with DOS](https://www.dosbox.com/)

![DOSBox 官网页面](/assets/image/2025-02-25/2.png)

下载好 .exe 后双击按指引安装即可，**不建议在安装路径中出现中文**。

### 3. MASM：[Download The MASM32 SDK](https://masm32.com/download.htm)

![MASM32 SDK 下载页面](/assets/image/2025-02-25/3.png)

点击图中红圈位置的绿色文本即可下载，下载后得到一个名为 install.exe 的文件，将该文件放进 ./RadASM 中。双击 install.exe，选择安装路径（**这里的安装路径不能自由指定，需要记住自己安装在哪一个盘中**），一路点击 OK，等待安装完成，**安装期间尽量不要切换窗口，否则可能导致安装卡死。**

![MASM32 安装](/assets/image/2025-02-25/4.png)

安装完成后，如果没有特殊需求，建议将 ./masm32 移动到 ./RadASM 下，即 ./RadASM/masm32，方便后续操作。

### 4. MASM缺失文件：[DOSBox_MASM/masm at master · xDarkLemon/DOSBox_MASM · GitHub](https://github.com/xDarkLemon/DOSBox_MASM/tree/master/masm)

共4个文件：MASM.EXE, LINK.EXE, debug.exe, exe2bin.exe。这里主要用到 debug.exe，用于补全之后安装的 masm32 的调试器。

## \*二、启用 RadASM 的中文语言包（第一步中的 Language pack）

![](/assets/image/2025-02-25/5.png)

![修改语言为中文](/assets/image/2025-02-25/6.png)

两个简体中文方案可根据需要自行选择。但此时只是软件界面为中文，程序本身还不能包含中文字符，可能出现乱码，解决方法如下：

![](/assets/image/2025-02-25/7.png)

![修复可能造成的乱码](/assets/image/2025-02-25/8.png)

配置后即可在程序中输入中文注释。

## 三、RadASM的配置

### 1. 修改 ./RadASM/masm.ini

进入 ./RadASM，用记事本打开配置文件 masm.ini，按 Ctrl+F 查找 **\[Dos App\]**，在下图红框内部分输入**DOSBox.exe 的绝对路径**。

![记事本编辑 ./RadASM/masm.ini](/assets/image/2025-02-25/9.png)

绝对路径获取方法如下，在末尾加上 DosBox.exe （该文件夹中唯一一个 .exe 文件的名称）即可。

![绝对路径获取](/assets/image/2025-02-25/10.png)

### 2. 在 RadASM 中关联 masm32

在 选项 - 编程语言 中打开如下界面，选择 masm.ini （即刚才加入 DOSBox 路径的文件）并点击"打开"即可。

![](/assets/image/2025-02-25/11.png)

![为 RadASM 指定 masm 路径](/assets/image/2025-02-25/12.png)

效果：

![设置完成](/assets/image/2025-02-25/13.png)

接下来在 RadASM 中设置 masm32 的路径：

![](/assets/image/2025-02-25/14.png)

下图中，红圈内为 masm32 的安装目录（绝对路径），绿圈表明 \$A 指代 masm32 的安装目录 ./masm32，蓝圈内**特别注意对应路径下的bin, help, include, lib的字母大小写问题！！！**

![](/assets/image/2025-02-25/15.png)

### 3. 配置系统环境变量

在开始菜单旁边的搜索栏中输入"环境变量"，选择"编辑系统环境变量"，点击"环境变量"。

![](/assets/image/2025-02-25/16.png)

在用户变量中加入键值对 include ：
./masm32/include（**绝对路径**，下同），lib： ./masm32/lib，path：./masm32。如果对应的变量已存在，双击进入后点击新建，在后面附加即可。

![新建用户变量（创建键值对）](/assets/image/2025-02-25/17.png)

![编辑环境变量（加入新的键值对）](/assets/image/2025-02-25/18.png)

## 四、补全 masm32 和 DOSBox 的执行环境与调试环境

这一步很简单，首先将第一步得到的 debug.exe 放进 ./masm32/bin 中，然后将 ./masm32/bin 中的 link16.exe 复制粘贴到该目录，并将副本改名为 DOSLNK.EXE 即可，此处不再附图。

### 2025.03.17 更新

修改 DOSBox 的配置文件（双击 ./DOSBox/DOSBox 0.74-3 Options.bat 打开），在该文件末尾加入如下内容（个人建议将 debug.exe 放到 ./DOSBox 中，以免之后需要调整时找不到合适的路径）：

> MOUNT D 这里填debug.exe所在的目录路径

![](/assets/image/2025-02-25/19.png)

此处的 D 可以换成除了 A, B, C, Z 外的其他盘符，**尤其是不能用 C 和 Z**，因为用 C 会导致 RadASM 调试时无法进入当前文件，用 Z 会和 DOSBox 本身的工作目录冲突。

## \*五、调整 DOSBox 的显示窗口参数

本步不是必要的，只是笔者在使用的时候感觉 DOSBox 的字体太小了，索性再去找些资料改一下界面大小。

打开文件夹 C:\\Users\\**此处替换为自己的计算机名称**\\AppData **（该文件夹默认隐藏）**\\Local\\DOSBox，里面有一个名为 dosbox-版本号.conf 的配置文件，用记事本（或VS Code）打开后找到 windowresolution，将其修改为适合自己电脑屏幕的分辨率即可，**注意图中的 x 是字母 X 的小写，不是Unicode中的乘号！**

如有需要，可将 output 从 surface 改为 opengl。

![](/assets/image/2025-02-25/20.png)

## 六、测试环境搭建是否成功

示例程序：（来源：《微机原理、汇编语言与接口技术》人民邮电出版社，第 4 章；稍作修改）

```nasm
DATA    SEGMENT
STRING  DB  'Hello world!', 0DH, 0AH, '$'
DATA    ENDS

STACK   SEGMENT     STACK
    DB  128 DUP (?)
STACK   ENDS

CODE    SEGMENT
ASSUME  CS:CODE,  DS:DATA,  SS:STACK
BEGIN:  MOV AX, DATA
   MOV  DS, AX
   MOV  AH, 09H
   LEA  DX, STRING
   INT  21H
   MOV  AH, 4CH
   INT  21H
CODE    ENDS
        END     BEGIN
```

预期行为：打开 DOSBox 的命令行窗口后，打印 Hello, world!

新建工程并选择 Dos App，指定工程名称后复制上述代码到代码框内，并按
Ctrl+F5 一次性进行编译，连接与运行。

![注意文件名不能超过 8 个 ASCII 字符](/assets/image/2025-02-25/21.png)

下图所示即为大功告成。

![大功告成](/assets/image/2025-02-25/22.png)

若需要进入调试模式，在可输入如下命令：

```pwsh
D:
debug C:\HELLO.EXE
```

效果如下图：

![成功进入调试模式](/assets/image/2025-02-25/23.png)

之后即可根据调试模式下的指令使用方法进行愉快的调试了（）

## 七、总结

至此汇编环境的搭建大功告成，享受汇编之旅吧！

如果感觉 DOSBox 的功能不足以满足自己的需要，比如**需要以 TTF 形式显示终端内容、更好的简体中文/繁体中文支持、运行时级别的CPU型号与DOS版本切换**，可以考虑将 DOSBox 更换为其分支版本 DOSBox-X。该项目最近一次更新为 2025/02/01，其使用方法和 DOSBox 基本相同，可以获得更好的体验。

DOSBox-X 项目地址：[joncampbell123/dosbox-x: DOSBox-X fork of the DOSBox
project](https://github.com/joncampbell123/dosbox-x)

官网介绍：[DOSBox-X - Accurate DOS emulation
for Windows, Linux, macOS, and
DOS](https://dosbox-x.com/)

如果需要 DOSBox-X 的教程，可以在评论区催更\~

## 八、参考文章

[【汇编语言】汇编实验IDE（集成开发环境）：RadASM的安装和使用说明-CSDN博客](https://blog.csdn.net/weixin_42929607/article/details/105081313)

[DOSBox+MASM汇编环境的安装和使用 - 知乎](https://zhuanlan.zhihu.com/p/411988671)

[MASM32汇编环境搭建教程-CSDN博客](https://blog.csdn.net/ir_mouse/article/details/134224771)

[RadASM原版下载、汉化、配置、解决中文乱码傻瓜式教程-CSDN博客](https://blog.csdn.net/2301_79113923/article/details/135665466)

[radasm的汇编语言操作入门 - nyc1893 - 博客园](https://www.cnblogs.com/nyc1893/archive/2011/08/05/2129058.html)

[dosbox下载并配置masm环境变量的方法_dosbox link、masm下载-CSDN博客](https://blog.csdn.net/weixin_41944412/article/details/81006594)

[调整DOSBox的窗口大小：跨过三连坑_windowresolution-CSDN博客](https://blog.csdn.net/sxhelijian/article/details/109397338)

[汇编语言消除连接程序时的警告：No stack segment_no stack segment 怎么解决-CSDN博客](https://blog.csdn.net/qq_43371810/article/details/103720520)

[DOSBOX与DEBUG的使用方法及命令_dosbox debug-CSDN博客](https://blog.csdn.net/fencecat/article/details/113600131)
