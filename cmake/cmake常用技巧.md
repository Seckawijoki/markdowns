## Ŀ¼
- [Ŀ¼](#Ŀ¼)
- [˵��](#˵��)
- [����](#����)
- [��װ�ĺ���](#��װ�ĺ���)
  - [Visual Studio��������ļ�](#visual-studio��������ļ�)
    - [����ļ������](#����ļ������)
    - [��д�ļ�](#��д�ļ�)
  - [Ѱ��sh.exe������shell�ű�](#Ѱ��shexe������shell�ű�)
- [Qt��Ŀ](#qt��Ŀ)
  - [ͨ������](#ͨ������)
  - [ת�������ļ�](#ת�������ļ�)
  - [�����Զ�UIC���ļ�](#�����Զ�uic���ļ�)
- [С����](#С����)
  - [��дϵͳ����](#��дϵͳ����)
  - [��VS�������������������¼�](#��vs�������������������¼�)
  - [������ʽ��ʹ�õ�һЩ�﷨���ַ�](#������ʽ��ʹ�õ�һЩ�﷨���ַ�)
- [VSCode�����õ�Snippet](#vscode�����õ�snippet)

## ˵��
  �����ռ�һЩcmake���ù���ʱ���õĺ������﷨�����ɻ�ע��������������£�

## ����
  Windows 10 Professional��CMake 3.17.4

## ��װ�ĺ���

### Visual Studio��������ļ�

#### ����ļ������
```cmake
��ӵ���ͷ�ļ��󣬲���ͷ�ļ�����ڵ�ǰcmake��Ŀ����Ŀ¼��·�����������ļ��еķ���
function(add_filtered_h filterName fileString)
  file(GLOB TMP_FILES ${fileString})
  set(HEADER_FILES ${HEADER_FILES} ${TMP_FILES} PARENT_SCOPE)
  source_group(${filterName} FILES ${TMP_FILES})
endfunction( add_filtered_h )

#��ӵ���Դ�ļ��󣬲���Դ�ļ�����ڵ�ǰcmake��Ŀ����Ŀ¼��·�����������ļ��еķ���
function(add_filtered_src filterName fileString)
  file(GLOB TMP_FILES ${fileString})
  set(SOURCE_FILES ${SOURCE_FILES} ${TMP_FILES} PARENT_SCOPE)
  source_group(${filterName} FILES ${TMP_FILES})
endfunction( add_filtered_src )

#��ӹ����ļ���
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

#### ��д�ļ�
```cmake
# ��cpp�ļ����޸�ÿ�ι���ʱ��
function(writeBuildTime szFilePath)
  file(READ ${szFilePath} szContent)
  # ƥ��ĸ��д����������ʽ
  set(szPattern "const[ ]*char\\*[ ]*g_BuildTime[ ]*=[ ]*\"BuildTime[ ]*:[0-9| ]+/[0-9| ]+/[0-9| ]+[ ]*[0-9| ]+:[0-9| ]+:[0-9| ]+\"[ ]*;")
  # ��ȡ��ǰʱ��
  string(TIMESTAMP szNow "%Y/%m/%d %H:%M:%S")
  # �滻��Ĵ���
  set(szReplace "const char* g_Buildtime = \"BuildTime:${szNow}\";")
  string(REGEX REPLACE "${szPattern}" "${szReplace}" szContent "${szContent}")
  file(WRITE ${szFilePath} "${szContent}")
endfunction()
```

### Ѱ��sh.exe������shell�ű�
> ��Ҫ���ú�git.exe�Ļ�������
```cmake
function(writeBuildTime)
  find_program(exeGit git)
  if (exeGit)
    get_filename_component(szDir ${exeGit} DIRECTORY)
    # ����git-bash.exe��·��
    set(exeGitBash ${szDir}/../git-bash.exe)
    # ת����Windows·��ʹ�õķ�б��
    string(REPLACE ${exeGitBash} "\/" "\\" exeGitBash)
    add_custom_command(TARGET ${APP_NAME} PRE_BUILD
      COMMAND ${exeGitBash} ARGS write_build_time.sh
    )
  endif()
endfunction()
```
> ����ʹ��sh.exe������shell�ű�������Ŀʹ��IncrediBuild���б���Ļ�����������exe��ʼǰ��סVisual Studio��

## Qt��Ŀ

### ͨ������
```cmake
# ��moc��rcc��uic
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# ����ts�ļ�
FILE(GLOB TS_FILES "ts/*.ts")

# ƴ��uic������·��
set (CMAKE_AUTOUIC_SEARCH_PATHS "")
# ʹ��uic����.ui�ļ�
foreach(ui_file ${UI_FILES}) 
  get_filename_component(dir_path ${ui_file} DIRECTORY)
  set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_AUTOUIC_SEARCH_PATHS} ${dir_path})
endforeach()
# �Ƴ���ͬ��UIC����·��
list(REMOVE_DUPLICATES CMAKE_AUTOUIC_SEARCH_PATHS)

QT5_WRAP_UI(UIC_FILES ${UI_FILES})
#����Ԥ������moc��Ϊÿ����Q_OBJECT��ͷ�ļ�����moc_XXX.cpp�ļ�
QT5_WRAP_CPP(MOC_FILES ${HEADER_FILES})
#ʹ��rcc����.qrc�ļ�
QT5_ADD_RESOURCES(RES_FILES ${RESOURCES})

# ��ui�ļ��������ļ�����
SOURCE_GROUP("Form Files" FILES ${UI_FILES})
# ��qrc��Դ�ļ�����
SOURCE_GROUP("Resource Files" FILES ${RES_FILES})
# �������ļ�����
SOURCE_GROUP("Translation Files" FILES ${TS_FILES})
# ��moc��rcc��uic���м������ļ�����
SOURCE_GROUP("Generated Files" FILES ${MOC_FILES} ${UIC_FILES})
```

### ת�������ļ�
```cmake
# �����Է����ļ�
# ����lupdate��lrelease����
find_program(LUPDATE_EXECUTABLE lupdate)
find_program(LRELEASE_EXECUTABLE lrelease)
foreach(_ts_file ${TS_FILES})
  execute_process(COMMAND ${LUPDATE_EXECUTABLE} -recursive ${CMAKE_CURRENT_SOURCE_DIR} -ts ${_ts_file})
  execute_process(COMMAND ${LRELEASE_EXECUTABLE} ${_ts_file})
endforeach()
```

### �����Զ�UIC���ļ�
```cmake
set_property(SOURCE <file> PROPERTY SKIP_AUTOUIC ON)
```

## С����

### ��дϵͳ����
����������ʹ��``ENV{��������}``����ʽ��
> [�ο��ٷ�˵����](https://cmake.org/cmake/help/latest/variable/ENV.html)

### ��VS�������������������¼�

�����´��룬ʹ��``$<CONFIG:yourConfig>``����������ֵ�жϣ���Ƕ��``$<condition:yourCommand>``�е�conditionλ�ã�    
��ʵ�ֽ���������÷ֱ���������¼���

���ڶ�����÷���ʱ��ֱ��ƴ����ͬ��ʽ�Ĵ��룬���ƶ��if������д��

```cmake
add_custom_command(TARGET ${APP_NAME} PRE_BUILD
  COMMAND $<$<CONFIG:Release>:${RELEASE_COMMAND}$<CONFIG:Debug>:${DEBUG_COMMAND}> ARGS ${someArgs}
)
```
> [�ο�]��https://stackoverflow.com/questions/13996440/how-do-you-make-add-custom-command-configuration-specific-on-windows��


### ������ʽ��ʹ�õ�һЩ�﷨���ַ�
~~\s~~��ƥ�����пո��ַ�    
~~{}~~�����ʷ���

## VSCode�����õ�Snippet
VSCode������Snippets��[���ò���ο�](https://juejin.cn/post/6844903795663568910)�������һ�������Ч������߱�дЧ�ʡ�
```json
{
  "message variable": {
    "scope": "CMakeLists.txt",
    "prefix": "msgv",
    "body": [
      "message(NOTICE \"$CLIPBOARD = ${$CLIPBOARD}\")",
    ],
    "description": "��ӡ���Ƶı���"
  },

  "message": {
    "scope": "CMakeLists.txt",
    "prefix": "msg",
    "body": [
      "message(NOTICE \"$1\")",
    ],
    "description": "��ӡ����������"
  },
}
```