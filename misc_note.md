# 笔记

## 2021/9/20 设置cmake语言版本

1. 设置CMAKE_CXX_STANDARD变量，后面跟一个数字作为设置的版本，不需要再加上c++。
同时可以设置CMAKE_CXX_STANDARD_REQIUIRED变量，后面跟True代表版本必须通过CMAKE_CXX_STANDARD来确定，如果不设置这个变量，那么CMAKE_CXX_STANDARD设置的就是首选版本。
若是c语言的话，则命令相应的为CMAKE_C_STANDARD和CMAKE_C_STANDARD_REQIUIRED。
示例：
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQIUIRED True)
注意：必须要在add_executable命令之前使用上述语句设置版本，否则会失效。

2. 使用target_compile_features命令也可以达到同样效果，示例:
target_compile_features(demo PUBLIC cxx_std_14)
注意该命令是要在add_executable之后。

cmake官方帮助文档：<https://cmake.org/cmake/help/latest/index.html>

## 2021/9/22 设置minicom为16进制显示

命令：minicom -H。
要尽量养成看帮助文档的习惯，帮助里都有写

## 2021/10/14 gcc参数 -x 作用

语句: gcc -x language filename  
该参数为设置后面的文件所使用的语言，如果不设置的话gcc会通过文件后缀识别
  
language可以为：  

    c, objective-c, c-header, c++, cpp-output, assembler, assembler-with-cpp  

若要取消设置 language 填 none

## 2021/10/14 makefile判断当前系统环境

    ifeq ($(OS), Windows_NT)
        #windows
    else
        #linux
    endif

## 2021/10/14(2) Windows下makefile删除文件命令

dos命令: del *filename*  
例: del $(BUILD_DIR) $(TARGET_DIR)

## 2021/10/14(3) Windows下makefile检查文件夹是否存在

dos命令: if not exist *filename* mkdir *filename*  
例: if not exist $(BUILD_DIR) mkdir $(BUILD_DIR)

## 2021/10/15 Windows下makefile获取当前路径

dos命令: echo %cd%  
例: TOP_DIR = $(shell echo %cd%)

## 2021/10/15(2) ubuntu卸载软件

shell命令: dpkg --purge <u>package</u>
用 --purge 可以删除一切，包括setting和配置文件

## 2021/10/20 GCC -mthumb 参数意思

*-mthumb* 意思是生成目标文件为Thumb指令  
关于编译器参数可以查看官网文档: <https://gcc.gnu.org/onlinedocs/gcc-11.2.0/gcc/#toc-GCC-Command-Options>

## 2021/10/20(2) 何为thumb指令

thumb指令为arm指令的子集，1代thumb指令为16位宽，代码密度高，但是指令集不全，需要搭配arm指令使用，而2代thumb指令为32位宽，性能与arm指令相当，而且保留了一代指令的简介特性(无法确定是否不需要在搭配arm指令使用)。  
参考资料: <https://blog.csdn.net/zlmm741/article/details/105209734>

## 2021/10/20(3) GCC -mcpu 参数意思

命令: -mcpu=*name*  
指定何种处理器，name为指定的处理器,不能与-march(指定何种架构)同时使用。若要查看支持的处理器列表，使用命令: gcc -mcpu=list

## 2021/10/20(4) GCC -mfpu 参数意思

命令: -mfpu=*name*  
指定何种fpu架构，即可用的浮点硬件。若要查看支持的fpu列表，使用命令: gcc -mfpu=list

## 2021/10/20(5) GCC -mfloat-abi 参数意思

命令: -mfloat-abi=*value*  
是否使用硬件浮点或软件浮点。value可为: soft 使用软件浮点; softfp 软硬件浮点联动; hard 使用硬件浮点

## 2021/10/20(6) GCC -fdata-sections -ffunction-sections 参数意思

对于程序中未使用的变量或者函数，在链接阶段连接器将会去除，减小可执行文件的大小

## 2021/10/25 GCC -c 参数意思

只编译不链接

## 2021/10/25(2) Makefile vpath 用法

命令: vpath *pattern* *path*  
pattern为匹配模式，path为需要查找的路径  
例: vpath %.c ./  该命令就是在将当前文件夹下的后缀为c的文件添加到环境变量VPATH中  
Makefile官方文档: <https://www.gnu.org/software/make/manual/>

## 2021/10/27 GCC -Wa,-adlms=*filename* 参数意思

首先参数 -Wa,option 是将option参数传递给汇编程序，因此-adlms=*filename*实际是汇编程序的参数，参数在不在gcc手册上，而是在 <https://sourceware.org/binutils/docs/as/index.html#SEC_Contents>，也可以通过本地GUN工具链的help功能找到，例如: arm-none-eabi-as --help  

-a 是指请求高级、汇编、符号列表，具体意思也不太清楚，后面的dlms是组合在一起的参数，分开来的话就是 -ad: 列表中省略调试指令的选项；-al: 请求输出程序汇编列表；-am: 请求包含宏拓展；-as: 请求输出符号列表。后面的filenames是输出列表文件的名称，生成的其实就是.lst文件。

## 2021/10/27(2) GCC -Wl,-Map=*filename*,--cref,--gc-sections,-u,_printf_float,-lc,-lm,-lnosys,-lrdimon 参数意思

首先参数 -Wl,option 是将option参数传递给链接程序，因此 -Wl 后面的参数都是链接程序的参数，该参数信息可在 <https://ftp.gnu.org/old-gnu/Manuals/ld-2.9.1/html_mono/ld.html#SEC5> 找到，也可以通过本地GUN工具链的help功能找到，例如: arm-none-eabi-ld --help  

-Map=*filename* 是将链接映射输出到指定的文件中，--cref 是创建交叉引用表，如果已经在输出连接映射的文件，则交叉引用表也会输出到该文件中，否则输出到标准输出上，-u *symbol* 强制符号作为未定义符号输入到输出文件中，这样会触发标准库中附加模块的链接。

## 2022/1/6 linux /etc/profile 文件作用

操作系统为用户定制环境的一个文件，开机的时候会读取一次该文件，设置环境变量。

扩展:
    1. /etc/bashrc文件在打开bash shell时候执行一次。
    2. ~/.profile 作用同上，为具体某个用户的配置文件
    3. ~/.bashrc 作用同上，为具体某个用户的配置文件

## 2022/1/6(2) source指令作用

格式: source **filename**

作用: 读取一次文件并执行，source在当前bash环境下执行命令

## 2022/1/6(3) linux nmcli 命令使用

无线查询: nmcli device wifi

无线连接: nmcli device wifi connect **ssid** password **password**

## 2021/1/7 linux du 指令

du指令全称，disk usage

常用指令:

* du -csh **obj** 统计某一目录下的文件及文件夹大小，-c 可求出数据总和，-s 为对每个参数统计磁盘使用量，-h为人类可读的方式显示，-d **depth** 为指定搜索的深度

## 2022/1/9 命令行查看ubuntu版本

命令: lsb_release -a

## 2022/1/9(2) 解压缩7z压缩包

命令: 7zr x **filename**，x为按压缩包原本目录解压缩

## 2022/1/9(3) ssh登录 WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED

最近换了一个开发帮，ssh登录提示以上错误，经搜索提示公钥不对，将.ssh/known_hosts删除即可，当然最好使用ssh-keygen -R **ip**，清除域名

## 2022/1/30 ssh登录root用户时，即使密码正确也无法登录

1. 保证 /etc/ssh/sshd_config 配置文件中有"PermitRootLogin yes"，以开启root登录许可，然后使用"service ssh restart"来重启服务
2. 其次要保证root用户设置了密码，也就是要登录到root用户后用passwd命令设置密码，然后重启

## 2022/1/30(2) nfs服务无法开启

开启nfs提示错误

```text
Starting nfs-kernel-server (via systemctl): nfs-kernel-server.serviceA dependency job for nfs-server.service failed. See 'journalctl -xe' for details.
```

使用journal查看日志提示

```text
unknown filesystem type 'nfsd'
```

原因是内核不完整，需要重新配置内核，要勾上NFS client support、NFS server support和IP/TCP中的DHCP等，如下图

![](./images/2022-01-30%2000-46-46%20的屏幕截图.png)

![](images/2022-01-30%2023-56-27%20的屏幕截图.png)

注: 在Edge-V这块开发板直接更新image并没有用

## 2022/1/30(3) 添加和删除用户

执行以上操作需要登录到root用户，不是通过sudo su登录，而是登出以后以root用户名登录。

* 添加用户 adduser **user name**
* 删除用户 deluser **user name**，如果还要删除文档，则为 deluser --remove-home **user name**

## 2022/1/30(4) 使用 adduser 添加的用户不能使用 sudo su

需要修改/etc/sudoers 加入

```text
admin ALL=(ALL) ALL
```

admin就是用户名，然后重启

## 2022/1/30(5) ubuntu 修改计算机名

命令: hostnamectl set-hostname **hostname**

## 2022/1/30(6) 使用netplan设置静态ip

需要安装 netplan 和 netplan.io

然后需要添加一个/etc/netplan/*.yaml文件，加入以下内容

```text
network:
    ethernets:
        eth0:
            dhcp4: no
            addresses: [192.168.1.2/24]
            gateway4: 192.168.1.1
```

然后使用命令 netplan apply 启动配置

## 2022/1/30(7) 开发板ubuntu不能补全或者在sudo命令后不能补全

需要安装 bash-completion，然后将电脑本地的 /etc/bash_completion.d/ 文件夹及内容全部复制到开发板的同目录下，最后重启。
