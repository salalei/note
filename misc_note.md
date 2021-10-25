# 杂项笔记

## 2021/10/14 gcc参数 -x 作用

语句: gcc -x language filename

该参数为设置后面的文件所使用的语言，如果不设置的话gcc会通过文件后缀识别。

language可以为：

    c, objective-c, c-header, c++, cpp-output, assembler, assembler-with-cpp

若要取消设置 language 填 none

## 2021/10/14 makefile判断当前系统环境

    ifeq ($(OS), Windows_NT)
        #windows
    else
        #linux
    endif

## 2021/10/14 Windows下makefile删除文件命令

dos命令: del *filename*

例: del $(BUILD_DIR) $(TARGET_DIR)

## 2021/10/14 Windows下makefile检查文件夹是否存在

dos命令: if not exist *filename* mkdir *filename*

例: if not exist $(BUILD_DIR) mkdir $(BUILD_DIR)

## 2021/10/15 Windows下makefile获取当前路径

dos命令: echo %cd%

例: TOP_DIR = $(shell echo %cd%)

## 2021/10/20 GCC -mthumb 参数意思

*-mthumb* 意思是生成目标文件为Thumb指令

关于编译器参数可以查看keil官网文档: <https://www.keil.com/support/man/docs/armclang_ref/armclang_ref_chr1392632801932.htm>

## 2021/10/20(1) 何为thumb指令

thumb指令为arm指令的子集，1代thumb指令为16位宽，代码密度高，但是指令集不全，需要搭配arm指令使用，而2代thumb指令为32位宽，性能与arm指令相当，而且保留了一代指令的简介特性(无法确定是否不需要在搭配arm指令使用)。

参考资料: <https://blog.csdn.net/zlmm741/article/details/105209734>

## 2021/10/20(2) GCC -mcpu 参数意思

命令: -mcpu=*name*

指定何种处理器，name为指定的处理器,不能与-march(指定何种架构)同时使用。若要查看支持的处理器列表，使用命令: gcc -mcpu=list

## 2021/10/20(3) GCC -mfpu 参数意思

命令: -mfpu=*name*

指定何种fpu架构，即可用的浮点硬件。若要查看支持的fpu列表，使用命令: gcc -mfpu=list

## 2021/10/20(4) GCC -mfloat-abi 参数意思

命令: -mfloat-abi=*value*

是否使用硬件浮点或软件浮点。value可为: soft 使用软件浮点; softfp 软硬件浮点联动; hard 使用硬件浮点

## 2021/10/20(5) GCC -fdata-sections -ffunction-sections 参数意思

对于程序中未使用的变量或者函数，在链接阶段连接器将会去除，减小可执行文件的大小

## 2021/10/25 GCC -c 参数意思

只编译不链接

## 2021/10/25 Makefile vpath 用法

命令: vpath *pattern* *path*

pattern为匹配模式，path为需要查找的路径

例: vpath %.c ./  该命令就是在将当前文件夹下的后缀为c的文件添加到环境变量VPATH中

Makefile官方文档: <https://www.gnu.org/software/make/manual/>
