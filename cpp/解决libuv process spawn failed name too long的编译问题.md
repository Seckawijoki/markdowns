## Ŀ¼
- [Ŀ¼](#Ŀ¼)
- [���л�����](#���л���)
- [�������⣺](#��������)
- [�������Դ](#�������Դ)
- [��������в���](#��������в���)
- [�������](#�������)
  - [һ��CMake�����з���](#һcmake�����з���)
  - [����CMake��C++�Ļ�ϸĶ�](#��cmake��c�Ļ�ϸĶ�)
    - [C++�ļ���](#c�ļ�)
    - [CMake�ļ���](#cmake�ļ�)
  - [�����Ķ�����Ŀ¼�ṹ](#���Ķ�����Ŀ¼�ṹ)

## ���л�����
> Windows 10 רҵ��  
> Visual Studio 2019 Community/Professional  
> C++����Ϊһ���ϴ���Ŀ������SDK������90����⣬�ܼƴ�6λ����������

## �������⣺

    AutoMoc subprocess error
    ------------------------
    The moc process failed to compile
    "SRC:/XXX.h"
    into
    "BIN:/XXX.cpp"
    libuv process spawn failed: name too long

## �������Դ
��������CreateProcess������������г���Ϊ32767�ַ����������������UNICODE_STRING�ṹ����Windows��C++������ͨ��ʹ��msbuild.exe�����н��У����ڲ�������������CreateProcessʧ�ܣ���ʹ����ʧ�ܡ�

## ��������в���
��Visual Studio���ڱ��뱨���Ŀ���**����Ҽ�**��**����**��Ȼ�����δ�**��������**��**C++**��**������**�������������е����в�����

���Խ���Щ�����и��Ƶ�Visual Code�Ĺ��ߣ�ȫѡ���ɿ����ַ���Ŀ��

## �������

### һ��CMake�����з���

1. ��鲢���ٸ���Ŀinclude_directories�е��ظ�·������û��ʵ�����õ�·��
2. ���include_directories�������õ������·������Ҫ����Щ���·���滻��**����·��**����������cmakeִ��[include_directories](https://cmake.org/cmake/help/latest/command/include_directories.html)ʱ������жϵ������·���������·��ǰ����ƴ�ӵ�ǰcmake�Ĺ������ڵ�·��������ʹC++����ĸ��Ӱ���·���䳤��

### ����CMake��C++�Ļ�ϸĶ�

��include_directories�е�ĳЩ·������������ͷ�ļ��Ƚ���ʱ����ֱ����C++��Ŀ�У�������#include��ͷ�ļ��ĵط��������ƴ����һ���ͷ�ļ����ڵ�Ŀ¼����ʱ���ɽ�cmake��include_directories������·��ȥ��������ʾ�����£�

#### C++�ļ���
~~`#include "Prerequisites.h"`~~  
**`#include "Config/Prerequisites.h"`**

#### CMake�ļ���
~~`include_diretories(${PROJECT_COMMON_DIR}/Core/Config)`~~  
**`include_diretories(${PROJECT_COMMON_DIR}/Core)`**

> ���ָĶ�������ͨ����дһ���ű��������滻

### �����Ķ�����Ŀ¼�ṹ

��30000���ַ��ĸ��Ӱ���·���У����д���ظ���ǰ׺·������ʱ�����Խ�**�ܵĹ���Ŀ¼������һ���ļ��У���ֱ���̷�Ŀ¼���ƶ�**���Լ����ظ���ǰ׺·���ĳ��ȡ�

��������в��������ҵ�ĳ����������Ŀ¼������Ŀ¼���˵�����һ���ļ����ƶ����Դﵽ���̰���·����Ŀ�ġ�