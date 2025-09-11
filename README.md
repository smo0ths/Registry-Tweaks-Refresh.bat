# Registry-Tweaks-Refresh.bat v0.4.8
Windows 11 Registry Tweaks
#### this is what i use, make the bat file and run it often (after updates) and force the regs in log
#### %windir%\System32\SystemPropertiesProtection.exe (create restore point on protected drive)
#### use Autoruns64, OOSU10++ and uninstallers/registry cleaners to get all the other stuff

```python
@echo off
color 0A
mode con: cols=78 lines=18
setlocal EnableDelayedExpansion

set "log=%userprofile%\desktop\registry-tweaks-refresh.txt"
set "hour=%time:~0,2%"
set "min=%time:~3,2%"
set "sec=%time:~6,2%"
if "%hour:~0,1%"==" " set "hour=0%hour:~1,1%"
set /a hourInt=1%hour%-100
set "ampm=AM"
if %hourInt% GEQ 12 set "ampm=PM"
if %hourInt% GTR 12 set /a hourInt-=12
if %hourInt%==0 set hourInt=12
title %~nx0 - %date% %hourInt%:%min%:%sec% %ampm%

:restorechoice
set /P restorechoice=Y OPENS SYSTEM PROPERTIES SO YOU CAN CREATE A RESTORE POINT, N SKIPS [Y/N]:
if /I "%restorechoice%"=="Y" goto yesrestore
if /I "%restorechoice%"=="N" goto norestore 
goto invalidchoice

:yesrestore
echo [CLICK ON PROTECTED DRIVE AND CREATE] & timeout /nobreak /t 1 >nul & cls
"%windir%\System32\SystemPropertiesProtection.exe"
goto updatechoice

:norestore
echo [SKIPPING RESTORE POINT] & timeout /nobreak /t 1 >nul & cls
goto updatechoice

:updatechoice
set /P updatechoice=Y DISABLES WINDOWS UPDATES, N ENABLES/DEFAULTS [Y/N]:
if /I "%updatechoice%"=="Y" goto disableupdates
if /I "%updatechoice%"=="N" goto enableupdates
goto invalidchoice

:disableupdates
echo [DISABLING WINDOWS UPDATES 1 MANUALLY] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\wscsvc (set REG_DWORD 4) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto printerchoice

:enableupdates
echo [ENABLING WINDOWS UPDATES] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\wscsvc (set REG_DWORD 2) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto printerchoice

:printerchoice
set /P printerchoice=Y DISABLES PRINTER, N ENABLES/DEFAULTS [Y/N]:
if /I "%printerchoice%"=="Y" goto disableprinter
if /I "%printerchoice%"=="N" goto enableprinter
goto invalidchoice

:disableprinter
echo [DISABLING PRINTER] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto copilotchoice

:enableprinter
echo [ENABLING PRINTER] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto copilotchoice

:copilotchoice
set /P copilotchoice=Y DISABLES WINDOWS COPILOT, N ENABLES/DEFAULTS [Y/N]:
if /I "%copilotchoice%"=="Y" goto disablecopilot
if /I "%copilotchoice%"=="N" goto enablecopilot
goto invalidchoice

:disablecopilot
echo [DISABLING WINDOWS COPILOT] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto interestschoice

:enablecopilot
echo [ENABLING WINDOWS COPILOT] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto interestschoice

:interestschoice
set /P interestschoice=Y DISABLES NEWS AND INTERESTS, N ENABLES/DEFAULTS [Y/N]:
if /I "%interestschoice%"=="Y" goto disableinterests
if /I "%interestschoice%"=="N" goto enableinterests
goto invalidchoice

:disableinterests
echo [DISABLE NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT ON TASKBAR 2 MANUALLY] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced (set TaskbarDa REG_DWORD to 0) >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds (set ShellFeedsTaskbarViewMode REG_DWORD to 2) >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsRunInBackground" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableNotificationCenter" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnUserService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto touchchoice

:enableinterests
echo [ENABLE NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT ON TASKBAR 1 MANUALLY] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced (set TaskbarDa REG_DWORD to 1) >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds (set ShellFeedsTaskbarViewMode REG_DWORD to 1) >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsRunInBackground" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableNotificationCenter" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnService" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnUserService" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto touchchoice

:touchchoice
set /P touchchoice=Y DISABLES TOUCH SCREEN STUFF, N ENABLES/DEFAULTS [Y/N]:
if /I "%touchchoice%"=="Y" goto disabletouch
if /I "%touchchoice%"=="N" goto enabletouch
goto invalidchoice

:disabletouch
echo [DISABLE TOUCH SCREEN STUFF] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\Control Panel\Accessibility" /v "TouchFeedback" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "TouchGestureSetting" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "DisableNewKeyboardExperience" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableCompatibilityKeyboard" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableDesktopModeAutoInvoke" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "KeyboardLayoutPreference" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "UserKeyboardScalingFactor" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\TouchMode" /v "ShowTouchModeButton" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\PenWorkspace" /v "PenWorkspaceButtonDesiredVisibility" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\Flicks" /v "EnableFlicks" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\PenFeedback" /v "TurnOnPressAndHold" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Software\Microsoft\Wisp\Touch" /v "TouchGate" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\TabletTip\1.7" /v "TipbandDesiredVisibility" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ImmersiveShell" /v "TabletMode" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\PrecisionTouchPad" /v "EnableVirtualTouchpad" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "TurnOffTouchInput" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "3FingerTapAction" /t REG_DWORD /d 0x0000000 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "4FingerTapAction" /t REG_DWORD /d 0x0000000 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\TabletPC" /v "EnableTouch" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto bluechoice

:enabletouch
echo [ENABLE TOUCH SCREEN STUFF] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\Control Panel\Accessibility" /v "TouchFeedback" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "TouchGestureSetting" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "DisableNewKeyboardExperience" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableCompatibilityKeyboard" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableDesktopModeAutoInvoke" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "KeyboardLayoutPreference" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "UserKeyboardScalingFactor" /t REG_DWORD /d 100 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\TouchMode" /v "ShowTouchModeButton" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\PenWorkspace" /v "PenWorkspaceButtonDesiredVisibility" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\Flicks" /v "EnableFlicks" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\PenFeedback" /v "TurnOnPressAndHold" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Software\Microsoft\Wisp\Touch" /v "TouchGate" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\TabletTip\1.7" /v "TipbandDesiredVisibility" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ImmersiveShell" /v "TabletMode" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\PrecisionTouchPad" /v "EnableVirtualTouchpad" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "TurnOffTouchInput" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "3FingerTapAction" /t REG_DWORD /d 0x00000a6 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "4FingerTapAction" /t REG_DWORD /d 0x30115b27 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\TabletPC" /v "EnableTouch" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto bluechoice

:bluechoice
set /P bluechoice=Y DISABLES BLUETOOTH, N ENABLES/DEFAULTS [Y/N]:
if /I "%bluechoice%"=="Y" goto noblue
if /I "%bluechoice%"=="N" goto yesblue
goto invalidchoice

:noblue
echo [DISABLING BLUETOOTH] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto troublechoice

:yesblue
echo [ENABLING BLUETOOTH] & timeout /nobreak /t 1 >nul & cls   
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto troublechoice

:troublechoice
set /P troublechoice=Y DISABLES TROUBLESHOOTERS, N ENABLES/DEFAULTS [Y/N]:
if /I "%troublechoice%"=="Y" goto notrouble
if /I "%troublechoice%"=="N" goto yestrouble
goto invalidchoice

:notrouble
echo [DISABLING TROUBLESHOOTERS] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DiagTrack" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\dmwappushservice" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DPS" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\DPS (set REG_DWORD 4) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lfsvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost (set REG_DWORD 4) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost (set REG_DWORD 4) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WerSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto endscript

:yestrouble
echo [ENABLING TROUBLESHOOTERS] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DiagTrack" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\dmwappushservice" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DPS" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\DPS (set REG_DWORD 2) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lfsvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost (set REG_DWORD 3) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost (set REG_DWORD 3) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WerSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto endscript

:invalidchoice
echo Invalid command run bat again
goto endscript
:endscript

for /f %%A in ('"prompt $H & for %%B in (1) do rem"') do set "BS=%%A"
<nul set /p "=standby %username%"
for %%A in (. . .) do (
    <nul set /p "=%%A"
    timeout /nobreak /t 1 >nul
    for /l %%i in (1,1,0) do <nul set /p "=%BS%"
)
echo.

echo [TCPIP TWEAKS] & timeout /nobreak /t 1 >nul & cls
for /f "tokens=2 delims== " %%G in ('wmic nic where "NetEnabled=true" get GUID /value ^| find "="') do (
    set "guid=%%G"
    goto :gotguid
)
:gotguid
set "guid=%guid: =%"
if not defined guid goto :gotnext
set "regpath=HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\%guid%"
reg add "%regpath%" /v "DisableTaskOffload" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "%regpath%" /v "Tcp1323Opts" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "%regpath%" /v "TcpAckFrequency" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "%regpath%" /v "TcpDelAckTicks" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "%regpath%" /v "TcpNoDelay" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
goto :gotnext
:gotnext

:: echo [ENHANCED FULLSCREEN EXCLUSIVE/FULLSCREEN EXCLUSIVE DISABLED] & timeout /nobreak /t 1 >nul & cls
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [ENHANCED FULLSCREEN EXCLUSIVE/FULLSCREEN EXCLUSIVE ENABLED] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [DISABLES ALL IPV6 COMPONENTS] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" /v "DisabledComponents" /t REG_DWORD /d 255 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [WIFI STUFF] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowAutoConnectToWiFiSenseHotspots" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowWiFiHotSpotReporting" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WcmSvc\wifinetworkmanager\config" /v "AutoConnectAllowedOEM" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WiFi\WiFiTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [DISABLING SENSORS] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{09485F5A-759E-4A45-B622-5C7F2FCE985E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C5C4656-0E77-4F44-BF4C-8C0C8A76E8A7}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C7C1A12-3B7D-4B4B-9F4E-B1A1B6E0E1D8}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{76B5CE0A-17DD-414D-93A1-E127F40BDF6E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{B5C5BB9C-DAF8-4661-BB0F-5A4E3D8C6F58}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{BFA794E4-F964-4FDB-90F6-51056BFE4B44}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{C04D7B5C-9C2F-4C32-9C47-5C6E2F0E6D98}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensorService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [SMB STUFF] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v "AutoShareWks" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [MSEC DO MANUALLY] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SOFTWARE\Microsoft\Windows Defender (add REG_DWORD DisableAntiSpyware 1) >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SOFTWARE\Microsoft\Windows Defender (add REG_DWORD DisableAntiVirus 1)  >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService (set REG_DWORD 4) >> "%log%"

echo [MSEC] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\RemovalTools\MpGears" /v "HeartbeatTrackingIndex" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontOfferThroughWUAU" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontReportInfectionInformation" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "AllowFastServiceStartup" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableSpecialRunningModes" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "PUAProtection" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "ServiceKeepAlive" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\MpEngine" /v "MpEnablePus" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableBehaviorMonitoring" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableOnAccessProtection" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableRealtimeMonitoring" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableScanOnRealtimeEnable" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\SmartScreen" /v "ConfigureAppInstallControl" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "DisableBlockAtFirstSeen" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SpyNetReporting" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SubmitSamplesConsent" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableActivityFeed" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreen" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreenForStoreApps" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "PublishUserActivities" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "UploadUserActivities" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Sense" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WbioSrvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdBoot" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdFilter" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefsvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefusersvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WinDefend" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [DISABLING FUNCTION DISCOVERY FRAMEWORK (APPLE/SMART TV/ETC)] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\fdPHost" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FDResPub" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SSDPSRV" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "SUCCESS INFO" >> "%log%"

echo [CACHING/INDEXING FOR SLOW DRIVES DISABLED] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SysMain" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSearch" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [IPV6 STUFF DISABLED] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\6to4" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\iphlpsvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\IPHTTPS" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ISATAP" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteAccess" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Teredo" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [UNIVERSAL WINDOWS PLATFORM(UWP/APP MODEL)/STORE/BACKEND/FRAMEWORK] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppReadiness" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppXSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\camsvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CDPUserSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ClipSVC" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\InstallService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NcbService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\OneSyncSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StateRepository" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TimeBrokerSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TokenBroker" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\TrustedInstaller (set REG_DWORD 3) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrustedInstaller" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UdkUserSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UnistoreSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UserDataSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [VARIOUS DISABLED SERVICES] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BcastDVRUserService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CaptureService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\cbdhsvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DusmSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServer" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServerMonitor" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\jhi_service" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lmhosts" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MessagingService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Ndu" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NPSMSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\P9RdrService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PhoneSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PimIndexMaintenanceSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\QWAVE" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteRegistry" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RetailDemo" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RmSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StiSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StorSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UevAgentService" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\whesvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wlidsvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WManSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblAuthManager" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblGameSave" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxGipSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxNetApiSvc" /v "Start" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [VARIOUS SERVICES SET CORRECTLY] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BITS" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ConsentUxUserSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CryptSvc" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DispBrokerDesktopSvc" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DisplayEnhancementService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\EventLog" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FontCache" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Intel(R) TPM Provisioning Service" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\msiserver" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Netman" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\netprofm" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PcaSvc" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PlugPlay" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TextInputManagementService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrkWks" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
echo HKLM\SYSTEM\CurrentControlSet\Services\TrkWks (set REG_DWORD 2) >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\W32Time" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Winmgmt" /v "Start" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WMIRegistrationService" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\xboxgip" /v "Start" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [DISABLES OTHER WINDOWS AI STUFF] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsAADCloudSearchEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsCloudSearchEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsDeviceSearchHistoryEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsMSACloudSearchEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "SafeSearchMode" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Camera" /v "AllowCamera" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DisableTelemetryOptInSettingsUx" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "AllowRecallEnablement" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableAIDataAnalysis" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableCocreator" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableGenerativeFill" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableImageCreator" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetCopilotHardwareKey" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetDenyUriListForRecall" /t REG_SZ /d "*" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [DISABLE AUTOCORRECT/SPELLCHECK/SUGGESTIONS/PREDICTION/ECT] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\InputPersonalization\TrainedDataStore" /v "HarvestContacts" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableAutocorrection" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableSpellchecking" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Input\TIPC" /v "Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "DisableHwkbAutocorrection" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "DisableHwkbTextPrediction" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictCloudSuggestions" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictImplicitInkCollection" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictImplicitTextCollection" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v "NoLockScreen" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "AllowClipboardHistory" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "AllowCrossDeviceClipboard" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [CONTROL PANEL TWEAKS] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\Control Panel\Desktop" /v "AutoEndTasks" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "DragFullWindows" /t REG_SZ /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "DragHeight" /t REG_SZ /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "DragWidth" /t REG_SZ /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "MenuShowDelay" /t REG_DWORD /d 200 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "SmoothScroll" /t REG_SZ /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "WaitToKillAppTimeout" /t REG_SZ /d 3000 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Desktop\WindowMetrics" /v "MinAnimate" /t REG_SZ /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\Control Panel\Mouse" /v "MouseHoverTime" /t REG_DWORD /d 200 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [GAME TWEAKS] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AllowAutoGameMode" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AutoGameModeEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "GamePanelStartupTipIndex" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "ShowStartupPanel" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DXGIHonorFSEWindowsCompatible" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_HonorUserFSEBehaviorMode" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [CLASSIC RIGHT CLICK] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /ve /t REG_SZ /d "" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [EXPLORER TWEAKS] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "AutoCheckSelect" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DisableThumbnailCache" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DontPrettyPath" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "EnableBalloonTips" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "Hidden" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "HideFileExt" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "IconsOnly" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "LaunchTo" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ListviewAlphaSelect" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowInfoTip" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowSuperHidden" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowTaskViewButton" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarGlomLevel" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\OperationStatusManager" /v "EnthusiastMode" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Serialize" /v "StartupDelayInMSec" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects" /v "VisualFXSetting" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "SettingsPageVisibility" /t REG_SZ /d "hide:home" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FlyoutMenuSettings" /v "ShowHibernateOption" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoAutorun" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoDriveTypeAutoRun" /t REG_DWORD /d 255 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableSearchBoxSuggestions" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "HidePeopleBar" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "HideSCAMeetNow" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [HKCU SIUF] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "NumberOfSIUFInPeriod" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "PeriodInNanoSeconds" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [HKCU CURRENTVERSION] & timeout /nobreak /t 1 >nul & cls
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Accessibility" /v "Animation" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\ConsentStore\location" /v "Value" /t REG_SZ /d Deny /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "ContentDeliveryAllowed" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "FeatureManagementEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "OEMPreInstalledAppsEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEverEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SilentInstalledAppsEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SoftLandingEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-310093Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-314559Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-314563Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338387Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338388Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338389Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338393Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353694Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353696Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353698Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-88000326Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContentEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SystemPaneSuggestionsEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsOneDriveResumeAllowed" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsResumeAllowed" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AppCaptureEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "BingSearchEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CanCortanaBeEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "DeviceHistoryEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "HistoryViewEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v "EnableTransparency" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\UserProfileEngagement" /v "ScoobeSystemSettingEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Windows Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [HKLM MICROSOFT] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Browser\AllowSmartScreen" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowCortana" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableAppInstallControl" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableSmartScreenInShell" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\PreventOverrideForFilesInShell" /v "Value" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "NetworkThrottlingIndex" /t REG_DWORD /d 4294967295 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "SystemResponsiveness" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AppHost" /v "EnableWebContentEvaluation" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "DODownloadMode" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "SystemSettingsDownloadMode" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Device Metadata" /v "PreventDeviceMetadataFromNetwork" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableFirstLogonAnimation" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableLUA" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\ScheduledDiagnostics" /v "EnabledExecution" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Reporting" /v "Disabled" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [HKLM POLICIES] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoActiveHelp" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoExplicitFeedback" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Biometrics" /v "Enabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\PushToInstall" /v "DisablePushToInstall" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CEIPEnable" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CorporateSQMURL" /t REG_SZ /d 0.0.0.0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient\Windows" /v "CEIPEnable" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\CurrentVersion\SOFTWARE Protection Platform" /v "NoGenTicket" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v "EnableMulticast" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AdvertisingInfo" /v "DisabledByGroupPolicy" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "AITEnable" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisableInventory" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisablePCA" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "ConfigureWindowsSpotlight" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableCloudOptimizedContent" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableConsumerAccountStateContent" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableSoftLanding" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableTailoredExperiencesWithDiagnosticData" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableThirdPartySuggestions" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsConsumerFeatures" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightFeatures" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnActionCenter" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnSettings" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightWindowsWelcomeExperience" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "IncludeEnterpriseSpotlight" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CredUI" /v "DisablePasswordReveal" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\PushNotifications" /v "NoTileApplicationNotification" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\FileHistory" /v "Disabled" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\HandwritingErrorReports" /v "PreventHandwritingErrorReports" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocation" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocationScripting" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableSensors" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableWindowsLocationProvider" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "AllowBuildPreview" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "EnableConfigFlighting" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSync" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSyncUserOverride" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "PreventHandwritingDataSharing" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Chat" /v "ChatIcon" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Reporting" /v "Disabled" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowCortana" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowSearchToUseLocation" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchPrivacy" /t REG_DWORD /d 3 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWeb" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWebOverMeteredConnections" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableSearch" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableWebSearch" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ManagePreviewBuilds" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AUOptions" /t REG_DWORD /d 2 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AutoInstallMinorUpdates" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "DetectionFrequencyEnabled" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoRebootWithLoggedOnUsers" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallDay" /t REG_DWORD /d 4 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallTime" /t REG_DWORD /d 20 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "UseWUServer" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\WMDRM" /v "DisableOnline" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [HKLM CONTROL] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\CurrentControlSet\Control\CI\Policy" /v "VerifiedAndReputablePolicyState" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\FileSystem" /v "LongPathsEnabled" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerThrottling" /v "PowerThrottlingOff" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "IRQ8Priority" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowFullControl" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowToGetHelp" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v "ClearPageFileAtShutdown" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Power" /v "HibernateEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\AutoLogger-Diagtrack-Listener" /v "Start" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\SQMLogger" /v "Start" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [MAPS] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\Maps" /v "AutoUpdateEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [AllowUpgradesWithUnsupportedTPMOrCPU] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SYSTEM\Setup\MoSetup" /v "AllowUpgradesWithUnsupportedTPMOrCPU" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [EDGE STUFF] & timeout /nobreak /t 1 >nul & cls
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AlternateErrorPagesEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AutofillAddressEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AutofillCreditCardEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "BackgroundModeEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "BlockThirdPartyCookies" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "ConfigureDoNotTrack" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "DiagnosticData" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "EnhanceSecurityMode" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "HideFirstRunExperience" /t REG_DWORD /d 1 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "NewTabPagePrerenderEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "PasswordManagerEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "PrelaunchEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SearchSuggestEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "CreateDesktopShortcutDefault" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "UpdateDefault" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\Main" /v "AllowPrelaunch" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\PhishingFilter" /v "EnabledV9" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\TabPreloader" /v "AllowTabPreloading" /t REG_DWORD /d 0 /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\EdgeUpdate" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
for /f "tokens=2 delims=:" %%T in ('schtasks /query /fo LIST /v ^| findstr /i "TaskName:" ^| findstr /i "MicrosoftEdgeUpdateTaskMachine"') do (
    set "raw=%%T"
    set "task=!raw:~1!"
    set "task=!task: =!"
    schtasks /change /tn "!task!" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
)

echo [SCHTASKS CANNOT DISABLE] & timeout /nobreak /t 1 >nul & cls
schtasks /change /tn "\Microsoft\Windows\Shell\UpdateUserPictureTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Report policies" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Schedule Scan" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Start Oobe Expedite Work" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\StartOobeAppsScan_LicenseAccepted" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\StartOobeAppsScanAfterUpdate" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\UIEOrchestrator" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\USO_UxBroker" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\UUS Failover Task" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [SCHTASKS] & timeout /nobreak /t 1 >nul & cls
schtasks /change /tn "\Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Application Experience\PcaPatchDbTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Application Experience\StartupAppTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\ApplicationData\DsSvcCleanup" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Autochk\Proxy" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Clip\License Validation" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\CloudExperienceHost\CreateObjectTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\Consolidator" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UnifiedConsentSyncTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataFlushing" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataReceiver" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataReporting" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsbCeip" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Diagnosis\RecommendedTroubleshootingScanner" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Diagnosis\Scheduled" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskFootprint\Diagnostics" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Feedback\Siuf\DmClientOnScenarioDownload" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\FileHistory\File History (maintenance mode)" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\License Manager\TempSignedLicenseExchange" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Maintenance\WinSAT" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Maps\MapsToastTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\NetworkDiagnostics\BfeOnServiceStartTypeChange" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\NetworkDiagnostics\ResolutionHost" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\PI\Sqm-Tasks" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\PowerGridForecastTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\SustainabilityTelemetry" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Printing\PrinterCleanupTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Printing\PrintJobCleanupTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\PushToInstall\LoginCheck" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\PushToInstall\Registration" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\RemoteAssistance\RemoteAssistanceTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Servicing\StartComponentCleanup" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyMonitor" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyRefreshTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\IndexerAutomaticMaintenance" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\NotificationTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\ThemesSyncedImageDownload" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\XblGameSaveTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Speech\SpeechModelDownloadTask" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Subscription\EnableLicenseAcquisition" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Subscription\LicenseAcquisition" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Time Synchronization\ForceSynchronizeTime" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\TPM\Tpm-Maintenance" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UPnP\UPnPHostConfig" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\USB\Usb-Notifications" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WaaSMedic\MaintenanceWork" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cache Maintenance" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cleanup" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Scheduled Scan" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Verification" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Reporting\QueueReporting" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WlanSvc\CDSSync" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WwanSvc\OobeDiscovery" /disable 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [UPDATE DELETES] & timeout /nobreak /t 1 >nul & cls
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\ClipchampUpdate" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\DevHomeUpdate" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OfficeHubUpdate" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OneDriveSetup" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OutlookUpdate" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\TeamsMachineInstaller" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\WebView2RuntimeUpdate" /f 2>&1 | findstr /v /i "ERROR SUCCESS INFO" >> "%log%"

echo [AFTER UPDATES/REPAIRS RUN AGAIN, CHECK registry-tweaks-refresh.txt FORCE LOG ENTRIES IN A PROGRAM LIKE RegCool.exe] >> "%log%"
echo [AFTER UPDATES/REPAIRS RUN AGAIN, CHECK registry-tweaks-refresh.txt FORCE LOG ENTRIES IN A PROGRAM LIKE RegCool.exe]
echo [REBOOT AFTER] >> "%log%"
echo [REBOOT AFTER]

powershell -c "(New-Object Media.SoundPlayer 'C:\Windows\Media\Ring05.wav').PlaySync()"
taskkill /f /im explorer.exe && start explorer.exe
endlocal
pause >nul
```
