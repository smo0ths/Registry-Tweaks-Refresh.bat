# Registry-Tweaks-Refresh.bat v0.5.3
Windows 11 Registry Tweaks
#### this is what i use, make the bat file and run it often (after updates) and force the regs in log
#### %windir%\System32\SystemPropertiesProtection.exe (create restore point on protected drive, code will prompt you)

```python
@echo off
color 0A
:: mode con: cols=80 lines=20
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

:AUTOUpdatechoice
set /P AUTOUpdatechoice=Y JUST STOP/DISABLE wuauserv(Windows Update)/UsoSvc(Update Orchestrator), N SKIPS [Y/N]:
if /I "%AUTOUpdatechoice%"=="Y" goto yesAUTO
if /I "%AUTOUpdatechoice%"=="N" goto noAUTO
goto invalidchoice

:yesAUTO
echo "RE-DISABLING wuauserv(Windows Update)/UsoSvc(Update Orchestrator)"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
net stop UsoSvc >nul 2>&1
net stop wuauserv >nul 2>&1
goto restorechoice

:noAUTO
echo "SKIPPING wuauserv(Windows Update)/UsoSvc(Update Orchestrator) stop/disable"
goto restorechoice

:restorechoice
set /P restorechoice=Y OPENS SYSTEM PROPERTIES SO YOU CAN CREATE A RESTORE POINT, N SKIPS [Y/N]:
if /I "%restorechoice%"=="Y" goto yesrestore
if /I "%restorechoice%"=="N" goto norestore 
goto invalidchoice

:yesrestore
echo "CLICK ON PROTECTED DRIVE AND CREATE"
"%windir%\System32\SystemPropertiesProtection.exe"
goto updatechoice

:norestore
echo "SKIPPING RESTORE POINT"
goto updatechoice

:updatechoice
set /P updatechoice=Y DISABLES WINDOWS UPDATES, N ENABLES/DEFAULTS [Y/N]:
if /I "%updatechoice%"=="Y" goto disableupdates
if /I "%updatechoice%"=="N" goto enableupdates
goto invalidchoice

:disableupdates
echo "DISABLING WINDOWS UPDATES (1 MANUALLY)"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\AppID\VerifiedPublisherCertStoreCheck" /disable >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\InstallService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
goto printerchoice

:enableupdates
echo "ENABLING WINDOWS UPDATES"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo AppIDSvc starttype= all >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\AppID\VerifiedPublisherCertStoreCheck" /enable >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\InstallService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo UsoSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo wuauserv starttype= all >nul 2>&1
goto printerchoice

:printerchoice
set /P printerchoice=Y DISABLES PRINTER, N ENABLES/DEFAULTS [Y/N]:
if /I "%printerchoice%"=="Y" goto disableprinter
if /I "%printerchoice%"=="N" goto enableprinter
goto invalidchoice

:disableprinter
echo "DISABLING PRINTER"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
goto copilotchoice

:enableprinter
echo "ENABLING PRINTER"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo PrintWorkflowUserSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
goto copilotchoice

:copilotchoice
set /P copilotchoice=Y DISABLES WINDOWS COPILOT, N ENABLES/DEFAULTS [Y/N]:
if /I "%copilotchoice%"=="Y" goto disablecopilot
if /I "%copilotchoice%"=="N" goto enablecopilot
goto invalidchoice

:disablecopilot
echo "DISABLING WINDOWS COPILOT"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
goto interestschoice

:enablecopilot
echo "ENABLING WINDOWS COPILOT"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
goto interestschoice

:interestschoice
set /P interestschoice=Y DISABLES NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT, N ENABLES/DEFAULTS [Y/N]:
if /I "%interestschoice%"=="Y" goto disableinterests
if /I "%interestschoice%"=="N" goto enableinterests
goto invalidchoice

:disableinterests
echo "DISABLING NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT ON TASKBAR (2 MANUALLY)"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 0 /f >nul 2>&1
echo "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced (set TaskbarDa REG_DWORD to 0)" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 2 /f >nul 2>&1
echo "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds (set ShellFeedsTaskbarViewMode REG_DWORD to 2)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsRunInBackground" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableNotificationCenter" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 0 /f >nul 2>&1
goto touchchoice

:enableinterests
echo "ENABLING NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT ON TASKBAR (1 MANUALLY)"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 1 /f >nul 2>&1
echo "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced (set TaskbarDa REG_DWORD to 1)" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 1 /f >nul 2>&1
echo "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds (set ShellFeedsTaskbarViewMode REG_DWORD to 1)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsRunInBackground" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableNotificationCenter" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 1 /f >nul 2>&1
goto touchchoice

:touchchoice
set /P touchchoice=Y DISABLES TOUCH SCREEN STUFF, N ENABLES/DEFAULTS [Y/N]:
if /I "%touchchoice%"=="Y" goto disabletouch
if /I "%touchchoice%"=="N" goto enabletouch
goto invalidchoice

:disabletouch
echo "DISABLE TOUCH SCREEN STUFF"
reg add "HKCU\Control Panel\Accessibility" /v "TouchFeedback" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "TouchGestureSetting" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "DisableNewKeyboardExperience" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableCompatibilityKeyboard" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableDesktopModeAutoInvoke" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "KeyboardLayoutPreference" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "UserKeyboardScalingFactor" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\TouchMode" /v "ShowTouchModeButton" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\PenWorkspace" /v "PenWorkspaceButtonDesiredVisibility" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\Flicks" /v "EnableFlicks" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\PenFeedback" /v "TurnOnPressAndHold" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\Software\Microsoft\Wisp\Touch" /v "TouchGate" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\TabletTip\1.7" /v "TipbandDesiredVisibility" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ImmersiveShell" /v "TabletMode" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\PrecisionTouchPad" /v "EnableVirtualTouchpad" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "TurnOffTouchInput" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "3FingerTapAction" /t REG_DWORD /d 0x0000000 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "4FingerTapAction" /t REG_DWORD /d 0x0000000 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\TabletPC" /v "EnableTouch" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
goto bluechoice

:enabletouch
echo "ENABLE TOUCH SCREEN STUFF"
reg add "HKCU\Control Panel\Accessibility" /v "TouchFeedback" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "TouchGestureSetting" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "DisableNewKeyboardExperience" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableCompatibilityKeyboard" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableDesktopModeAutoInvoke" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "KeyboardLayoutPreference" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "UserKeyboardScalingFactor" /t REG_DWORD /d 100 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\TouchMode" /v "ShowTouchModeButton" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\PenWorkspace" /v "PenWorkspaceButtonDesiredVisibility" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\Flicks" /v "EnableFlicks" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\PenFeedback" /v "TurnOnPressAndHold" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\Software\Microsoft\Wisp\Touch" /v "TouchGate" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\TabletTip\1.7" /v "TipbandDesiredVisibility" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ImmersiveShell" /v "TabletMode" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\PrecisionTouchPad" /v "EnableVirtualTouchpad" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "TurnOffTouchInput" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "3FingerTapAction" /t REG_DWORD /d 0x00000a6 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "4FingerTapAction" /t REG_DWORD /d 0x30115b27 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\TabletPC" /v "EnableTouch" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo PenService starttype= all >nul 2>&1
goto bluechoice

:bluechoice
set /P bluechoice=Y DISABLES BLUETOOTH, N ENABLES/DEFAULTS [Y/N]:
if /I "%bluechoice%"=="Y" goto noblue
if /I "%bluechoice%"=="N" goto yesblue
goto invalidchoice

:noblue
echo "DISABLING BLUETOOTH"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTAGService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthav" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHPORT" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHUSB" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DeviceAssociationService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicePickerUserSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicesFlowUserSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
goto troublechoice

:yesblue
echo "ENABLING BLUETOOTH"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTAGService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo BTAGService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthav" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo bthav starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHPORT" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo BTHPORT starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHUSB" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo BTHUSB starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DeviceAssociationService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo DeviceAssociationService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicePickerUserSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo DevicePickerUserSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicesFlowUserSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo DevicesFlowUserSvc starttype= all >nul 2>&1
goto troublechoice

:troublechoice
set /P troublechoice=Y DISABLES TROUBLESHOOTERS, N ENABLES/DEFAULTS [Y/N]:
if /I "%troublechoice%"=="Y" goto notrouble
if /I "%troublechoice%"=="N" goto yestrouble
goto invalidchoice

:notrouble
echo "DISABLING TROUBLESHOOTERS"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DiagTrack" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\dmwappushservice" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DPS" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\DPS (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lfsvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WerSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
goto endscript

:yestrouble
echo "ENABLING TROUBLESHOOTERS"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DiagTrack" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\dmwappushservice" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo dmwappushservice starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DPS" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\DPS (set REG_DWORD 2)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lfsvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo lfsvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost (set REG_DWORD 3)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost (set REG_DWORD 3)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WerSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo WerSvc starttype= all >nul 2>&1
goto endscript

:invalidchoice
echo "INVALID COMMAND RUN BAT AGAIN"
goto endscript
:endscript

:: standby
for /f %%A in ('"prompt $H & for %%B in (1) do rem"') do set "BS=%%A"
<nul set /p "=standby %username%"
for %%A in (. . .) do (
    <nul set /p "=%%A"
    timeout /nobreak /t 1 >nul
    for /l %%i in (1,1,0) do <nul set /p "=%BS%"
)
echo.

echo "TCPIP TWEAKS"
for /f "tokens=2 delims== " %%G in ('wmic nic where "NetEnabled=true" get GUID /value ^| find "="') do (
    set "guid=%%G"
    goto :gotguid
)
:gotguid
set "guid=%guid: =%"
if not defined guid goto :gotnext
set "regpath=HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\%guid%"
reg add "%regpath%" /v "DisableTaskOffload" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "%regpath%" /v "Tcp1323Opts" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "%regpath%" /v "TcpAckFrequency" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "%regpath%" /v "TcpDelAckTicks" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "%regpath%" /v "TcpNoDelay" /t REG_DWORD /d 1 /f >nul 2>&1
goto :gotnext
:gotnext

:: echo "ENHANCED FULLSCREEN EXCLUSIVE/FULLSCREEN EXCLUSIVE DISABLED"
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 0 /f >nul 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 2 /f >nul 2>&1

echo "ENHANCED FULLSCREEN EXCLUSIVE/FULLSCREEN EXCLUSIVE ENABLED"
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 0 /f >nul 2>&1

echo "DISABLES ALL IPV6 COMPONENTS"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" /v "DisabledComponents" /t REG_DWORD /d 255 /f >nul 2>&1

echo "WIFI STUFF"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowAutoConnectToWiFiSenseHotspots" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowWiFiHotSpotReporting" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WcmSvc\wifinetworkmanager\config" /v "AutoConnectAllowedOEM" /t REG_DWORD /d 0 /f >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\WiFi\WiFiTask" /disable >nul 2>&1

echo "SENSORS STUFF"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{09485F5A-759E-4A45-B622-5C7F2FCE985E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C5C4656-0E77-4F44-BF4C-8C0C8A76E8A7}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C7C1A12-3B7D-4B4B-9F4E-B1A1B6E0E1D8}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{76B5CE0A-17DD-414D-93A1-E127F40BDF6E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{B5C5BB9C-DAF8-4661-BB0F-5A4E3D8C6F58}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{BFA794E4-F964-4FDB-90F6-51056BFE4B44}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{C04D7B5C-9C2F-4C32-9C47-5C6E2F0E6D98}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensorDataService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo SensorDataService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensorService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo SensorService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensrSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo SensrSvc starttype= all >nul 2>&1

echo "SMB STUFF"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v "AutoShareWks" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1

echo "DISABLING MSEC (3 MANUALLY)"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f >nul 2>&1
echo "HKLM\SOFTWARE\Microsoft\Windows Defender (set REG_DWORD DisableAntiSpyware 1 ~ turn off in Windows Security app)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 1 /f >nul 2>&1
echo "HKLM\SOFTWARE\Microsoft\Windows Defender (set REG_DWORD DisableAntiVirus 1 ~ turn off in Windows Security app)"  >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService (set REG_DWORD 4)" >> "%log%"

echo "DISABLING MSEC"
reg add "HKLM\SOFTWARE\Microsoft\RemovalTools\MpGears" /v "HeartbeatTrackingIndex" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontOfferThroughWUAU" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontReportInfectionInformation" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "AllowFastServiceStartup" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableSpecialRunningModes" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "PUAProtection" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "ServiceKeepAlive" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\MpEngine" /v "MpEnablePus" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableBehaviorMonitoring" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableOnAccessProtection" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableRealtimeMonitoring" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableScanOnRealtimeEnable" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\SmartScreen" /v "ConfigureAppInstallControl" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "DisableBlockAtFirstSeen" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SpyNetReporting" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SubmitSamplesConsent" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableActivityFeed" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreen" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreenForStoreApps" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "PublishUserActivities" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "UploadUserActivities" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Sense" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WbioSrvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo WbioSrvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdBoot" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdFilter" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefsvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefusersvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo webthreatdefusersvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WinDefend" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
echo "DISABLING FUNCTION DISCOVERY FRAMEWORK (APPLE/SMART TV/ETC)"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\fdPHost" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FDResPub" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo FDResPub starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SSDPSRV" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
echo "CACHING/INDEXING FOR SLOW DRIVES DISABLED"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SysMain" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSearch" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1

echo "CORE IPV6 IPHLPSVC DISABLED"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\iphlpsvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1

echo "UNIVERSAL WINDOWS PLATFORM(UWP/APP MODEL)/STORE/BACKEND/FRAMEWORK"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppReadiness" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo AppReadiness starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppXSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo AppXSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\camsvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo camsvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CDPUserSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo CDPUserSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ClipSVC" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo ClipSVC starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NcbService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo NcbService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\OneSyncSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo OneSyncSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StateRepository" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo StateRepository starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TimeBrokerSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo TimeBrokerSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TokenBroker" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo TokenBroker starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrustedInstaller" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
:: echo "this is fine"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UdkUserSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo UdkUserSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UnistoreSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo UnistoreSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UserDataSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo UserDataSvc starttype= all >nul 2>&1

echo "VARIOUS DISABLED SERVICES"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BcastDVRUserService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo BcastDVRUserService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CaptureService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo CaptureService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\cbdhsvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo cbdhsvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DusmSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo DusmSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServer" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo FrameServer starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServerMonitor" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\jhi_service" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lmhosts" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MessagingService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo MessagingService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Ndu" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NPSMSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\P9RdrService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PhoneSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo PhoneSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PimIndexMaintenanceSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo PimIndexMaintenanceSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\QWAVE" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteAccess" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteRegistry" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RetailDemo" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StiSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UevAgentService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo UevAgentService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\whesvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wlidsvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WManSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblAuthManager" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo XblAuthManager starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblGameSave" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo XblGameSave starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxGipSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo XboxGipSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxNetApiSvc" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo XboxNetApiSvc starttype= all >nul 2>&1

echo "VARIOUS SERVICES SET CORRECTLY"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BITS" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ConsentUxUserSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo ConsentUxUserSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CryptSvc" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DispBrokerDesktopSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo DispBrokerDesktopSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DisplayEnhancementService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\EventLog" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FontCache" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Intel(R) TPM Provisioning Service" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\msiserver" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Netman" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\netprofm" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo netprofm starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PcaSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo PcaSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PlugPlay" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RmSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo RmSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StorSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo StorSvc starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TextInputManagementService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo TextInputManagementService starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrkWks" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
:: echo "this is fine"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\W32Time" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\W32Time\Parameters" /v Type /t REG_SZ /d NoSync /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Winmgmt" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WMIRegistrationService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnService" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnUserService" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc" /v "Start" /t REG_DWORD /d 2 /f >nul 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc (set REG_DWORD 2) (should be default)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\xboxgip" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1

echo "BITLOCKER OFF"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BDESVC" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo BDESVC starttype= all >nul 2>&1

echo "DISABLES OTHER WINDOWS AI STUFF"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsAADCloudSearchEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsCloudSearchEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsDeviceSearchHistoryEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsMSACloudSearchEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "SafeSearchMode" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Camera" /v "AllowCamera" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DisableTelemetryOptInSettingsUx" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "AllowRecallEnablement" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableAIDataAnalysis" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableCocreator" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableGenerativeFill" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableImageCreator" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetCopilotHardwareKey" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetDenyUriListForRecall" /t REG_SZ /d "*" /f >nul 2>&1

echo "DISABLE AUTOCORRECT/SPELLCHECK/SUGGESTIONS/PREDICTION/ECT"
reg add "HKCU\SOFTWARE\Microsoft\InputPersonalization\TrainedDataStore" /v "HarvestContacts" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableAutocorrection" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableSpellchecking" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Input\TIPC" /v "Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "DisableHwkbAutocorrection" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "DisableHwkbTextPrediction" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictCloudSuggestions" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictImplicitInkCollection" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictImplicitTextCollection" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v "NoLockScreen" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "AllowClipboardHistory" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "AllowCrossDeviceClipboard" /t REG_DWORD /d 0 /f >nul 2>&1

echo "CONTROL PANEL TWEAKS"
reg add "HKCU\Control Panel\Desktop" /v "AutoEndTasks" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "DragFullWindows" /t REG_SZ /d 1 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "DragHeight" /t REG_SZ /d 2 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "DragWidth" /t REG_SZ /d 2 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "MenuShowDelay" /t REG_DWORD /d 200 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "SmoothScroll" /t REG_SZ /d 1 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop" /v "WaitToKillAppTimeout" /t REG_SZ /d 3000 /f >nul 2>&1
reg add "HKCU\Control Panel\Desktop\WindowMetrics" /v "MinAnimate" /t REG_SZ /d 0 /f >nul 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseHoverTime" /t REG_DWORD /d 200 /f >nul 2>&1

echo "GAME TWEAKS"
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AllowAutoGameMode" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AutoGameModeEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "GamePanelStartupTipIndex" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "ShowStartupPanel" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DXGIHonorFSEWindowsCompatible" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_HonorUserFSEBehaviorMode" /t REG_DWORD /d 1 /f >nul 2>&1

echo "CLASSIC RIGHT CLICK"
reg add "HKCU\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /ve /t REG_SZ /d "" /f >nul 2>&1

echo "EXPLORER TWEAKS"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "AutoCheckSelect" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DisableThumbnailCache" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DontPrettyPath" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "EnableBalloonTips" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "Hidden" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "HideFileExt" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "IconsOnly" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "LaunchTo" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ListviewAlphaSelect" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowInfoTip" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowSuperHidden" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowTaskViewButton" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarGlomLevel" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\OperationStatusManager" /v "EnthusiastMode" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Serialize" /v "StartupDelayInMSec" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects" /v "VisualFXSetting" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "SettingsPageVisibility" /t REG_SZ /d "hide:home" /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FlyoutMenuSettings" /v "ShowHibernateOption" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoAutorun" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoDriveTypeAutoRun" /t REG_DWORD /d 255 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableSearchBoxSuggestions" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "HidePeopleBar" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "HideSCAMeetNow" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >nul 2>&1

echo "HKCU SIUF"
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "NumberOfSIUFInPeriod" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "PeriodInNanoSeconds" /t REG_DWORD /d 0 /f >nul 2>&1

echo "HKCU CURRENTVERSION"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Accessibility" /v "Animation" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\ConsentStore\location" /v "Value" /t REG_SZ /d Deny /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "ContentDeliveryAllowed" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "FeatureManagementEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "OEMPreInstalledAppsEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEverEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SilentInstalledAppsEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SoftLandingEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-310093Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-314559Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-314563Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338387Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338388Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338389Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338393Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353694Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353696Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353698Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-88000326Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContentEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SystemPaneSuggestionsEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsOneDriveResumeAllowed" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsResumeAllowed" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AppCaptureEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "BingSearchEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CanCortanaBeEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "DeviceHistoryEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "HistoryViewEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v "EnableTransparency" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\UserProfileEngagement" /v "ScoobeSystemSettingEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Windows Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f >nul 2>&1

echo "HKLM MICROSOFT"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Browser\AllowSmartScreen" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowCortana" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableAppInstallControl" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableSmartScreenInShell" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\PreventOverrideForFilesInShell" /v "Value" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "NetworkThrottlingIndex" /t REG_DWORD /d 4294967295 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "SystemResponsiveness" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AppHost" /v "EnableWebContentEvaluation" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "DODownloadMode" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "SystemSettingsDownloadMode" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Device Metadata" /v "PreventDeviceMetadataFromNetwork" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableFirstLogonAnimation" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableLUA" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\ScheduledDiagnostics" /v "EnabledExecution" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Reporting" /v "Disabled" /t REG_DWORD /d 1 /f >nul 2>&1

echo "HKLM POLICIES"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoActiveHelp" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoExplicitFeedback" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Biometrics" /v "Enabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\PushToInstall" /v "DisablePushToInstall" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CEIPEnable" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CorporateSQMURL" /t REG_SZ /d 0.0.0.0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient\Windows" /v "CEIPEnable" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\CurrentVersion\SOFTWARE Protection Platform" /v "NoGenTicket" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v "EnableMulticast" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AdvertisingInfo" /v "DisabledByGroupPolicy" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "AITEnable" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisableInventory" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisablePCA" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "ConfigureWindowsSpotlight" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableCloudOptimizedContent" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableConsumerAccountStateContent" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableSoftLanding" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableTailoredExperiencesWithDiagnosticData" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableThirdPartySuggestions" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsConsumerFeatures" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightFeatures" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnActionCenter" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnSettings" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightWindowsWelcomeExperience" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "IncludeEnterpriseSpotlight" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CredUI" /v "DisablePasswordReveal" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\PushNotifications" /v "NoTileApplicationNotification" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\FileHistory" /v "Disabled" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\HandwritinReports" /v "PreventHandwritinReports" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocation" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocationScripting" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableSensors" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableWindowsLocationProvider" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "AllowBuildPreview" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "EnableConfigFlighting" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSync" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSyncUserOverride" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "PreventHandwritingDataSharing" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Chat" /v "ChatIcon" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Reporting" /v "Disabled" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowCortana" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowSearchToUseLocation" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchPrivacy" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWeb" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWebOverMeteredConnections" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableSearch" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableWebSearch" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ManagePreviewBuilds" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AUOptions" /t REG_DWORD /d 2 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AutoInstallMinorUpdates" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "DetectionFrequencyEnabled" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoRebootWithLoggedOnUsers" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallDay" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallTime" /t REG_DWORD /d 20 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "UseWUServer" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\WMDRM" /v "DisableOnline" /t REG_DWORD /d 1 /f >nul 2>&1

echo "HKLM CONTROL"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\CI\Policy" /v "VerifiedAndReputablePolicyState" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\FileSystem" /v "LongPathsEnabled" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerThrottling" /v "PowerThrottlingOff" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "IRQ8Priority" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowFullControl" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowToGetHelp" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v "ClearPageFileAtShutdown" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Power" /v "HibernateEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\AutoLogger-Diagtrack-Listener" /v "Start" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\SQMLogger" /v "Start" /t REG_DWORD /d 0 /f >nul 2>&1

echo "MAPS"
reg add "HKLM\SYSTEM\Maps" /v "AutoUpdateEnabled" /t REG_DWORD /d 0 /f >nul 2>&1

echo "AllowUpgradesWithUnsupportedTPMOrCPU"
reg add "HKLM\SYSTEM\Setup\MoSetup" /v "AllowUpgradesWithUnsupportedTPMOrCPU" /t REG_DWORD /d 1 /f >nul 2>&1

echo "MICROSOFT CLOUD IDENTITY SERVICE"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\cloudidsvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo cloudidsvc starttype= all >nul 2>&1

echo "CLOUD RESET"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CloudBackupRestoreSvc" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1

echo "OEM UTILITIES,LMS,AMT,ME Firmware,MEIx64"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MEIx64" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WMIRegistrationService" /v "Start" /t REG_DWORD /d 3 /f >nul 2>&1
sc triggerinfo WMIRegistrationService starttype= all >nul 2>&1

echo "DISABLE APPLE MOBILE DEVICE SERVICE STUFF"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Apple Mobile Device Service" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppleKmdfFilter" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppleLowerFilter" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1

echo "EDGE STUFF"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Ext\CLSID" /v "{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AlternatPagesEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AutofillAddressEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AutofillCreditCardEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "BackgroundModeEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "BlockThirdPartyCookies" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "ConfigureDoNotTrack" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "DiagnosticData" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "EnhanceSecurityMode" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "HideFirstRunExperience" /t REG_DWORD /d 1 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "NewTabPagePrerenderEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "PasswordManagerEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "PrelaunchEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SearchSuggestEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "CreateDesktopShortcutDefault" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "UpdateDefault" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\Main" /v "AllowPrelaunch" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\PhishingFilter" /v "EnabledV9" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\TabPreloader" /v "AllowTabPreloading" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Policies\Ext\CLSID" /v "{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}" /t REG_DWORD /d 0 /f >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\edgeupdate" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo edgeupdate starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\edgeupdatem" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo edgeupdatem starttype= all >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MicrosoftEdgeElevationService" /v "Start" /t REG_DWORD /d 4 /f >nul 2>&1
:: sc triggerinfo MicrosoftEdgeElevationService starttype= all >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\EdgeUpdate" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects" /f >nul 2>&1

echo "EDGE STUFF 2"
for /f "tokens=2 delims=:" %%T in ('schtasks /query /fo LIST /v ^| findstr /i "TaskName:" ^| findstr /i "MicrosoftEdgeUpdateTaskMachine"') do (
    set "raw=%%T"
    set "task=!raw:~1!"
    set "task=!task: =!"
    schtasks /change /tn "!task!" /disable >nul 2>&1
)
for /f "tokens=*" %%a in ('reg query "HKLM\SOFTWARE\Microsoft\Active Setup\Installed Components"') do (
    for /f "tokens=2,*" %%b in ('reg query "%%a" /v StubPath 2^>nul ^| find /i "StubPath" ^| find /i "edge"') do (
        reg delete "%%a" /v "StubPath" /f >nul 2>&1
    )
)

:: echo "SCHTASKS CANNOT DISABLE"
:: schtasks /change /tn "\Microsoft\Windows\Shell\UpdateUserPictureTask" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Report policies" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Schedule Scan" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Start Oobe Expedite Work" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\StartOobeAppsScan_LicenseAccepted" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\StartOobeAppsScanAfterUpdate" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\UIEOrchestrator" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\USO_UxBroker" /disable >nul 2>&1
:: schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\UUS Failover Task" /disable >nul 2>&1

echo "SCHTASKS"
schtasks /change /tn "\Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Application Experience\PcaPatchDbTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Application Experience\StartupAppTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\ApplicationData\DsSvcCleanup" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Autochk\Proxy" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Clip\License Validation" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\CloudExperienceHost\CreateObjectTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\Consolidator" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UnifiedConsentSyncTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataFlushing" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataReceiver" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataReporting" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsbCeip" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Diagnosis\RecommendedTroubleshootingScanner" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Diagnosis\Scheduled" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\DiskFootprint\Diagnostics" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Feedback\Siuf\DmClientOnScenarioDownload" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\FileHistory\File History (maintenance mode)" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\License Manager\TempSignedLicenseExchange" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Maintenance\WinSAT" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Maps\MapsToastTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\NetworkDiagnostics\BfeOnServiceStartTypeChange" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\NetworkDiagnostics\ResolutionHost" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\PI\Sqm-Tasks" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\PowerGridForecastTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\SustainabilityTelemetry" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Printing\PrinterCleanupTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Printing\PrintJobCleanupTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\PushToInstall\LoginCheck" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\PushToInstall\Registration" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\RemoteAssistance\RemoteAssistanceTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Servicing\StartComponentCleanup" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyMonitor" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyRefreshTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\IndexerAutomaticMaintenance" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\NotificationTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\ThemesSyncedImageDownload" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\XblGameSaveTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Speech\SpeechModelDownloadTask" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Subscription\EnableLicenseAcquisition" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Subscription\LicenseAcquisition" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Time Synchronization\ForceSynchronizeTime" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\TPM\Tpm-Maintenance" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\UPnP\UPnPHostConfig" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\USB\Usb-Notifications" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\WaaSMedic\MaintenanceWork" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cache Maintenance" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cleanup" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Scheduled Scan" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Verification" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Reporting\QueueReporting" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\WlanSvc\CDSSync" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\Windows\WwanSvc\OobeDiscovery" /disable >nul 2>&1
schtasks /change /tn "\Microsoft\XblGameSave\XblGameSaveTask" /disable >nul 2>&1

echo "UPDATE DELETES"
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\ClipchampUpdate" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\DevHomeUpdate" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OfficeHubUpdate" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OneDriveSetup" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OutlookUpdate" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\TeamsMachineInstaller" /f >nul 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\WebView2RuntimeUpdate" /f >nul 2>&1

:: cls
echo "Run after updates/repairs" >> "%log%"
echo "Run after updates/repairs"
echo "check log on desktop and CHANGE VALUES* in RegCool.exe(or other) manually" >> "%log%"
echo "check log on desktop and CHANGE VALUES* in RegCool.exe(or other) manually"
echo "Turn off everything in windows virus & threat protection or revert MSEC sections if you want that stuff" >> "%log%"
echo "Turn off everything in windows virus & threat protection or revert MSEC sections if you want that stuff"
echo "REBOOT AFTER" >> "%log%"
echo "REBOOT AFTER"

powershell -c "(New-Object Media.SoundPlayer 'C:\Windows\Media\tada.wav').PlaySync()"
:: taskkill /f /im explorer.exe && start explorer.exe

endlocal
pause >nul
```
