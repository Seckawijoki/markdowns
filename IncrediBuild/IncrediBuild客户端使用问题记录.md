## Ŀ¼
- [Ŀ¼](#Ŀ¼)
- [˵��](#˵��)
- [���л���](#���л���)
- [�����¼](#�����¼)
  - [1.���ϱ����CPU��������](#1���ϱ����cpu��������)
    - [����](#����)
    - [ȱ��](#ȱ��)
  - [2.�Լ��ĵ��Բ������ϱ����CPU����](#2�Լ��ĵ��Բ������ϱ����cpu����)
    - [��1�����ٱ��ز��������CPU](#1���ٱ��ز��������cpu)
    - [��2�����ٲ����������������CPU](#2���ٲ����������������cpu)
  - [3.�Զ����±�ȡ��](#3�Զ����±�ȡ��)
  - [4.��������exe�Ĺ�������Խ��Խ��](#4��������exe�Ĺ�������Խ��Խ��)
  - [5.��������](#5��������)
  - [6.��������IncrediBuild���Լ��Ļ��Ӳ���](#6��������incredibuild���Լ��Ļ��Ӳ���)
  - [7.�����Լ��Ļ��ӽ��б���](#7�����Լ��Ļ��ӽ��б���)
  - [8.�޷���������](#8�޷���������)
    - [����](#����)
    - [���](#���)
  - [9.VS��F5��ʼ���Ժ��ٴ���ʾ������Ŀ��Ҫ����](#9vs��f5��ʼ���Ժ��ٴ���ʾ������Ŀ��Ҫ����)
    - [����](#����-1)
    - [���](#���-1)
  - [10.���ӽ׶��޷�ֹͣ](#10���ӽ׶��޷�ֹͣ)
    - [�޷�����ļ���������](#�޷�����ļ�������)
      - [~~���´�Visual Studio~~](#���´�visual-studio)
      - [~~Restart IncrediBuild Agent Service~~](#restart-incredibuild-agent-service)
  - [11.Failed to start build](#11failed-to-start-build)

## ˵��
�����ռ�һЩ����������£��ͻ���������һЩ����Ľ�����������������£�

## ���л���
- Windows 10 Professional��IncrediBuild 9.6.6��Visual Studio 2019 Community
- �ش�C++��Ŀ

## �����¼

### 1.���ϱ����CPU��������

#### ����
��``�Ҽ�������ͼ��IncrediBuild Agent``->``Agent Settings``->``Visual Studio Builds``->``Advanced``�У��ر�``Limit concurrent PDB file instances to XX``��
ͬʱ����``Agent Settings``->``Initiator``->``General``->``CPU Allocation``�У���ѡ``Limit maximum number of cores utilized in build to``�������·���``Logical core(s)``��ɽϴ�ֵ��

> ���˵Ĺ����������н�100̨����i9�ĵĻ������õ�IncrediBuild��ÿ̨�����ṩ6~10��cores������£����õ�ֵ�ɸߵ�600~900������Ϊ���ڽ϶���ӿ��е�����£���������CPU�ӽ�400��

�ݴ˲�������ڲ�������ĵ����㹻�������£��ɴ�����������ϱ����CPU���������������������ٶȡ�

> ��ʾ��
> ��``�Ҽ�������ͼ��IncrediBuild Agent``->``Coordinator Monitor``�У��ɲ鿴��ǰ���ߵĻ����������Լ����������ṩ��CPU cores��

#### ȱ��
������ϱ������Ŀ��exeӦ�ó�����Ŀʱ��Ψһ�������ǣ��������ӽ׶Σ���ʹ�����쳣���٣��޷�������
�Ƽ�����Rebuild���һ�α��������¿����������ļ��޸��������١�û�������ļ���û����������ͷ�ļ�������£��رա�
``�ر�ʱ``���ɾ�ʵ�����������concurrent��������

### 2.�Լ��ĵ��Բ������ϱ����CPU����

#### ��1�����ٱ��ز��������CPU
��``Agent Settings``->``Agent``->``CPU Utilization``->``CPU Utilization - when acting as Initiator``�У���~~Single CPU, 10 Core Hyperthreading Enabled~~����Ϊ``User Defined``�������·���``Utilize 20 logical core(s)``�м��ֵ�ĵ�

#### ��2�����ٲ����������������CPU
��``Agent Settings``->``Agent``->``CPU Utilization``->``CPU Utilization - when acting as Helper``�У���ѡ``When acting as Helper utilize up to XX cores``��������һ�����ʵ�ֵ

### 3.�Զ����±�ȡ��
����������IncrediBuild���Զ����µĵ��������Լ�ǡ�������Cancel��ť��λ�ã���������ȡ����
����``�Ҽ�������ͼ��IncrediBuild Agent``�У������������ص�``Update Version``����������Զ����¡�

### 4.��������exe�Ĺ�������Խ��Խ��
����clean��������

### 5.��������
ż����������������������ӵ�exe������һ���緹�����ݻ����緹�������˰��Сʱ��2��Сʱ����û�������ꡣ
��Ҫ������ע���������

### 6.��������IncrediBuild���Լ��Ļ��Ӳ���
��װIncrediBuild

### 7.�����Լ��Ļ��ӽ��б���
���������Ҫ��ϵ����Ա������``License``

### 8.�޷���������

#### ����
����IB��Build�������µ��򣬵����޷����� 
> Maximum number of concurrent builds reached

#### ���
�������Խ����������Incredibuild��ĳ���������ڱ����еĴ���״̬��

### 9.VS��F5��ʼ���Ժ��ٴ���ʾ������Ŀ��Ҫ����

#### ����
����һ���ֶ��ж�IB������ٴ�����һ��IB��Build�������VS��F5������ʼ����ǰ����ʾ������Ŀ��Ҫ�ٴα��롣
��ʱ�ٴ��������ٴ�IB��Build��F5���ǻ���ʾ������Ŀ��Ҫ�ٴα��롣

#### ���
��ʱ��ҪIB��Rebuild��������Ŀ״̬��

### 10.���ӽ׶��޷�ֹͣ

�����������ɾ������IncrediBuild Build Helper

#### �޷�����ļ���������

##### ~~���´�Visual Studio~~
�ò���������ֹ���룬�ٴα���������������⣺
> Cannot create file: F:\sndbx\miniGame\Projects\vs2019-win32-Editor-develop\PredictedInputCache_MiniStudio_Debug_Win32.dat: ��һ����������ʹ�ô��ļ��������޷����ʡ� (32)
ɾ�����ļ�ʱ�����֪��IncrediBuild Build Systemռ�ö��޷�ɾ��

##### ~~Restart IncrediBuild Agent Service~~
�ò���Ҳ�����жϱ���

### 11.Failed to start build

��ʱ����IncrediBuild��VS���ٳ���10min������Ӧ��
���������Խ����    
![Failed to start build](./pic/failed_to_start_build.jpg "Failed to start build")