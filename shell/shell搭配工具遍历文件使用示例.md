## ˵��

> ���л�����Windows 10

�����ռ�һЩshell�ű����õı����ļ���ʹ��ʾ����

## Ŀ¼
- [˵��](#˵��)
- [Ŀ¼](#Ŀ¼)
  - [1.�������](#1�������)
    - [����ƴ�������](#����ƴ�������)
    - [�ַ���ƴ�������](#�ַ���ƴ�������)
    - [�ݹ�����ļ�](#�ݹ�����ļ�)
    - [�ݹ���Ҷ��ֺ�׺���ļ�](#�ݹ���Ҷ��ֺ�׺���ļ�)
    - [�����������������浽������](#�����������������浽������)
    - [��ȡ��һ����������н��](#��ȡ��һ����������н��)
    - [�ַ���ƥ���ж�](#�ַ���ƥ���ж�)
    - [�ļ����ļ����ж�](#�ļ����ļ����ж�)
  - [2.��������](#2��������)
    - [����һ���ļ�](#����һ���ļ�)
    - [�ݹ�����ļ�](#�ݹ�����ļ�)
  - [3.����ʹ��](#3����ʹ��)
    - [����ת��](#����ת��)
- [VSCodeʹ��һ�����������п��ٱ�д](#vscodeʹ��һ�����������п��ٱ�д)

### 1.�������

#### ����ƴ�������
```bash
array[${#array[*]}]=123
array[${#array[*]}]=abc
array[${#array[*]}]="string"
for (( i = 0; i < ${#array[*]}; ++i ))
do
    echo ${array[i]}
done
```
#### �ַ���ƴ�������
```bash
paths="$paths header/"
paths="$paths include/"
for (( i = 0; i < ${#paths[*]}; ++i ))
do
    echo ${paths[i]}
done
```
#### �ݹ�����ļ�
```bash
find ./ -name "*.*"
```
#### �ݹ���Ҷ��ֺ�׺���ļ�
```bash
find ./ -regex ".*\.\(sh\|md\)"
```
#### �����������������浽������
```bash
szResult=`file --mime-encoding README.md`
echo $szResult
```
��
```bash
szResult=$(file --mime-encoding README.md)
echo $szResult
```
#### ��ȡ��һ����������н��
ʹ��`$?`��ȡ��ֵ��������ȡ��������ͬ��
#### �ַ���ƥ���ж�
```bash
szResult=`file --mime-encoding README.md`
hasUTF8=`echo $szResult | awk '{print match($0, utf-8)}'`
echo $hasUTF8
if [[ $hasUTF8 -gt 0 ]];
then
    echo README.md is UTF-8
fi
```
#### �ļ����ļ����ж�
```bash
if test -f $file
then
    echo $file is a file.
elif test -d $file
then
    echo $file is a directory.
fi
```

### 2.��������

#### ����һ���ļ�
> ��Ҫͬʱ�����ļ���ʱ��ͨ�����Ϊ`*`
```bash
for file in *.*
do
    echo $file
done
```

#### �ݹ�����ļ�
> ��Ҫͬʱ�����ļ���ʱ��ͨ�����Ϊ`*`
```bash
for file in `find ./ -name "*.*"` 
do
    echo $file
done
```

### 3.����ʹ��

#### ����ת��
���磬������Ҫ��UTF-8��h�ļ���cpp�ļ�ת����ascii��
����������Ҫ��`$?`��ȡ`iconv`��������н�����жϣ��Ƿ����гɹ��������ת��������ı���
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

## VSCodeʹ��һ�����������п��ٱ�д
�ɽ��·��Ļ��������Ĵ���ֱ�����õ�shellscript.json�У�������ʱ��дʹ��
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
		"description": "�ݹ�����ļ�"
	},

	"for_iterate_files" : {
		"prefix": "forf",
		"body": [
			"for file in *.*",
			"do",
			"    echo $$file",
			"done",
		],
		"description": "����һ���ļ�"
	},
}
```