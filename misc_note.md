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
