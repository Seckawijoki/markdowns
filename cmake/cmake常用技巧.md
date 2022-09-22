## 目录
- [目录](#目录)
- [说明](#说明)
- [环境](#环境)
- [封装的函数](#封装的函数)
  - [Visual Studio工程添加文件](#visual-studio工程添加文件)
    - [添加文件与分组](#添加文件与分组)
    - [改写文件](#改写文件)
  - [寻找sh.exe来调用shell脚本](#寻找shexe来调用shell脚本)
- [Qt项目](#qt项目)
  - [通用配置](#通用配置)
  - [转换翻译文件](#转换翻译文件)
  - [跳过自动UIC的文件](#跳过自动uic的文件)
- [小技巧](#小技巧)
  - [读写系统变量](#读写系统变量)
  - [按VS解决方案配置添加生成事件](#按vs解决方案配置添加生成事件)
  - [正则表达式不使用的一些语法和字符](#正则表达式不使用的一些语法和字符)
- [VSCode上配置的Snippet](#vscode上配置的snippet)

## 说明
  本文收集一些cmake配置工程时常用的函数、语法、技巧或注意事项。（持续更新）

## 环境
  Windows 10 Professional、CMake 3.17.4

## 封装的函数

### Visual Studio工程添加文件

#### 添加文件与分组
```cmake
添加单个头文件后，并按头文件相对于当前cmake项目所在目录的路径，进行子文件夹的分组
function(add_filtered_h filterName fileString)
  file(GLOB TMP_FILES ${fileString})
  set(HEADER_FILES ${HEADER_FILES} ${TMP_FILES} PARENT_SCOPE)
  source_group(${filterName} FILES ${TMP_FILES})
endfunction( add_filtered_h )

#添加单个源文件后，并按源文件相对于当前cmake项目所在目录的路径，进行子文件夹的分组
function(add_filtered_src filterName fileString)
  file(GLOB TMP_FILES ${fileString})
  set(SOURCE_FILES ${SOURCE_FILES} ${TMP_FILES} PARENT_SCOPE)
  source_group(${filterName} FILES ${TMP_FILES})
endfunction( add_filtered_src )

#添加过滤文件夹
function(add_filtered_std_rooted rootPath relativePath)
  string(REPLACE "/" "\\" filterPart ${relativePath})
  include_directories("${rootPath}/${relativePath}")
  set(PRO_INCLUDE_DIRS  ${PRO_INCLUDE_DIRS} "${rootPath}/${relativePath}" PARENT_SCOPE)
  add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.h")
  add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.hpp")
  add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.inl")
  add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.cpp")
  add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.c")
  add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.cc")
  if(APPLE_IOS)
    add_filtered_src("Source Files\\${filterPart}" "${rootPath}/${relativePath}/*.mm")
  endif()
  set(SOURCE_FILES ${SOURCE_FILES} PARENT_SCOPE)
endfunction(add_filtered_std_rooted)
```

#### 改写文件
```cmake
# 在cpp文件中修改每次构建时间
function(writeBuildTime szFilePath)
  file(READ ${szFilePath} szContent)
  # 匹配的该行代码的正则表达式
  set(szPattern "const[ ]*char\\*[ ]*g_BuildTime[ ]*=[ ]*\"BuildTime[ ]*:[0-9| ]+/[0-9| ]+/[0-9| ]+[ ]*[0-9| ]+:[0-9| ]+:[0-9| ]+\"[ ]*;")
  # 获取当前时间
  string(TIMESTAMP szNow "%Y/%m/%d %H:%M:%S")
  # 替换后的代码
  set(szReplace "const char* g_Buildtime = \"BuildTime:${szNow}\";")
  string(REGEX REPLACE "${szPattern}" "${szReplace}" szContent "${szContent}")
  file(WRITE ${szFilePath} "${szContent}")
endfunction()
```

### 寻找sh.exe来调用shell脚本
> 需要配置好git.exe的环境变量
```cmake
function(writeBuildTime)
  find_program(exeGit git)
  if (exeGit)
    get_filename_component(szDir ${exeGit} DIRECTORY)
    # 设置git-bash.exe的路径
    set(exeGitBash ${szDir}/../git-bash.exe)
    # 转换成Windows路径使用的反斜杠
    string(REPLACE ${exeGitBash} "\/" "\\" exeGitBash)
    add_custom_command(TARGET ${APP_NAME} PRE_BUILD
      COMMAND ${exeGitBash} ARGS write_build_time.sh
    )
  endif()
endfunction()
```
> 避免使用sh.exe来运行shell脚本。在项目使用IncrediBuild进行编译的话，会在链接exe开始前卡住Visual Studio。

## Qt项目

### 通用配置
```cmake
# 打开moc、rcc和uic
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# 搜索ts文件
FILE(GLOB TS_FILES "ts/*.ts")

# 拼接uic的搜索路径
set (CMAKE_AUTOUIC_SEARCH_PATHS "")
# 使用uic处理.ui文件
foreach(ui_file ${UI_FILES}) 
  get_filename_component(dir_path ${ui_file} DIRECTORY)
  set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_AUTOUIC_SEARCH_PATHS} ${dir_path})
endforeach()
# 移除相同的UIC搜索路径
list(REMOVE_DUPLICATES CMAKE_AUTOUIC_SEARCH_PATHS)

QT5_WRAP_UI(UIC_FILES ${UI_FILES})
#调用预编译器moc，为每个含Q_OBJECT的头文件生成moc_XXX.cpp文件
QT5_WRAP_CPP(MOC_FILES ${HEADER_FILES})
#使用rcc处理.qrc文件
QT5_ADD_RESOURCES(RES_FILES ${RESOURCES})

# 将ui文件和生成文件分组
SOURCE_GROUP("Form Files" FILES ${UI_FILES})
# 将qrc资源文件分组
SOURCE_GROUP("Resource Files" FILES ${RES_FILES})
# 将翻译文件分组
SOURCE_GROUP("Translation Files" FILES ${TS_FILES})
# 将moc、rcc和uic的中间生成文件分组
SOURCE_GROUP("Generated Files" FILES ${MOC_FILES} ${UIC_FILES})
```

### 转换翻译文件
```cmake
# 多语言翻译文件
# 查找lupdate和lrelease程序
find_program(LUPDATE_EXECUTABLE lupdate)
find_program(LRELEASE_EXECUTABLE lrelease)
foreach(_ts_file ${TS_FILES})
  execute_process(COMMAND ${LUPDATE_EXECUTABLE} -recursive ${CMAKE_CURRENT_SOURCE_DIR} -ts ${_ts_file})
  execute_process(COMMAND ${LRELEASE_EXECUTABLE} ${_ts_file})
endforeach()
```

### 跳过自动UIC的文件
```cmake
set_property(SOURCE <file> PROPERTY SKIP_AUTOUIC ON)
```

## 小技巧

### 读写系统变量
变量名规则使用``ENV{变量名字}``的形式。
> [参考官方说明。](https://cmake.org/cmake/help/latest/variable/ENV.html)

### 按VS解决方案配置添加生成事件

用如下代码，使用``$<CONFIG:yourConfig>``，来做布尔值判断，内嵌到``$<condition:yourCommand>``中的condition位置，    
来实现解决方案配置分别添加生成事件。

存在多个配置方案时，直接拼接相同格式的代码，类似多个if语句块连写。

```cmake
add_custom_command(TARGET ${APP_NAME} PRE_BUILD
  COMMAND $<$<CONFIG:Release>:${RELEASE_COMMAND}$<CONFIG:Debug>:${DEBUG_COMMAND}> ARGS ${someArgs}
)
```
> [参考]（https://stackoverflow.com/questions/13996440/how-do-you-make-add-custom-command-configuration-specific-on-windows）


### 正则表达式不使用的一些语法和字符
~~\s~~：匹配所有空格字符    
~~{}~~：量词符号

## VSCode上配置的Snippet
VSCode的配置Snippets（[设置步骤参考](https://juejin.cn/post/6844903795663568910)）来完成一键输入的效果，提高编写效率。
```json
{
  "message variable": {
    "scope": "CMakeLists.txt",
    "prefix": "msgv",
    "body": [
      "message(NOTICE \"$CLIPBOARD = ${$CLIPBOARD}\")",
    ],
    "description": "打印复制的变量"
  },

  "message": {
    "scope": "CMakeLists.txt",
    "prefix": "msg",
    "body": [
      "message(NOTICE \"$1\")",
    ],
    "description": "打印并输入内容"
  },
}
```