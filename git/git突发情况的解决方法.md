## Ŀ¼
- [Ŀ¼](#Ŀ¼)
- [˵��](#˵��)
- [��Ŀ�ṹ](#��Ŀ�ṹ)
- [��������](#��������)
- [��������](#��������)
  - [git stash](#git-stash)
  - [git fetch](#git-fetch)
  - [git branch](#git-branch)
  - [git checkout](#git-checkout)
  - [git reset](#git-reset)
  - [git restore](#git-restore)
- [ʹ�ü���](#ʹ�ü���)
  - [1.�½����ؿ�����֧](#1�½����ؿ�����֧)
    - [�龰](#�龰)
    - [�������](#�������)
    - [�ؼ�����](#�ؼ�����)
  - [2.��ģ�鿪�����벻ͬ��](#2��ģ�鿪�����벻ͬ��)
    - [�龰](#�龰-1)
    - [�������](#�������-1)
    - [�ؼ�����](#�ؼ�����-1)
  - [3.�ύ��¼����](#3�ύ��¼����)
    - [�龰](#�龰-2)
    - [�������](#�������-2)
    - [�ؼ�����](#�ؼ�����-2)

## ˵��
�����ռ������ڹ�������������һЩͻ��������Լ��պ����������ֵĽ�����������������£�
> ����˵���У���Ҫ����ģ����롢�����ģ����롣ÿ��ģ�麬����֧main��������֧develop��

## ��Ŀ�ṹ
��Ŀ�ṹ����Ϊ��ģ�飨�ļ��У����������ģ�飨�ļ��У��㼶���ۣ�����ʽ
>��ģ��    
������ģ��1  
������ģ��2  
������ģ��3  
����...  

## ��������
Windows 10 Professional

## ��������
�ȴ����˽��£�����ҪƵ���õ���һЩ����
> ����[����ҳ����ϰ��֧����](https://learngitbranching.js.org/?locale=zh_CN&NODEMO)
### git stash
```shell
# ���ر����޸ĵ��ļ������������ļ�
git stash -u -m "my message"

# ��ʾ���ع����޸��б�
git stash list

# Ӧ�����һ�����ص��޸�
git stash apply

# Ӧ��ָ�����ص��޸ġ���stash@{1}�����ջ��0��ʼ�ĵڶ������ء�
# ��git stash list�ܲ鿴����ҪӦ�õ�����
git stash apply stash@{1}
```

### git fetch
```shell
# ������develop��֧�У���ȡmain��֧�Ĵ���
git fetch origin main:main
```

### git branch
```shell
# ��ʾ���ص����з�֧
git branch

# ��ʾ���ؼ�Զ�̵����з�֧
git branch -a

# ������֧bugfix
git branch bugfix

# ��commit hashΪXXXX�İ汾������֧bugfix
git branch bugfix XXXX

# �ӵ�ǰ��֧��������������commit�İ汾������֧bugfix
git branch bugfix HEAD~3

# ɾ��bugfix��֧
git branch -d bugfix

# ����ǰ��֧�����ָ�Ϊdevelop
git branch -m develop
```

### git checkout
```shell
# �л���develop��֧
git checkout develop

# ����bugfix��֧�����л����˷�֧
git checkout -b bugfix

# ��commit hashΪXXXX�İ汾������֧bugfix�����л����˷�֧
git checkout -b bugfix XXXX

# �ӵ�ǰ��֧��������������commit�İ汾������֧bugfix�����л����˷�֧
git checkout -b bugfix HEAD~3

# �ع���ǰ��֧�޸Ĺ����ļ���������������
git checkout -- .
```

### git reset
```shell
# �������һ�ε��ύ
git reset HEAD~1

# �������һ�ε��ύ�������������޸�
git reset HEAD~1 --soft
```

### git restore
```shell
# �ع���ǰ��֧�޸Ĺ����ļ���������������
git restore .
```

## ʹ�ü���

### 1.�½����ؿ�����֧

#### �龰 
��ȡ�˴�������˱��벻����Ӱ�칤����������  

#### �������
��ĳһ���ȶ�����ʷ�ύ�汾����ĳ���ȶ��ķ�֧�������½���֧���������й���  

#### �ؼ�����
```shell
# ��ȷ��Ϊ�ȶ��汾��commit hashΪ3d5cb75d8�İ汾������֧stable�����л����˷�֧
git checkout -b stable 3d5cb75d8
```

### 2.��ģ�鿪�����벻ͬ��

#### �龰 
����ģ�飨develop��֧���Ĵ����������ʱ������ģ�飨main��֧���Ĵ�������ĳ��ԭ��δ�������¡�������б���ʱ����ģ����ȱ����ģ��ĳ���ύ�ĸĶ������벻����

#### �������
����ģ��cherry-pick�ܽ�����������ĳ���ύ��Ȼ���ѡ���Ե�reset�������زֿ���ύ�����õ�ȱʧ���ļ���
��������ģ�鴦�������󣬿�ʹdevelop�ض�����rebase����main��֧�ϡ�

#### �ؼ�����
```shell
git checkout main

# ȱʧ���ļ�����ģ��main��֧���ύa20d551ded�ϣ�����cherry-pick����
# ������֧������
git cherry-pick a20d551ded

# ����ѡ����������a20d551ded�ڸ�ģ��develop��֧�ϵ��ύ��ʹ����˵������޸�
# HEAD~1Ϊ��һ���ύ��HEAD~2λ�����ڶ��Σ���������
git reset HEAD~1
```
��ʱ������������Ҫ�ϲ���ĳ���ύ�����ٰ������������С�
��ģ�鴦���֮�󣬿�ִ�����²�����ʹdevelop��mainͬ��
```shell
git checkout develop
git rebase develop
```

### 3.�ύ��¼����

#### �龰 
��ĳ��bug�޷��Ӵ����߼����ҵ�Դͷʱ��������Ҫͨ����������ʷ��¼���л�����֧�����о�׼��λ

#### ������� 
��ĳ��������������������´�����ⲿ���ύ�У����԰����ַ��Ĳ������δ�ѡ����Χ���м��ύ��¼�����𲽶�λ

#### �ؼ�����
```shell
# ���ύ��¼b0508b937e�д����·�֧review�����л���ȥ
git checkout -b review b0508b937e

# ����ǰ��֧��ǰ�ƽ���3a3fc3576b�����ύ��¼���������������ύ��¼
git merge 3a3fc3576be
```
�����bug���һЩ��β����
```shell
# �����bug��ɾ�������ķ�֧
git branch -d review

# ��д-DΪǿ��ɾ��
git branch -D review
```
