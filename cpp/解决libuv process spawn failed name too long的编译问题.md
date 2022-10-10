## 目录
- [目录](#目录)
- [运行环境：](#运行环境)
- [编译问题：](#编译问题)
- [问题的起源](#问题的起源)
- [检查命令行参数](#检查命令行参数)
- [解决方法](#解决方法)
  - [一、CMake工程中方法](#一cmake工程中方法)
  - [二、CMake与C++的混合改动](#二cmake与c的混合改动)
    - [C++文件：](#c文件)
    - [CMake文件：](#cmake文件)
  - [三、改动工程目录结构](#三改动工程目录结构)

## 运行环境：
> Windows 10 专业版  
> Visual Studio 2019 Community/Professional  
> C++工程为一个较大项目，包含SDK在内有90多个库，总计达6位数行数代码

## 编译问题：

    AutoMoc subprocess error
    ------------------------
    The moc process failed to compile
    "SRC:/XXX.h"
    into
    "BIN:/XXX.cpp"
    libuv process spawn failed: name too long

## 问题的起源
这是由于CreateProcess函数的最长命令行长度为32767字符。这个限制来自于UNICODE_STRING结构。而Windows上C++编译是通过使用msbuild.exe命令行进行，由于参数过长，导致CreateProcess失败，致使编译失败。

## 检查命令行参数
打开Visual Studio，在编译报错的库上**鼠标右键**打开**属性**，然后依次打开**配置属性**、**C++**、**命令行**，即看到命令行的所有参数。

可以将这些命令行复制到Visual Code的工具，全选即可看到字符数目。

## 解决方法

### 一、CMake工程中方法

1. 检查并减少该项目include_directories中的重复路径，或没有实际引用的路径
2. 如果include_directories中有引用到了相对路径，需要将这些相对路径替换成**绝对路径**。这是由于cmake执行[include_directories](https://cmake.org/cmake/help/latest/command/include_directories.html)时，如果判断到是相对路径，则会在路径前自行拼接当前cmake的工程所在的路径，最终使C++编译的附加包含路径变长。

### 二、CMake与C++的混合改动

当include_directories中的某些路径，所包含的头文件比较少时，可直接在C++项目中，将所有#include该头文件的地方，在左边拼接上一层该头文件所在的目录，此时即可将cmake中include_directories的这行路径去掉。代码示例如下：

#### C++文件：
~~`#include "Prerequisites.h"`~~  
**`#include "Config/Prerequisites.h"`**

#### CMake文件：
~~`include_diretories(${PROJECT_COMMON_DIR}/Core/Config)`~~  
**`include_diretories(${PROJECT_COMMON_DIR}/Core)`**

> 这种改动，可以通过编写一个脚本来批量替换

### 三、改动工程目录结构

在30000多字符的附加包含路径中，具有大多重复的前缀路径。此时，可以将**总的工程目录往更上一级文件夹（或直至盘符目录）移动**，以减少重复的前缀路径的长度。

检查命令行参数，查找到某个包含最多的目录，将该目录适宜地往上一级文件夹移动，以达到缩短包含路径的目的。