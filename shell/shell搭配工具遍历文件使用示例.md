## 说明

> 运行环境：Windows 10

本文收集一些shell脚本常用的遍历文件的使用示例。

## 目录
- [说明](#说明)
- [目录](#目录)
  - [1.常用语句](#1常用语句)
    - [数组拼接与遍历](#数组拼接与遍历)
    - [字符串拼接与遍历](#字符串拼接与遍历)
    - [递归查找文件](#递归查找文件)
    - [递归查找多种后缀的文件](#递归查找多种后缀的文件)
    - [将命令行输出结果保存到变量中](#将命令行输出结果保存到变量中)
    - [获取上一条命令的运行结果](#获取上一条命令的运行结果)
    - [字符串匹配判断](#字符串匹配判断)
    - [文件或文件夹判断](#文件或文件夹判断)
  - [2.基本遍历](#2基本遍历)
    - [遍历一级文件](#遍历一级文件)
    - [递归遍历文件](#递归遍历文件)
  - [3.搭配使用](#3搭配使用)
    - [批量转码](#批量转码)
- [VSCode使用一键输入代码进行快速编写](#vscode使用一键输入代码进行快速编写)

### 1.常用语句

#### 数组拼接与遍历
```bash
array[${#array[*]}]=123
array[${#array[*]}]=abc
array[${#array[*]}]="string"
for (( i = 0; i < ${#array[*]}; ++i ))
do
    echo ${array[i]}
done
```
#### 字符串拼接与遍历
```bash
paths="$paths header/"
paths="$paths include/"
for (( i = 0; i < ${#paths[*]}; ++i ))
do
    echo ${paths[i]}
done
```
#### 递归查找文件
```bash
find ./ -name "*.*"
```
#### 递归查找多种后缀的文件
```bash
find ./ -regex ".*\.\(sh\|md\)"
```
#### 将命令行输出结果保存到变量中
```bash
szResult=`file --mime-encoding README.md`
echo $szResult
```
或：
```bash
szResult=$(file --mime-encoding README.md)
echo $szResult
```
#### 获取上一条命令的运行结果
使用`$?`获取数值结果。与获取输出结果不同。
#### 字符串匹配判断
```bash
szResult=`file --mime-encoding README.md`
hasUTF8=`echo $szResult | awk '{print match($0, utf-8)}'`
echo $hasUTF8
if [[ $hasUTF8 -gt 0 ]];
then
    echo README.md is UTF-8
fi
```
#### 文件或文件夹判断
```bash
if test -f $file
then
    echo $file is a file.
elif test -d $file
then
    echo $file is a directory.
fi
```

### 2.基本遍历

#### 遍历一级文件
> 需要同时遍历文件夹时，通配符改为`*`
```bash
for file in *.*
do
    echo $file
done
```

#### 递归遍历文件
> 需要同时遍历文件夹时，通配符改为`*`
```bash
for file in `find ./ -name "*.*"` 
do
    echo $file
done
```

### 3.搭配使用

#### 批量转码
例如，我们需要将UTF-8的h文件和cpp文件转换成ascii。
这里我们需要从`$?`获取`iconv`命令的运行结果来判断，是否运行成功，避免仅转换到半截文本。
```bash
szRootPath=./
szFileFilter=".*\.\(h\|cpp\)"
for file in `find $szRootPath -regex "$szFileFilter"`
do
    szResult=`file --mime-encoding $file`
    hasUTF8=`echo $szResult | awk '{print match($0, utf-8)}'`
    if [[ $hasUTF8 -gt 0 ]];
    then
        szIconvResult=$(iconv -f UTF-8 -t GB2312 $file > UTF-8_FILE)
        if [[ $? -eq 0 ]];
        then
            echo $szResult
            cp UTF-8_FILE $file
        else
            echo $szIconvResult
        fi
    fi
done
```

## VSCode使用一键输入代码进行快速编写
可将下方的基本遍历的代码直接配置到shellscript.json中，方便随时编写使用
```json
{
	"for_iterate_files_recursively" : {
		"prefix": "forfr",
		"body": [
			"for file in `find ./ -name \"*.*\"` ",
			"do",
			"    echo $$file",
			"done",
		],
		"description": "递归遍历文件"
	},

	"for_iterate_files" : {
		"prefix": "forf",
		"body": [
			"for file in *.*",
			"do",
			"    echo $$file",
			"done",
		],
		"description": "遍历一级文件"
	},
}
```