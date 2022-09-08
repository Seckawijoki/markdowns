> ##### ���л�����
> Windows 10 רҵ��


# if_else����ѡ�����������

## Ŀ¼
- [1.��������Ƕ��](#1.��������Ƕ��)  
- [2.�����߼����Ķ�ѡ�������](#2.�����߼����Ķ�ѡ�������)  

## 1.��������Ƕ��
### 1.a

������һ�μ򵥵ĸ���ѡ����䣬���޷�ͨ�����еġ�

![if_else_error.png](./pic/if_else/if_else_error.png)

### 1.b
������Ϊÿ�������� **()** С����ʱ������Ȼ���޷��������С�

![if_else_error2.png](./pic/if_else/if_else_error2.png)

### 1.c
������Ϊ if �� else �������ִ����䣬ֻ����һ����俴����
```bat
if %option% == 3 ( echo three ) else if %option% == 2 ( echo two ) else if %option% == 1 ( echo one ) else ( echo zero )
```

![if_else_one_line.png](./pic/if_else/if_else_one_line.png)

### 1.d
> ʵ�ʱ���У�������ǵ�����ϴ�ѡ��ࡢ�����볤ʱ������if_elseǶ�����д����һ�У���Ӱ���˿ɶ��ԣ�Ҳ�����ά���Ѷȡ����ǲ�����ܱ�д����һ�д��롣

���ڲ����˻��У�������Ҫ��ÿ������ִ������β�� **��**������� **^**���Գ䵱���е����á�
```bat
set option=2
if %option% == 3 ( echo three ) ^
else if %option% == 2 ( echo two ) ^
else if %option% == 1 ( echo one ) ^
else ( echo zero )
```
![if_else_error3.png](./pic/if_else/if_else_error3.png)

## 2.�����߼����Ķ�ѡ�������
### 2.a
����ͨ����� **^** ����ʽ��ȷʵ����˶��Ƕ�׵����⡣���Ƕ��ڶ�������ִ����������ǲ�ͬ��
���ǿ��Կ���������ڶ��е���������Ѻ���ĸ�ֵ��䣬���ַ�������ˡ�����Ǹ����ӵ��﷨�������������е�һ�������echo���ͻῴ���﷨����
![if_else_error4.png](./pic/if_else/if_else_error4.png)

��ʱ��������Ҫ�����������б�д�����ܱ�֤�������ȷ���С�
����������ܻ�����ϤC/C++���ϰ�ߵ����ѣ����д����if_else��ʽ����䣺
```C
if (option == 2) 
{
  //
}
else if (option == 1)
{
  //
}
else 
{
  //
}
```
�ֻ�����Java�ı��ϰ�ߵ���ʽ��
```Java
if (option == 2) {
  //
} else if (option == 1) {
  //
} else {
  //
}
```
### 2.b
�����ȿ���C/C++��дϰ�ߵ�if_else��䣺
�������е���һ�����������Ѿ������������Ϊ if ������Ĳ�������Ϊһ����䡣
![if_else_error5.png](./pic/if_else/if_else_error5.png)
### 2.c
������ÿ��if����������в����� **^** �����ǲ����ܵõ���Ҫ�����н����
�ҵ�һ������ǣ����в�����ֻ����һ�����ǰ�õ�ͬ�У����ڶ�����һ��û����䣬�����ճ����߼��������С���ʱ�ͳ���������һ����C/C++��Java������û���ֹ���һ�������**Ƕ����if_else����м����䱻ִ���ˡ�**�۲��������ǿ��Կ���ÿ��echo��䶼��ִ�У�����ִ�е�4��sum��ֵ����ʾ�ľ������һ�θ�ֵΪ0��ִ�н����**����bat�ű��ĵ�һ����������﷨�ص㡣**
������в����������յ�һ����䣬����ִ������ȷ����䣬�ҵ���elseʱ���жϵ�ǰ����if��䣬������Ϊ���﷨���󣬾ͼ�������ִ�С�
![if_else_error6.png](./pic/if_else/if_else_error6.png)
### 2.d
����������в������Ķ�����û��ʶ������������߼���һ������顣���ǳ���ֱ�ӽ�ÿ���������������������� **(** ǰ�õ���if���ͬһ�еĺ��棬����ȴ�õ�����ȷ�Ľ����
```bat
setlocal EnableDelayedExpansion
set option=2
set sum=-1
if %option% == 3 ( 
  echo three 
  set /a sum=%option%*%option%*%option%
) ^
else if %option% == 2 ( 
  echo two 
  set /a sum=2*%option%
) ^
else if %option% == 1 ( 
  echo one 
  set /a sum=%option% 
) ^
else ( 
  echo zero 
  set /a sum=0 
)
echo sum = !sum!
```
![if_else_error7.png](./pic/if_else/if_else_error7.png)

���ˣ�����ȷʵ�õ�����ȷ�����н�����������ǻ�����Ҫ��֤������C/C++���ϰ�߸�ʽ����䡣���˵�2.c�Ĳ����ϣ��������޸ġ�
�������ų������޸ĵĿ��ܣ�
> * �����ź�������в����� **^** ����ɾ�������ǵ�1���ó��Ľ��ۡ�
> * ������ÿһ�д��������в����� **^** ������2.a�Ĵ��������ȡ�

### 2.e
��ÿһ�д��������в�������
���۲����룬�����Ĳ���������һ����������Ĳ�����
![if_else_error8.png](./pic/if_else/if_else_error8.png)

### 2.f
������Ļ����ϣ���ÿ�������� **(** ��������в�������
���۸������룬û��һ�������ִ�е���
![if_else_error9.png](./pic/if_else/if_else_error9.png)

### 2.g
��2.c�Ļ����ϣ�ֻ����ÿ�������� **(** ��������в������Ķ�����
������Ȼ�Ǵ���ġ�
![if_else_error10.png](./pic/if_else/if_else_error10.png)

#��ȷ��д��

> ���ˣ�����û�еõ�����C/C++��дϰ�ߵ�if_else��ʽ����ȷд����������2.d�����Ľ��ۣ�
- **if** ����������� **(** ͬ��
- ������ **)**  ���� **else if** ͬ��
- ������ **)** ����������в����� **^**
```bat
setlocal EnableDelayedExpansion
set option=2
set sum=-1
if %option% == 3 ( 
  echo three 
  set /a sum=%option%*%option%*%option%
) ^
else if %option% == 2 ( 
  echo two 
  set /a sum=2*%option%
) ^
else if %option% == 1 ( 
  echo one 
  set /a sum=%option% 
) ^
else ( 
  echo zero 
  set /a sum=0 
)
echo sum = !sum!
```
### 2.h.Java��дϰ�ߵ�д��
����Ҳ������ؿ��������ֱ�дϰ��Ҳ����ȷ�ġ�
```bat
setlocal EnableDelayedExpansion
set option=2
set sum=-1
if %option% == 3 ( 
  echo three 
  set /a sum=%option%*%option%*%option% 
) else if %option% == 2 ( 
  echo two 
  set /a sum=2*%option% 
) else if %option% == 1 ( 
  echo one 
  set /a sum=%option% 
) else ( 
  echo zero 
  set /a sum=0 
)
echo sum = !sum!
```
![if_else.png](./pic/if_else/if_else.png)