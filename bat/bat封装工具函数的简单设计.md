
> ����  
> [bat��������bat�ļ��еĺ�����ȡ����ֵ�ķ���](https://stackoverflow.com/questions/11481150/batch-file-call-sub-batch-file-to-pass-n-paramenters-and-return-without-use-of-f/25249059)

### ʾ��

������Android SDK��adb���ߵ�ʹ����Ϊ���ӡ�
�������������и�[functions_adb.bat](../tools/functions_adb.bat)��Ϊ�������bat�ļ��Ĵ��룬�����ܹ���װ�˻�ȡ���ӵ��豸�б������ļ�����װapk������app��4��������
```bat
@echo off
set exeAdb=F:\AndroidSDK\platform-tools\adb.exe
set szStoragePath=/storage/emulated/0
set szTargetPrePath=!szStoragePath!/Android/data
call %*
goto :end

setlocal EnableDelayedExpansion

:get_device_list
set aDevices=
for /f "tokens=1 skip=1 delims= " %%d in ('%exeAdb% devices -l') do (
  set szDevice=%%d
  @REM echo szDevice = !szDevice!
  set "aDevices=!aDevices! !szDevice!"
)
echo aDevices = !aDevices!
set "%1=!aDevices!"
goto :eof
@REM ======================================================================================================

:push_file
set "szDevice=%~1"
set "szSrcPath=%~2"
set "szDstPath=%~3"
if "!szSrcPath!" == "" goto :eof
set "szAdbCommand=%exeAdb% -s !szDevice!"
if "!szDevice!" == "" (
  set "szAdbCommand=%exeAdb%"
)
call !szAdbCommand! push !szSrcPath! !szStoragePath!/!szDstPath!/
goto :eof
@REM ======================================================================================================

:install_apk
set "szDevice=%~1"
set "szApkPath=%~2"
if "!szApkPath!" == "" goto :eof
set "szAdbCommand=%exeAdb% -s !szDevice!"
if "!szDevice!" == "" (
  set "szAdbCommand=%exeAdb%"
)
call !szAdbCommand! install -r "!szApkPath!"
goto :eof
@REM ======================================================================================================

:launch_app
set "szDevice=%~1"
set "szActivityPkg=%~2"
if "!szActivityPkg!" == "" goto :eof
set "szAdbCommand=%exeAdb% -s !szDevice!"
if "!szDevice!" == "" (
  set "szAdbCommand=%exeAdb%"
)
call !szAdbCommand! shell am start "!szActivityPkg!"
goto :eof
@REM ======================================================================================================

:end
exit /b
```

������裬������Ҫ��ȡ���ӵĶ����׿������Ȼ��ָ����apk��һ��װ�����������ϣ���������ôһ�δ��룺
```bat
@echo off
setlocal EnableDelayedExpansion
set "szApkPath=f:/myapp.apk"
set retDevices=
call functions_adb :get_device_list retDevices
for %%d in (!retDevices!) do (
  set szDevice=%%d
  call functions_adb :install_apk "!szDevice!" "!szApkPath!" 
  echo.
)
```
������Ҫ����`functions_adb.bat`�����ܷ���ʹ�ã����Һ������ܼ����������ĺ�����
ֻҪ�������·����`functions_adb.bat`·��һ�������ߵ���ʱƴ������·����

### ��β

���ڼ򵥵ĺ�����ƣ�����Ϊ�Լ�����һЩ�Ե���������ͨ�õķ�װ�������պ�Ĺ���ʹ�á�