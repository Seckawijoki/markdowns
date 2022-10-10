## ˵��

�����ռ�һЩ���õ�bat�����������߱����ļ�������һЩ�����ĸ����Բ��������Կ���

### Ŀ¼
- [˵��](#˵��)
  - [Ŀ¼](#Ŀ¼)
  - [forѭ��index����˵��](#forѭ��index����˵��)
  - [dir��ƥ���](#dir��ƥ���)
  - [ʵ��](#ʵ��)
    - [1.��������](#1��������)
      - [�ݹ�����ļ�](#�ݹ�����ļ�)
      - [����һ���ļ����µ��ļ�](#����һ���ļ����µ��ļ�)
      - [�����������͵��ļ�](#�����������͵��ļ�)
      - [�ݹ�����ļ���](#�ݹ�����ļ���)
      - [����һ���ļ���](#����һ���ļ���)
      - [ʹ�ó�������](#ʹ�ó�������)
    - [2.�ļ�����](#2�ļ�����)
      - [��ȡ���·��](#��ȡ���·��)
      - [ƴ���ļ���](#ƴ���ļ���)
      - [��ȡ�ļ�ʱ��](#��ȡ�ļ�ʱ��)
    - [3.��ϲ���](#3��ϲ���)
      - [�ֲ�ʽ����](#�ֲ�ʽ����)
    - [4.�������������](#4�������������)
      - [TexturePacker��pngת����ktx](#texturepacker��pngת����ktx)
      - [��������svn mv����](#��������svn-mv����)
      - [�Զ���ֿ����svn cleanup](#�Զ���ֿ����svn-cleanup)
    - [5.������д����](#5������д����)
      - [��дxml�ļ�](#��дxml�ļ�)
      - [��дmarkdown�е������б�](#��дmarkdown�е������б�)
      - [����WinMerger�ļ�](#����winmerger�ļ�)
  - [VSCodeʹ��һ�����������п��ٱ�д](#vscodeʹ��һ�����������п��ٱ�д)

### forѭ��index����˵��
�����Ǳ�дforѭ�������ļ�ʱ����������%%i�Ŀɱ���ʽ���£�
- %%~i - ɾ������(\")������ %%i
- %%~fi - �� %%i ���䵽һ����ȫ�ϸ��·����
- %%~di - ���� %%i ���䵽һ����������
- %%~pi - ���� %%i ���䵽һ��·��
- %%~ni - ���� %%i ���䵽һ���ļ���
- %%~xi - ���� %%i ���䵽һ���ļ���չ��
- %%~si - �����·��ָ���ж���
- %%~ai - �� %%i ���䵽�ļ�����
- %%~ti - �� %%i ���䵽�ļ�������/ʱ��
- %%~zi - �� %%i ���䵽�ļ��Ĵ�С

��·����صı�ʽ�������Ƕ�����ϣ�
- %%~dpi ��ʾ�̷���·��������·��
- %%~dpni ��ʾ�̷���·�����ļ���������ʾ��׺
- %%~dpnxi ��ʾ�̷���·�����ļ�������ʾ��׺

### dir��ƥ���
- `*` �����ļ����ļ���
- `*.*` �������ļ�

### ʵ��

#### 1.��������

##### �ݹ�����ļ�
��ʹ��`for /f`��`for /r`������ʽ
`for /f`��ʽ��Ҫ��������Ŀ¼ʱ������ǰ�����`pushd`/`pop`���У�����dir�е�ƥ���ǰ����Ŀ¼·��
```bat
for /f %%i in ('dir /s /b *') do (
    echo %%i
)
```
`for /r`��ʽ��Ҫ��������Ŀ¼ʱ��ֻ�轫��Ŀ¼����`%cd%`�滻�ɶ�Ӧ��Ŀ¼·�����������
```bat
for /r %cd% %%i in (*) do (
    echo %%i
)
```

##### ����һ���ļ����µ��ļ�
��ʱ%%iֻ����ļ�����dir�еĲ���`/a-d`�ǽ��ļ����ų����⡣
```bat
for /f %%i in ('dir /b /a-d *') do (
    echo %%i
)
```

##### �����������͵��ļ�
��ͬʱ�ݹ�bat��cpp��sh��lua���������͵��ļ���
```bat
for /f %%i in ('dir /s /b f:\batch_files\*.bat, f:\cpp_files\*.cpp, f:\bash_files\*.sh, f:\lua_files\*.lua') do ( 
  echo %%i 
)
```

##### �ݹ�����ļ���
ʹ��`/ad`����ֻ�����ļ���
```bat
for /f %%i in ('dir /s /b /ad *') do (
    echo %%i
)
```

##### ����һ���ļ���
ʹ��`for /d`��ֱ�ӱ�����ǰ����Ŀ¼�µ�һ���ļ���
```bat
for /d %%i in (*) do (
    echo %%i
)
```
ʹ��`for /f`��ʽ��
```bat
for /f %%i in ('dir /b /ad *') do (
    echo %%i
)
```

##### ʹ�ó�������
�ϼ򵥵�һ�ַ���������д�������ض�·����ʹ��`,`��`�ո�`�ָ���Щ·����ֱ��ͨ��`for`ѭ�����б�����
���ַ������Ծ���ʹ�á�
```bat
@echo off
@REM �ǵÿ����ӳٱ���
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

#### 2.�ļ�����

##### ��ȡ���·��
> �滻��`=`ǰ�ı���������`%%`�������滻��ǰ��д����
```bat
@echo off
@REM �ǵÿ����ӳٱ���
setlocal EnableDelayedExpansion
for /r %cd% %%i in (*.*) do (
    set szPath=%%i
    set szRelPath=!szPath:%cd%\=!
    echo !szRelPath!
)
```

##### ƴ���ļ���
���ļ���ƴ�ӵ�һ�������У�Ȼ�����������̨����ȡƴ�ӽ����
���£�����batʹ��`";"`ƴ�ӡ�
��ƴ�Ӳ����У��轫`set`�ؼ��ʺ�����д���ʹ��˫���Ű�����
> ͬ��������չ��ƴ�Ӿ���·�������·������ƴ���ļ�����ƴ���ļ��е�
```bat
@echo off
setlocal EnableDelayedExpansion
for /f %%i in ('dir /s /b *.*') do (
    set "a=!a!;%%~nxi"
)
@REM ȥ����һ��;
set a=!a:~1!
echo !a!
```

##### ��ȡ�ļ�ʱ��
```bat
@echo off
setlocal EnableDelayedExpansion
for /r %cd% %%i in (*) do (
    for %%a in (%%i) do set fileDate=%%~ta
    echo %%i !fileDate!
)
```

#### 3.��ϲ���
##### �ֲ�ʽ����
���ҹ����д��ڵ�tolua.bat��������
```bat
for /f %%i in ('dir /s /b tolua.bat') do (
  pushd %%~dpi
  call tolua.bat
  popd
)
```

#### 4.�������������

##### TexturePacker��pngת����ktx
�ݹ����png�ļ���ʹ��TexturePacker����ktx��pvr��ת��
```bat
@echo off
setlocal EnableDelayedExpansion
@REM ���õ���������TexturePacker��·��
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
@REM ������ǰĿ¼���ڵ��ļ��У������Զ��庯������ת��
for /d %%i in (*) do (
    echo %%i
    call :function_transform_texture_set "%%i"
)
goto :end
@REM -----------------------------------------------------------------------
@REM ��pngת����ktx�Ĺ��ܷ�װ��һ������
:function_transform_texture_set
if "%~1" == "" goto :eof
if exist %~1.png del /q %~1.png
if exist %~1.xml del /q %~1.xml
@REM �����ļ���
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

##### ��������svn mv����
��������svn mv�������ܱ���svn���ύ��¼��
���£���editor�����е�ͷ�ļ���cpp�ļ���Ǩ�Ƶ�ui�ļ�����
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

##### �Զ���ֿ����svn cleanup
��ĳ̨������������˶����ͬ�Ĳֿ⣬�����н��о����Ե���ȥ����ͱ��빤������Ӳ���п��ܳ��ֲ���ʱ������jenkins������һ��svn cleanup�����棬�����ڼ��ٲֿ���ܴ�С
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

#### 5.������д����
##### ��дxml�ļ�
���磬�ݹ����������ļ����е�png�ļ��������·��д��Qt��qrc�����ļ���
> **ע��**��qrcʵ��Ϊxml�ļ����﷨�ϴ��ڴ�����"<"��">"���ţ�д���ļ�ʱ����Ҫ���"^"����ת��
```bat
@echo off
setlocal EnableDelayedExpansion
@REM ������Ҫ�����������ļ���
set aDirPaths=
set "aDirPaths=!aDirPaths!,ui\aiicons"
set "aDirPaths=!aDirPaths!,ui\items"
set "aDirPaths=!aDirPaths!,ui\rideicons"
set "aDirPaths=!aDirPaths!,ui\roleicons"
set aDirPaths=!aDirPaths:~1!
set fileOutput=res.qrc
if exist "!fileOutput!" del /q "!fileOutput!"
@REM ��һ��д���ļ�ʹ��">"��д��xml�ı�ǩͷ
(
  echo ^<RCC^>
  echo     ^<qresource prefix="/res"^>  
) > "!fileOutput!"
@REM �����ļ�д��
for %%p in (!aDirPaths!) do (
  set szDirPath=%%p
  @REM ��Windows���ļ��ָ���"\"�滻����б��"/"
  set szQrcPrefix=!szDirPath:\=/!
  for /f %%i in ('dir /b !szDirPath!\*.png') do (
    @REM д�����ã�������tab�������ʽ
    echo         ^<file^>!szQrcPrefix!/%%i^</file^> >> "!fileOutput!"
  )
)
@REM д��xml�ı�ǩβ
(  
  echo     ^</qresource^>
  echo ^</RCC^>
) >> "!fileOutput!"
```

##### ��дmarkdown�е������б�
�ݹ�����ļ�����д����Ҫ��ת���ļ�ӳ����markdown�е������б����к󣬴ӿ���̨������������markdown�С�
```bat
@echo off
setlocal EnableDelayedExpansion
for /r %%i in (*.lua) do (
  set szPath=%%i
  set szRelPath=!szPath:%cd%\=!
  echo * [%%~nxi]^(./!szRelPath!^)
)
```

##### ����WinMerger�ļ�
������Ŀ¼�ṹ��ͬ�Ĵ��͹���Ŀ¼����Ҫ�����Ա�ĳЩ�ļ��У����ֲ���Ҫ��ȫ���Ƚ�ʱ�����Ա�д�������ű����������ɣ�ͳһ�����WinMerge�ļ��󣬷����պ�ֱ��˫���򿪣��������鷳���϶��ļ������Աȡ�
```bat
@echo off
setlocal EnableDelayedExpansion
set OUTPUT_PATH=F:\WinMergeProjectsOfMiniWorld
@REM ָ���������̵�·��
set aEnvPaths=
set "aEnvPaths=!aEnvPaths!,C:\trunk\env1ed"
set "aEnvPaths=!aEnvPaths!,F:\trunk\env1oversea"
set aEnvPaths=!aEnvPaths:~1!
@REM ָ����ҪWinMerge�Ĳ����ļ���
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
@REM ��·���л�ȡ�ļ���
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
@REM ����WinMerge�ļ�
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
> ������������ԵĴ��룬�йر����ļ��Ķ������Լ���������ӳ������顢�����ַ������顢����Ȳ�ͬ���ܵ��ı�

### VSCodeʹ��һ�����������п��ٱ�д
ʹ��VSCodeʱ������ͨ������[Snippets](https://juejin.cn/post/6844903795663568910)�����һ�������Ч������߱�дЧ�ʡ�
�·�����[��������](#��������)�����ô��룬����ҷ��㸴��ʹ�ã�
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
		"description": "�ݹ�����ļ�"
	},

	"for_r_iterate_files_recursively" : {
		"scope": "bat",
		"prefix": "forrfr",
		"body": [
			"for /r %cd% %%i in (*) do (",
			"  echo %%i",
		  ")",
		],
		"description": "�ݹ�����ļ�"
	},

	"for_f_iterate_files" : {
		"scope": "bat",
		"prefix": "forff",
		"body": [
			"for /f %%i in ('dir /b *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "����һ���ļ�"
	},

	"for_f_iterate_dirs_recursively" : {
		"scope": "bat",
		"prefix": "forfdr",
		"body": [
			"for /f %%i in ('dir /s /b /ad *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "�ݹ�����ļ���"
	},

	"for_d_iterate_dirs" : {
		"scope": "bat",
		"prefix": "ford",
		"body": [
			"for /d %%i in (*) do (",
			"  echo %%i",
		  ")",
		],
		"description": "����һ���ļ���"
	},

	"for_d_iterate_dirs" : {
		"scope": "bat",
		"prefix": "forfd",
		"body": [
			"for /f %%i in ('dir /b /ad *') do (",
			"  echo %%i",
		  ")",
		],
		"description": "����һ���ļ���"
	},
}
```
> ��������Sublime�Ĺ��ߣ�Ҳ�������������������д