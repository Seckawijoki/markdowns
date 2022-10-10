## 说明

本文收集一些常用的bat搭配其它工具遍历文件，进行一些开发的辅助性操作或辅助性开发

### 目录
- [说明](#说明)
  - [目录](#目录)
  - [for循环index参数说明](#for循环index参数说明)
  - [dir的匹配符](#dir的匹配符)
  - [实例](#实例)
    - [1.基本遍历](#1基本遍历)
      - [递归遍历文件](#递归遍历文件)
      - [遍历一级文件夹下的文件](#遍历一级文件夹下的文件)
      - [遍历多种类型的文件](#遍历多种类型的文件)
      - [递归遍历文件夹](#递归遍历文件夹)
      - [遍历一级文件夹](#遍历一级文件夹)
      - [使用常量遍历](#使用常量遍历)
    - [2.文件操作](#2文件操作)
      - [获取相对路径](#获取相对路径)
      - [拼接文件名](#拼接文件名)
      - [获取文件时间](#获取文件时间)
    - [3.组合操作](#3组合操作)
      - [分布式调用](#分布式调用)
    - [4.搭配第三方工具](#4搭配第三方工具)
      - [TexturePacker将png转换成ktx](#texturepacker将png转换成ktx)
      - [批量进行svn mv操作](#批量进行svn-mv操作)
      - [对多个仓库进行svn cleanup](#对多个仓库进行svn-cleanup)
    - [5.辅助编写代码](#5辅助编写代码)
      - [编写xml文件](#编写xml文件)
      - [编写markdown中的无序列表](#编写markdown中的无序列表)
      - [生成WinMerger文件](#生成winmerger文件)
  - [VSCode使用一键输入代码进行快速编写](#vscode使用一键输入代码进行快速编写)

### for循环index参数说明
当我们编写for循环遍历文件时，遍历参数%%i的可变形式如下：
- %%~i - 删除引号(\")，扩充 %%i
- %%~fi - 将 %%i 扩充到一个完全合格的路径名
- %%~di - 仅将 %%i 扩充到一个驱动器号
- %%~pi - 仅将 %%i 扩充到一个路径
- %%~ni - 仅将 %%i 扩充到一个文件名
- %%~xi - 仅将 %%i 扩充到一个文件扩展名
- %%~si - 扩充的路径指含有短名
- %%~ai - 将 %%i 扩充到文件属性
- %%~ti - 将 %%i 扩充到文件的日期/时间
- %%~zi - 将 %%i 扩充到文件的大小

与路径相关的变式，可以是多种组合：
- %%~dpi 显示盘符与路径，即父路径
- %%~dpni 显示盘符、路径与文件名，不显示后缀
- %%~dpnxi 显示盘符、路径与文件名，显示后缀

### dir的匹配符
- `*` 包含文件和文件夹
- `*.*` 仅包含文件

### 实例

#### 1.基本遍历

##### 递归遍历文件
可使用`for /f`或`for /r`两种形式
`for /f`形式需要遍历其它目录时，可在前后添加`pushd`/`pop`进行，或在dir中的匹配符前输入目录路径
```bat
for /f %%i in ('dir /s /b *') do (
    echo %%i
)
```
`for /r`形式需要遍历其它目录时，只需将根目录参数`%cd%`替换成对应的目录路径或遍历即可
```bat
for /r %cd% %%i in (*) do (
    echo %%i
)
```

##### 遍历一级文件夹下的文件
此时%%i只输出文件名。dir中的参数`/a-d`是将文件夹排除在外。
```bat
for /f %%i in ('dir /b /a-d *') do (
    echo %%i
)
```

##### 遍历多种类型的文件
如同时递归bat、cpp、sh、lua这四种类型的文件：
```bat
for /f %%i in ('dir /s /b f:\batch_files\*.bat, f:\cpp_files\*.cpp, f:\bash_files\*.sh, f:\lua_files\*.lua') do ( 
  echo %%i 
)
```

##### 递归遍历文件夹
使用`/ad`表明只遍历文件夹
```bat
for /f %%i in ('dir /s /b /ad *') do (
    echo %%i
)
```

##### 遍历一级文件夹
使用`for /d`可直接遍历当前运行目录下的一级文件夹
```bat
for /d %%i in (*) do (
    echo %%i
)
```
使用`for /f`形式：
```bat
for /f %%i in ('dir /b /ad *') do (
    echo %%i
)
```

##### 使用常量遍历
较简单的一种方法，则是写定部分特定路径，使用`,`或`空格`分隔这些路径，直接通过`for`循环进行遍历。
这种方法可以经常使用。
```bat
@echo off
@REM 记得开启延迟变量
setlocal EnableDelayedExpansion
set aDirPaths=
set "aDirPaths=!aDirPaths!,ui\aiicons"
set "aDirPaths=!aDirPaths!,ui\items"
set "aDirPaths=!aDirPaths!,ui\rideicons"
set "aDirPaths=!aDirPaths!,ui\roleicons"
for %%i in (!aDirPaths!) do (
    echo %%i
)
```

#### 2.文件操作

##### 获取相对路径
> 替换符`=`前的变量仅能用`%%`包含。替换符前可写常量
```bat
@echo off
@REM 记得开启延迟变量
setlocal EnableDelayedExpansion
for /r %cd% %%i in (*.*) do (
    set szPath=%%i
    set szRelPath=!szPath:%cd%\=!
    echo !szRelPath!
)
```

##### 拼接文件名
将文件名拼接到一个变量中，然后输出到控制台，获取拼接结果。
如下，遍历bat使用`";"`拼接。
在拼接操作中，需将`set`关键词后的所有代码使用双引号包含。
> 同理，可以扩展到拼接绝对路径、相对路径，仅拼接文件、仅拼接文件夹等
```bat
@echo off
setlocal EnableDelayedExpansion
for /f %%i in ('dir /s /b *.*') do (
    set "a=!a!;%%~nxi"
)
@REM 去掉第一个;
set a=!a:~1!
echo !a!
```

##### 获取文件时间
```bat
@echo off
setlocal EnableDelayedExpansion
for /r %cd% %%i in (*) do (
    for %%a in (%%i) do set fileDate=%%~ta
    echo %%i !fileDate!
)
```

#### 3.组合操作
##### 分布式调用
查找工程中存在的tolua.bat，并调用
```bat
for /f %%i in ('dir /s /b tolua.bat') do (
  pushd %%~dpi
  call tolua.bat
  popd
)
```

#### 4.搭配第三方工具

##### TexturePacker将png转换成ktx
递归遍历png文件，使用TexturePacker进行ktx和pvr的转换
```bat
@echo off
setlocal EnableDelayedExpansion
@REM 配置第三方工具TexturePacker的路径
set PATH_TEXTURE_PACKER=D:\TexturePacker\bin\TexturePacker.exe
set "szParams=!szParams! --format xml"
set "szParams=!szParams! --max-size 2048"
set "szParams=!szParams! --shape-padding 2"
set "szParams=!szParams! --border-padding 2"
set "szParams=!szParams! --disable-rotation"
set "szParams=!szParams! --algorithm MaxRects"
set "szParams=!szParams! --no-trim"
set "szParams=!szParams! --dither-fs-alpha"
set "aKtxParams=!szParams! --opt RGBA8888"
@REM 遍历当前目录存在的文件夹，调用自定义函数进行转换
for /d %%i in (*) do (
    echo %%i
    call :function_transform_texture_set "%%i"
)
goto :end
@REM -----------------------------------------------------------------------
@REM 将png转换成ktx的功能封装成一个函数
:function_transform_texture_set
if "%~1" == "" goto :eof
if exist %~1.png del /q %~1.png
if exist %~1.xml del /q %~1.xml
@REM 检查空文件夹
set szCmdOutput=
for /f "delims=" %%a in ('dir /a /b %~1') do (
    set "szCmdOutput=%%a"
)
if "!szCmdOutput!" == "" (
    echo %~1 is empty
    rd /q %~1
    del /q %~1.ktx
    del /q %~1_alpha.ktx
    goto :eof
)
@REM ktx
%PATH_TEXTURE_PACKER%  %~1 !aKtxParams! --sheet %~1.png --data %~1.xml
goto :eof
@REM -----------------------------------------------------------------------
:end
```

##### 批量进行svn mv操作
批量进行svn mv操作，能保留svn的提交记录。
如下，将editor下所有的头文件和cpp文件，迁移到ui文件夹下
```bat
@echo off
setlocal EnableDelayedExpansion
for /f %%i in ('dir /b ui\*.*') do (
  set szSrcPurePath=editor\%%~ni
  set szDstPurePath=ui\%%~ni
  if exist "!szSrcPurePath!.h" svn mv !szSrcPurePath!.h !szDstPurePath!.h
  if exist "!szSrcPurePath!.cpp" svn mv !szSrcPurePath!.cpp !szDstPurePath!.cpp
)
```

##### 对多个仓库进行svn cleanup
如某台打包机，配置了多个不同的仓库，而且有进行经常性的拉去代码和编译工作。在硬盘有可能出现不足时，可在jenkins上配置一份svn cleanup清理缓存，来定期减少仓库的总大小
```bat
@echo off
setlocal EnableDelayedExpansion
set aEnvPaths=
set "aEnvPaths=!aEnvPaths! C:\env1develop"
set "aEnvPaths=!aEnvPaths! C:\env1debug"
set "aEnvPaths=!aEnvPaths! C:\env1release"
set aEnvPaths=!aEnvPaths:~1!
for %%p in (!aEnvPaths!) do (
  svn cleanup %%p --include-externals
  svn cleanup %%p --vacuum-pristines --include-externals
)
```

#### 5.辅助编写代码
##### 编写xml文件
例如，递归遍历将多个文件夹中的png文件，用相对路径写入Qt的qrc配置文件中
> **注意**：qrc实际为xml文件，语法上存在大量的"<"、">"符号，写入文件时，需要添加"^"进行转义
```bat
@echo off
setlocal EnableDelayedExpansion
@REM 设置需要遍历的所有文件夹
set aDirPaths=
set "aDirPaths=!aDirPaths!,ui\aiicons"
set "aDirPaths=!aDirPaths!,ui\items"
set "aDirPaths=!aDirPaths!,ui\rideicons"
set "aDirPaths=!aDirPaths!,ui\roleicons"
set aDirPaths=!aDirPaths:~1!
set fileOutput=res.qrc
if exist "!fileOutput!" del /q "!fileOutput!"
@REM 第一次写入文件使用">"，写入xml的标签头
(
  echo ^<RCC^>
  echo     ^<qresource prefix="/res"^>  
) > "!fileOutput!"
@REM 遍历文件写入
for %%p in (!aDirPaths!) do (
  set szDirPath=%%p
  @REM 将Windows的文件分隔符"\"替换成正斜杠"/"
  set szQrcPrefix=!szDirPath:\=/!
  for /f %%i in ('dir /b !szDirPath!\*.png') do (
    @REM 写入配置，并增加tab以适配格式
    echo         ^<file^>!szQrcPrefix!/%%i^</file^> >> "!fileOutput!"
  )
)
@REM 写入xml的标签尾
(  
  echo     ^</qresource^>
  echo ^</RCC^>
) >> "!fileOutput!"
```

##### 编写markdown中的无序列表
递归遍历文件，编写所需要跳转的文件映射在markdown中的无序列表。运行后，从控制台复制输出结果到markdown中。
```bat
@echo off
setlocal EnableDelayedExpansion
for /r %%i in (*.lua) do (
  set szPath=%%i
  set szRelPath=!szPath:%cd%\=!
  echo * [%%~nxi]^(./!szRelPath!^)
)
```

##### 生成WinMerger文件
对两个目录结构相同的大型工程目录，需要大量对比某些文件夹，但又不需要在全部比较时，可以编写个遍历脚本来批量生成，统一保存成WinMerge文件后，方便日后直接双击打开，无需再麻烦地拖动文件夹来对比。
```bat
@echo off
setlocal EnableDelayedExpansion
set OUTPUT_PATH=F:\WinMergeProjectsOfMiniWorld
@REM 指定两个工程的路径
set aEnvPaths=
set "aEnvPaths=!aEnvPaths!,C:\trunk\env1ed"
set "aEnvPaths=!aEnvPaths!,F:\trunk\env1oversea"
set aEnvPaths=!aEnvPaths:~1!
@REM 指定需要WinMerge的部分文件夹
set aSubPaths=
set "aSubPaths=!aSubPaths!,bin"
set "aSubPaths=!aSubPaths!,bin\res"
set "aSubPaths=!aSubPaths!,bin\res\ui"
set "aSubPaths=!aSubPaths!,source\client"
set "aSubPaths=!aSubPaths!,source\server"
set aSubPaths=!aSubPaths:~1!
if not exist %OUTPUT_PATH% mkdir %OUTPUT_PATH%
for %%i in (!aEnvPaths!) do (
  for %%j in (!aEnvPaths!) do (
    for %%k in (!aSubPaths!) do (
      set szEnvLeft=
      set szEnvRight=
      call :function_getFolderNameFromPath szEnvLeft "%%i"
      call :function_getFolderNameFromPath szEnvRight "%%j"
      set szLeftPath=%%i\%%k
      set szRightPath=%%j\%%k
      set szFolderName=
      call :function_getFolderNameFromPath szFolderName "!szLeftPath!"
      set "szOutputPath=%OUTPUT_PATH%\!szFolderName! !szEnvLeft! - !szEnvRight!.WinMerge"
      @REM echo szLeftPath = !szLeftPath!
      @REM echo szFolderName = !szFolderName!
      call :localFunction_createWinMergeProject "!szOutputPath!" "!szLeftPath!" "!szRightPath!"
    )
  )
)
@REM -----------------------------------------------------------------------
@REM 从路径中获取文件名
:function_getFolderNameFromPath
set "szPath=%~2"
if "!szPath!" == "" (
  echo [ERROR] path empty
  goto :eof
)
if not exist "!szPath!" (
  echo [ERROR] not exist: !szPath!
  goto :eof
)
pushd "!szPath!"
for /f "delims=" %%i in ('cd') do (
  set szFolderName=%%~nxi
)
popd
set "%~1=!szFolderName!"
goto :eof
@REM -----------------------------------------------------------------------
@REM 创建WinMerge文件
:localFunction_createWinMergeProject
set "szOutputPath=%~1"
set "szLeftPath=%~2"
set "szRightPath=%~3"
set "szFileType=%~4"
if "!szOutputPath!" == "" (
  echo [ERROR] output path empty
  goto :eof
)
if "!szLeftPath!" == "" (
  echo [ERROR] left path empty
  goto :eof
)
if "!szRightPath!" == "" (
  echo [ERROR] right path empty
  goto :eof
)
if "!szLeftPath!" == "!szRightPath!" (
  @REM echo [ERROR] both paths equal
  goto :eof
)
if not exist "!szLeftPath!" (
  @REM echo [ERROR] not exist: !szLeftPath!
  goto :eof
)
if not exist "!szRightPath!" (
  @REM echo [ERROR] not exist: !szRightPath!
  goto :eof
)
if "!szFileType!" == "" (
  set szFileType=*.*
)
if exist "!szOutputPath!" del /q "!szOutputPath!"

(
  echo ^<?xml version="1.0" encoding="UTF-8"?^>
  echo ^<project^>
  echo 	^<paths^>
  echo 		^<left^>!szLeftPath!^</left^>
  echo 		^<right^>!szRightPath!^</right^>
  echo 		^<filter^>!szFileType!^</filter^>
  echo 		^<subfolders^>1^</subfolders^>
  echo 		^<left-readonly^>0^</left-readonly^>
  echo 		^<right-readonly^>0^</right-readonly^>
  echo 	^</paths^>
  echo ^</project^>
) >!szOutputPath!
goto :eof
@REM -----------------------------------------------------------------------

```
> 相对于其它语言的代码，有关遍历文件的都可以自己配置来添加常量数组、常量字符串数组、代码等不同功能的文本

### VSCode使用一键输入代码进行快速编写
使用VSCode时，可以通过配置[Snippets](https://juejin.cn/post/6844903795663568910)来完成一键输入的效果，提高编写效率。
下方贴出[基本遍历](#基本遍历)的配置代码，供大家方便复制使用：
```json
{
	"for_f_iterate_files_recursively" : {
		"scope": "bat",
		"prefix": "forffr",
		"body": [
			"for /f %%i in ('dir /s /b *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "递归遍历文件"
	},

	"for_r_iterate_files_recursively" : {
		"scope": "bat",
		"prefix": "forrfr",
		"body": [
			"for /r %cd% %%i in (*) do (",
			"  echo %%i",
		  ")",
		],
		"description": "递归遍历文件"
	},

	"for_f_iterate_files" : {
		"scope": "bat",
		"prefix": "forff",
		"body": [
			"for /f %%i in ('dir /b *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "遍历一级文件"
	},

	"for_f_iterate_dirs_recursively" : {
		"scope": "bat",
		"prefix": "forfdr",
		"body": [
			"for /f %%i in ('dir /s /b /ad *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "递归遍历文件夹"
	},

	"for_d_iterate_dirs" : {
		"scope": "bat",
		"prefix": "ford",
		"body": [
			"for /d %%i in (*) do (",
			"  echo %%i",
		  ")",
		],
		"description": "遍历一级文件夹"
	},

	"for_d_iterate_dirs" : {
		"scope": "bat",
		"prefix": "forfd",
		"body": [
			"for /f %%i in ('dir /b /ad *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "遍历一级文件夹"
	},
}
```
> 像其它如Sublime的工具，也可以自行配置来方便编写