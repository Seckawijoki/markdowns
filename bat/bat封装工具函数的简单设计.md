
> 引用  
> [bat调用其它bat文件中的函数获取返回值的方法](https://stackoverflow.com/questions/11481150/batch-file-call-sub-batch-file-to-pass-n-paramenters-and-return-without-use-of-f/25249059)

### 示例

现在以Android SDK的adb工具的使用作为例子。
我们这里首先有个[functions_adb.bat](../tools/functions_adb.bat)作为工具类的bat文件的代码，里面总共封装了获取连接的设备列表、推送文件、安装apk、启动app共4个函数：
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

而后假设，我们需要获取连接的多个安卓机器，然后将指定的apk逐一安装到各个机子上，可以有这么一段代码：
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
我们需要保存`functions_adb.bat`，就能方便使用，而且后续还能继续添加所需的函数。
只要上面这段路径与`functions_adb.bat`路径一样，或者调用时拼接它的路径。

### 结尾

基于简单的函数设计，可以为自己保存一些对第三方工具通用的封装，方便日后的工作使用。