# Registry-Tweaks-Refresh.bat v1.0.4
Windows 11 Registry Tweaks
#### this is what i use, make the bat file and run it in safe mode* and force the CHANGE* regs in log and skim through for info
#### use Autoruns64.exe to find out more about your PC's autoruns

```python
@echo off

:: echo "CHECK ADMIN RIGHTS USING FLTMC (WORKS WITHOUT SERVER SERVICE)"
fltmc >nul 2>&1 || (
    powershell -Command "Start-Process '%~f0' -Verb RunAs"
    exit /b
)

:: echo "COSMETIC"
color 0A
:: mode con: cols=80 lines=20

:: echo "ENVIRONMENT"
setlocal EnableDelayedExpansion
set "log=%userprofile%\desktop\registry-tweaks-refresh.txt"

:: echo "REG_DWORD VALUES 0=BOOT 1=SYSTEM 2=AUTOMATIC 3=MANUAL 4=DISABLED"

:restorechoice
echo "Y OPENS SYSTEM PROPERTIES SO YOU CAN CREATE A RESTORE POINT, N SKIPS"
set /P restorechoice=[Y/N]:
if /I "%restorechoice%"=="Y" goto yesrestore
if /I "%restorechoice%"=="N" goto norestore 
goto invalidchoice

:yesrestore
echo "CLICK ON PROTECTED DRIVE AND CREATE" >> "%log%"
echo "CLICK ON PROTECTED DRIVE AND CREATE"
"%windir%\System32\SystemPropertiesProtection.exe"
goto updatechoice

:norestore
echo "SKIPPING RESTORE POINT" >> "%log%"
echo "SKIPPING RESTORE POINT"
goto updatechoice

:updatechoice
echo "Y DISABLES WINDOWS UPDATES, N ENABLES/DEFAULTS"
set /P updatechoice=[Y/N]:
if /I "%updatechoice%"=="Y" goto disableupdates
if /I "%updatechoice%"=="N" goto enableupdates
goto invalidchoice

:disableupdates
echo "DISABLING WINDOWS UPDATES" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\hpatchmon" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Hotpatch\Monitoring" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuqisvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto AUTODRIVchoice

:enableupdates
echo "ENABLING WINDOWS UPDATES" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\hpatchmon" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo hpatchmon starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Hotpatch\Monitoring" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo UsoSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuqisvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo wuauserv starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
goto AUTODRIVchoice

:AUTODRIVchoice
echo "Y DISABLES AUTOMATICALLY SEARCH WINDOWS UPDATE FOR DRIVERS WHEN NEW HARDWARE IS CONNECTED, N ENABLES/DEFAULTS"
set /P AUTODRIVchoice=[Y/N]:
if /I "%AUTODRIVchoice%"=="Y" goto disableAUTODRIVchoice
if /I "%AUTODRIVchoice%"=="N" goto enableAUTODRIVchoice
goto invalidchoice

:disableAUTODRIVchoice
echo "DISABLING AUTOMATICALLY SEARCH WINDOWS UPDATE FOR DRIVERS WHEN NEW HARDWARE IS CONNECTED" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
goto DRIVERchoice

:enableAUTODRIVchoice
echo "ENABLING AUTOMATICALLY SEARCH WINDOWS UPDATE FOR DRIVERS WHEN NEW HARDWARE IS CONNECTED" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
goto DRIVERchoice

:DRIVERchoice
echo "Y DISABLES WINDOWS UPDATE TO DELIVER DRIVER UPDATES ALONG WITH NORMAL UPDATES, N ENABLES/DEFAULTS"
set /P DRIVERchoice=[Y/N]:
if /I "%DRIVERchoice%"=="Y" goto disableDRIVERchoice
if /I "%DRIVERchoice%"=="N" goto enableDRIVERchoice
goto invalidchoice

:disableDRIVERchoice
echo "DISABLING WINDOWS UPDATE TO DELIVER DRIVER UPDATES ALONG WITH NORMAL UPDATES" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
goto printerchoice

:enableDRIVERchoice
echo "ENABLING WINDOWS UPDATE TO DELIVER DRIVER UPDATES ALONG WITH NORMAL UPDATES" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
goto printerchoice

:printerchoice
echo "Y DISABLES PRINTER, N ENABLES/DEFAULTS"
set /P printerchoice=[Y/N]:
if /I "%printerchoice%"=="Y" goto disableprinter
if /I "%printerchoice%"=="N" goto enableprinter
goto invalidchoice

:disableprinter
echo "DISABLING PRINTER" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto copilotchoice

:enableprinter
echo "ENABLING PRINTER" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo PrintWorkflowUserSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
goto copilotchoice

:copilotchoice
echo "Y DISABLES WINDOWS COPILOT, N ENABLES/DEFAULTS"
set /P copilotchoice=[Y/N]:
if /I "%copilotchoice%"=="Y" goto disablecopilot
if /I "%copilotchoice%"=="N" goto enablecopilot
goto invalidchoice

:disablecopilot
echo "DISABLING WINDOWS COPILOT" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto interestschoice

:enablecopilot
echo "ENABLING WINDOWS COPILOT" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
goto interestschoice

:interestschoice
echo "Y DISABLES NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT, N ENABLES/DEFAULTS"
set /P interestschoice=[Y/N]:
if /I "%interestschoice%"=="Y" goto disableinterests
if /I "%interestschoice%"=="N" goto enableinterests
goto invalidchoice

:disableinterests
echo "DISABLING NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT ON TASKBAR" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "CHANGE* HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced (set TaskbarDa REG_DWORD to 0)" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "CHANGE* HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds (set ShellFeedsTaskbarViewMode REG_DWORD to 2)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsRunInBackground" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\PushNotifications" /v "NoTileApplicationNotification" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
goto touchchoice

:enableinterests
echo "ENABLING NOTIFICATIONS/WIDGETS/BACKGROUND APPS/ECT ON TASKBAR" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CHANGE* HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced (set TaskbarDa REG_DWORD to 1)" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CHANGE* HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds (set ShellFeedsTaskbarViewMode REG_DWORD to 1)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsRunInBackground" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\PushNotifications" /v "NoTileApplicationNotification" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
goto touchchoice

:touchchoice
echo "Y DISABLES TOUCH SCREEN STUFF, N ENABLES/DEFAULTS"
set /P touchchoice=[Y/N]:
if /I "%touchchoice%"=="Y" goto disabletouch
if /I "%touchchoice%"=="N" goto enabletouch
goto invalidchoice

:disabletouch
echo "DISABLE TOUCH SCREEN STUFF" >> "%log%"
reg add "HKCU\Control Panel\Accessibility" /v "TouchFeedback" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "TouchGestureSetting" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "DisableNewKeyboardExperience" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableCompatibilityKeyboard" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableDesktopModeAutoInvoke" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "KeyboardLayoutPreference" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "UserKeyboardScalingFactor" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\TouchMode" /v "ShowTouchModeButton" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\PenWorkspace" /v "PenWorkspaceButtonDesiredVisibility" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\Flicks" /v "EnableFlicks" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\PenFeedback" /v "TurnOnPressAndHold" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Software\Microsoft\Wisp\Touch" /v "TouchGate" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\TabletTip\1.7" /v "TipbandDesiredVisibility" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ImmersiveShell" /v "TabletMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\PrecisionTouchPad" /v "EnableVirtualTouchpad" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "TurnOffTouchInput" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "3FingerTapAction" /t REG_DWORD /d 0x0000000 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "4FingerTapAction" /t REG_DWORD /d 0x0000000 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "ConvertibleSlateMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\TabletPC" /v "EnableTouch" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto bluechoice

:enabletouch
echo "ENABLE TOUCH SCREEN STUFF" >> "%log%"
reg add "HKCU\Control Panel\Accessibility" /v "TouchFeedback" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "TouchGestureSetting" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "DisableNewKeyboardExperience" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableCompatibilityKeyboard" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableDesktopModeAutoInvoke" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "KeyboardLayoutPreference" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "UserKeyboardScalingFactor" /t REG_DWORD /d 100 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\TouchMode" /v "ShowTouchModeButton" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\PenWorkspace" /v "PenWorkspaceButtonDesiredVisibility" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\Flicks" /v "EnableFlicks" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Wisp\Pen\PenFeedback" /v "TurnOnPressAndHold" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\Software\Microsoft\Wisp\Touch" /v "TouchGate" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\TabletTip\1.7" /v "TipbandDesiredVisibility" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ImmersiveShell" /v "TabletMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\PrecisionTouchPad" /v "EnableVirtualTouchpad" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "TurnOffTouchInput" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "3FingerTapAction" /t REG_DWORD /d 0x00000a6 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Synaptics\SynTPPlugins\SynTP" /v "4FingerTapAction" /t REG_DWORD /d 0x30115b27 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "ConvertibleSlateMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\TabletPC" /v "EnableTouch" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo PenService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
goto bluechoice

:bluechoice
echo "Y DISABLES BLUETOOTH, N ENABLES/DEFAULTS"
set /P bluechoice=[Y/N]:
if /I "%bluechoice%"=="Y" goto noblue
if /I "%bluechoice%"=="N" goto yesblue
goto invalidchoice

:noblue
echo "DISABLING BLUETOOTH" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTAGService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthav" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHPORT" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHUSB" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DeviceAssociationService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicePickerUserSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicesFlowUserSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto PowerThrottlingchoice

:yesblue
echo "ENABLING BLUETOOTH" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTAGService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo BTAGService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthav" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo bthav starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHPORT" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo BTHPORT starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHUSB" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo BTHUSB starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DeviceAssociationService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo DeviceAssociationService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicePickerUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo DevicePickerUserSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicesFlowUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo DevicesFlowUserSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
goto PowerThrottlingchoice

:PowerThrottlingchoice
echo "Y DISABLES POWER THROTTLING, N ENABLES/DEFAULTS (for laptops ect.)"
set /P PowerThrottlingchoice=[Y/N]:
if /I "%PowerThrottlingchoice%"=="Y" goto noPowerThrottling
if /I "%PowerThrottlingchoice%"=="N" goto yesPowerThrottling
goto invalidchoice

:noPowerThrottling
echo "DISABLING POWER THROTTLING" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerThrottling" /v "PowerThrottlingOff" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
goto RTR

:yesPowerThrottling
echo "ENABLING POWER THROTTLING" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerThrottling" /v "PowerThrottlingOff" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
goto RTR

:invalidchoice
echo "INVALID COMMAND RUN BAT AGAIN"
powershell -c "(New-Object Media.SoundPlayer 'C:\Windows\Media\Windows User Account Control.wav').PlaySync()"
exit /b

goto RTR
:RTR

echo "STANDBY" >> "%log%"
for /f %%A in ('"prompt $H & for %%B in (1) do rem"') do set "BS=%%A"
<nul set /p "=STANDBY %username%"
for %%A in (. . .) do (
    <nul set /p "=%%A"
    timeout /nobreak /t 1 >nul
    for /l %%i in (1,1,0) do <nul set /p "=%BS%"
)
echo.

echo "CUSTOM WINDOWS UPDATES SETTINGS" >> "%log%"
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "DisableOSUpgrade" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "DODownloadMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "SystemSettingsDownloadMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Device Metadata" /v "PreventDeviceMetadataFromNetwork" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "AllowBuildPreview" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "EnableConfigFlighting" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ManagePreviewBuilds" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AUOptions" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AutoInstallMinorUpdates" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "DetectionFrequency" /t REG_DWORD /d 22 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "DetectionFrequencyEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoRebootWithLoggedOnUsers" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallDay" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallTime" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "UseWUServer" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "BACKGROUND INTELLIGENT TRANSFER SERVICE (WINDOWS UPDATE/STORE/DEFENDER/TELEMETRY AND DIAG/3RD PARTY) DEFAULT 2" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BITS" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo "SETTINGS > PRIVACY & SECURITY > APP PERMISSIONS (0=TOGGLE 1=FORCE ALLOW 2=FORCE DENY FOR LETAPPSACCESS)" >> "%log%"
echo "LOCATION" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocation" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocationScripting" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableSensors" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableWindowsLocationProvider" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CAMERA" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Camera" /v "AllowCamera" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "MICROPHONE" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessMicrophone" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "VOICE ACTIVATION" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsActivateWithVoice" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "NOTIFICATIONS" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PushToInstall" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo PushToInstall starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\PushToInstall" /v "DisablePushToInstall" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\PushToInstall\LoginCheck" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\PushToInstall\Registration" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: echo "ACCOUNT INFO" >> "%log%"
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessAccountInfo" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "CONTACTS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessContacts" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "CALENDAR" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessCalendar" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "PHONE CALLS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessPhoneCall" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "CALL HISTORY" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessCallHistory" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "EMAIL" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessEmail" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "TASKS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessTasks" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "MESSAGING" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessMessaging" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "RADIOS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsControlRadios" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "OTHER DEVICES" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsSyncWithDevices" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "APP DIAGNOSTICS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessDiagnosticsInfo" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "AUTOMATIC FILE DOWNLOADS (BLOCKED VIA POLICY/BLOCK SPECIFIC APPS)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\CloudFiles" /v "AutomaticDownloads" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\CloudFiles" /v "BlockedApps" /t REG_MULTI_SZ /d "app1.exe\app2.exe" /f >> "%log%" 2>&1
echo "DOCUMENTS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessDocumentsLibrary" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "DOWNLOADS FOLDER" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessDownloadsFolder" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "MUSIC LIBRARY" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessMusicLibrary" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "PICTURES" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessPicturesLibrary" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "VIDEOS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessVideosLibrary" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "FILE SYSTEM" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessFileSystem" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "SCREENSHOT BORDERS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessScreenshotBorder" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "SCREENSHOTS AND SCREEN RECORDING" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessScreenCapture" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "TEXT AND IMAGE GENERATION" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsUseTextAndImageGeneration" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "PASSKEYS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsAccessPasskeyAutofill" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppPrivacy" /v "LetAppsControlPasskeys" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

:: echo "VPN/IPSEC/IPV6 INFRASTRUCTURE STUFF" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\IKEEXT" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo IKEEXT starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\iphlpsvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo iphlpsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\NcaSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo NcaSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\PolicyAgent" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo PolicyAgent starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\RasMan\Parameters" /v "AllowVpnOverMeteredNetworks" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\RasMan\Parameters" /v "AllowVpnWhileRoaming" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\SstpSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" /v "DisabledComponents" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "MOUSE STUFF POINTER SPEED DEFAULT (10 IN REGISTRY/MS-SETTINGS:MOUSETOUCHPAD) (6/11 IN SLIDER/MAIN.CPL)" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "DragFullWindows" /t REG_SZ /d 1 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "DragHeight" /t REG_SZ /d 4 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "DragWidth" /t REG_SZ /d 4 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "MenuShowDelay" /t REG_SZ /d 400 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "SmoothScroll" /t REG_SZ /d 1 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "DoubleClickSpeed" /t REG_SZ /d 500 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "ListViewHoverTime" /t REG_SZ /d 400 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseHoverTime" /t REG_SZ /d 400 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseSensitivity" /t REG_SZ /d 10 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseSpeed" /t REG_SZ /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseThreshold1" /t REG_SZ /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseThreshold2" /t REG_SZ /d 0 /f >> "%log%" 2>&1

:: echo "HIDE SETTINGS PAGES DELETE RESTORES DEFAULT" >> "%log%"
:: reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "SettingsPageVisibility" /t REG_SZ /d "hide:home" /f >> "%log%" 2>&1
:: reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "SettingsPageVisibility" /f >> "%log%" 2>&1

echo "CHECK YOUR CURRENT TIMER RESOLUTION (Get-Process | Where-Object { $_.Name -eq "System" } | Measure-Command { })" >> "%log%"
echo "TIMER RESOLUTION (10000×100ns = 1,000,000ns = 1 millisecond)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Clock Rate" /t REG_DWORD /d 10000 /f >> "%log%" 2>&1
:: echo "TIMER RESOLUTION (5000×100ns = 500,000ns = 0.5 milliseconds)" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Clock Rate" /t REG_DWORD /d 5000 /f >> "%log%" 2>&1
:: echo "TIMER RESOLUTION DEFAULT NOT IN REGISTRY" >> "%log%"
:: reg delete "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Clock Rate" /f >> "%log%" 2>&1

echo "MULTIMEDIA CLASS SCHEDULER SERVICE DEFAULTS" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Affinity" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Background Only" /t REG_SZ /d "False" /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "GPU Priority" /t REG_DWORD /d 8 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Priority" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Scheduling Category" /t REG_SZ /d "Medium" /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "SFIO Priority" /t REG_SZ /d "Normal" /f >> "%log%" 2>&1

echo "BOOSTS/QUANTUM DEFAULT IS BALANCE/SAFER=26 (2:1/SHORT) EVEN/RESPONSIVE/SMOOTH=2 (1:1/SHORT) GAME ONLY/LEAST BACKGROUND=38 (3:1/SHORT)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "Win32PrioritySeparation" /t REG_DWORD /d 26 /f >> "%log%" 2>&1

echo "BORDERLESS FULLSCREEN: ALWAYS COMPOSITED BY DWM, SIMPLEST AND MOST STABLE BUT LESS EFFICIENT THAN FSE/EFSE(FO)" >> "%log%"
echo "FULLSCREEN EXCLUSIVE (FSE): BYPASSES DWM FOR LOWEST LATENCY/BEST PERFORMANCE, BUT ALT TABBING IS SLOW/FLICKERY, FORCE IT BY CHECKING DISABLE FULLSCREEN OPTIMIZATIONS IN .EXE/DSEBEHAVIOR=2/OR JUST SETTING GAME TO FULLSCREEN IF SUPPORTED" >> "%log%"
echo "FULLSCREEN OPTIMIZATIONS (FO/EFSE): USES FLIP MODEL PRESENTATION THROUGH DWM FOR NEAR FSE PERFORMANCE WITH INSTANT ALT TAB AND WORKING OVERLAYS/NOTIFICATIONS" >> "%log%"
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DSEBehavior" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DXGIHonorFSEWindowsCompatible" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehaviorMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_HonorUserFSEBehaviorMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: echo "FSE/EFSE(FO) ECT DEFAULTS" >> "%log%"
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DSEBehavior" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DXGIHonorFSEWindowsCompatible" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehaviorMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_HonorUserFSEBehaviorMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "GAMEDVR OFF KEYS" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AppCaptureEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "BROADCAST DVR (GAMEDVR/XBOX RECORDING)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BcastDVRUserService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo BcastDVRUserService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "GAME MODE LEAVE ON UNLESS YOU THINK ITS BREAKING SOMETHING" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AllowAutoGameMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AutoGameModeEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "GAMEBAR" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "GameBarEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "GamePanelStartupTipIndex" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "ShowStartupPanel" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "UseNexusForGameBarEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "SYSTEM MEDIA CONTROLS (NOW PLAYING SESSION MANAGER) DEFAULT IS 3" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NPSMSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo "SMS ROUTER SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SmsRouter" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "THIS PREVENTS WINDOWS FROM AUTO ASSIGNING MUSIC/PICTURES/VIDEOS TEMPLATES BASED ON CONTENT AND UNDO REG DELETE" >> "%log%"
reg add "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\Bags\AllFolders\Shell" /v "FolderType" /t REG_SZ /d NotSpecified /f >> "%log%" 2>&1
:: reg delete "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\Bags\AllFolders\Shell" /v "FolderType" /f >> "%log%" 2>&1

echo "WINHTTP WEB PROXY AUTO DISCOVERY SERVICE (WPAD) PROTOCOL CHECK WITH (NETSH WINHTTP SHOW PROXY) (DISABLEWPAD DEFAULT DELETE)" >> "%log%"
:: reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings" /v "AutoDetect" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v "DisableWpad" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v "DisableWpad" /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WinHttpAutoProxySvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

:: echo "REDUCES ATTACK SURFACE (TAKE OWNERSHIP CONTEXT MENU YOU MIGHT HAVE)" >> "%log%"
:: reg delete "HKCR\*\shell\TakeOwnership" /f >> "%log%" 2>&1
:: reg delete "HKCR\Directory\shell\TakeOwnership" /f >> "%log%" 2>&1

:: echo "WIFI STUFF" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowAutoConnectToWiFiSenseHotspots" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowWiFiHotSpotReporting" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\WcmSvc\wifinetworkmanager\config" /v "AutoConnectAllowedOEM" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: schtasks /change /tn "\Microsoft\Windows\WiFi\WiFiTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: schtasks /change /tn "\Microsoft\Windows\WlanSvc\CDSSync" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "REMOTE DCOM ACTIVATION IS DISABLED (NO CROSS MACHINE COM CALLS)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Ole" /v "EnableDCOM" /t REG_SZ /d "N" /f >> "%log%" 2>&1

echo "ROUTES DEVICE MANAGEMENT PUSH MESSAGES (MDM/INTUNE)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\dmwappushservice" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo dmwappushservice starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "DISABLED CONNECTED USER EXPERIENCES AND TELEMETRY SERVICE DEFAULT 2" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DiagTrack" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "GEOLOCATION SERVICE, IT MANAGES LOCATION DATA FOR YOUR DEVICE DEFAULT 3" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lfsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo lfsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CRASH REPORTING BACKBONE OF WINDOWS DISABLED DEFAULT 3" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WerSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo WerSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "ENABLING LOCAL TROUBLESHOOTING/DIAGNOSTICS/HEALTH MONITORING (AUTOMATED TROUBLESHOOTING/ERROR REPORTS)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DPS" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\DPS default 2" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\WdiServiceHost default 3" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost default 3" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\whesvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo whesvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

:: echo "WHEA LOGGER" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Control\WHEA\Logger" /v "DisableLogging" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg delete "HKLM\SYSTEM\CurrentControlSet\Control\WHEA\Logger" /v "DisableLogging" /f >> "%log%" 2>&1

echo "ADVERTISING/CLOUD SUGGESTIONS" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AppHost" /v "EnableWebContentEvaluation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AdvertisingInfo" /v "DisabledByGroupPolicy" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "PCHEALTHCHECK STUFF" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PCHC" /v "PreviousUninstall" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PCHealthCheck" /v "installed" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "SENSORS STUFF" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{09485F5A-759E-4A45-B622-5C7F2FCE985E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C5C4656-0E77-4F44-BF4C-8C0C8A76E8A7}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C7C1A12-3B7D-4B4B-9F4E-B1A1B6E0E1D8}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{76B5CE0A-17DD-414D-93A1-E127F40BDF6E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{B5C5BB9C-DAF8-4661-BB0F-5A4E3D8C6F58}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{BFA794E4-F964-4FDB-90F6-51056BFE4B44}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{C04D7B5C-9C2F-4C32-9C47-5C6E2F0E6D98}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensorDataService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo SensorDataService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensorService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo SensorService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensrSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo SensrSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "WHO YOU ARE, WHAT YOU CAN RUN (APPIDSVC) DISABLE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\AppID\VerifiedPublisherCertStoreCheck" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: echo "WHO YOU ARE, WHAT YOU CAN RUN (APPIDSVC) ENABLE" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo AppIDSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: schtasks /change /tn "\Microsoft\Windows\AppID\VerifiedPublisherCertStoreCheck" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

:: echo "UNELEVATE USERACCOUNTCONTROLSETTINGS" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "ConsentPromptBehaviorAdmin" /t REG_DWORD /d 5 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableLUA" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "PromptOnSecureDesktop" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: schtasks /change /tn "CreateExplorerShellUnelevatedTask" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "SMB STUFF" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v "AutoShareWks" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "DISABLING FUNCTION DISCOVERY FRAMEWORK (APPLE/SMART TV/ETC)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\fdPHost" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FDResPub" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo FDResPub starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SSDPSRV" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "WIRELESS RADIO SIGNALS TOGGLE FOR WIFI/BLUETOOTH/CELLULAR/AIRPLANE MODE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RmSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo RmSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "PREFETCH\SUPERFETCH\SYSMAIN DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SysMain" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "REMOTE STUFF DISABLED" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Remote Assistance" /v "fAllowToGetHelp" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fAllowToGetHelp" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fDisableAudioCapture" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fDisableCcm" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fDisableCdm" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fDisableClip" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fDisableCpm" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v "fDisableLPT" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowFullControl" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowToGetHelp" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v "fDenyTSConnections" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd" /v "fDisableClip" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd" /v "StartupPrograms" /t REG_SZ /d "" /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteAccess" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteRegistry" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\RemoteAssistance\RemoteAssistanceTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "OPTIMIZE DRIVES" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\defragsvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Defrag\ScheduledDefrag" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Storage Tiers Management\Storage Tiers Optimization" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "LIMITED USER ACCOUNT FILE VIRTUALIZATION DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\luafv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo luafv starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "UNIVERSAL WINDOWS PLATFORM(UWP/APP MODEL)/STORE/BACKEND/FRAMEWORK" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppReadiness" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo AppReadiness starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppXSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo AppXSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\camsvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo camsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CDPUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo CDPUserSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ClipSVC" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo ClipSVC starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
echo "[SC] OpenService FAILED 5 (starttype Access is denied)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\InstallService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo InstallService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NcbService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo NcbService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\OneSyncSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo OneSyncSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StateRepository" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo StateRepository starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TimeBrokerSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo TimeBrokerSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
echo "[SC] OpenService FAILED 5 (starttype Access is denied)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TokenBroker" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo TokenBroker starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrustedInstaller" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\TrustedInstaller default 3" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UdkUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo UdkUserSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UnistoreSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo UnistoreSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UserDataSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo UserDataSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnService" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnUserService" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Clip\License Validation" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\IndexerAutomaticMaintenance" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\ThemesSyncedImageDownload" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Speech\SpeechModelDownloadTask" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Subscription\LicenseAcquisition" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "MSEC STUFF DISABLED" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Browser\AllowSmartScreen" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableAppInstallControl" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableSmartScreenInShell" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\PreventOverrideForFilesInShell" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\RemovalTools\MpGears" /v "HeartbeatTrackingIndex" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontOfferThroughWUAU" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontReportInfectionInformation" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "AllowFastServiceStartup" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableSpecialRunningModes" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "PUAProtection" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "ServiceKeepAlive" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\MpEngine" /v "MpEnablePus" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableBehaviorMonitoring" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableOnAccessProtection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableRealtimeMonitoring" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableScanOnRealtimeEnable" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\SmartScreen" /v "ConfigureAppInstallControl" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\SmartScreen" /v "ConfigureAppInstallControlEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "DisableBlockAtFirstSeen" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SpyNetReporting" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SubmitSamplesConsent" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableActivityFeed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreen" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreenForStoreApps" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "PublishUserActivities" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "UploadUserActivities" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cache Maintenance" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cleanup" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Scheduled Scan" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Verification" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "MSEC SERVICES 1 DISABLED" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender (set REG_DWORD DisableAntiSpyware 1)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender (set REG_DWORD DisableAntiVirus 1)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Features" /v "TamperProtection" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender\Features (set REG_DWORD TamperProtection 0)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection" /v "DpaDisabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection (set DpaDisabled REG_DWORD to 1)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection" /v "DisableRealtimeMonitoring" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection (set DisableRealtimeMonitoring REG_DWORD to 1)" >> "%log%"
echo "DisableRealtimeMonitoring only works when windows defender is on and change in windows security app" >> "%log%"

:: echo "MSEC SERVICES 1 DEFAULTS" >> "%log%"
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender (set REG_DWORD DisableAntiSpyware 0)" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender (set REG_DWORD DisableAntiVirus 0)" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Features" /v "TamperProtection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender\Features (set REG_DWORD TamperProtection 1)" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection" /v "DpaDisabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection (set DpaDisabled REG_DWORD to 0)" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection" /v "DisableRealtimeMonitoring" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection (set DisableRealtimeMonitoring REG_DWORD to 0)" >> "%log%"

echo "MSEC SERVICES 2 DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService (set REG_DWORD 3)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WbioSrvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo WbioSrvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Sense" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo Sense starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefusersvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

:: echo "MSEC SERVICES 2 DEFAULTS" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService" /v "DelayedAutoStart" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService (set REG_DWORD 2)" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WbioSrvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo WbioSrvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefsvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Sense" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo Sense starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefusersvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "MSEC HARDCORE SERVICES DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdBoot" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdBoot (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdFilter" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdFilter (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo WdNisDrv starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo WdNisSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc (set REG_DWORD 4)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WinDefend" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WinDefend (set REG_DWORD 4)" >> "%log%"

:: echo "MSEC HARDCORE SERVICES DEFAULTS" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc (set REG_DWORD 2)" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdBoot" /v "Start" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdBoot (set REG_DWORD 0)" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdFilter" /v "Start" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdFilter (set REG_DWORD 0)" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo WdNisDrv starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv (set REG_DWORD 3)" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
:: sc triggerinfo WdNisSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc (set REG_DWORD 3)" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WinDefend" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: echo "CHANGE* HKLM\SYSTEM\CurrentControlSet\Services\WinDefend (set REG_DWORD 2)" >> "%log%"

echo "THE MICROSOFT ACCOUNT SIGN IN ASSISTANT SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wlidsvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo wlidsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "TCP/IP NETBIOS HELPER" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lmhosts" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo lmhosts starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "WINDOWS NETWORK DATA USAGE DRIVER" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Ndu" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "QUALITY WINDOWS AUDIO VIDEO EXPERIENCE (QOS FOR MULTIMEDIA)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\QWAVE" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo "SCREEN CAPTURE SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CaptureService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo CaptureService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CLIPBOARD HISTORY SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\cbdhsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo cbdhsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "DATA USAGE SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DusmSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo DusmSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CAMERA STREAMING SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServer" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo FrameServer starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CAMERA MONITORING SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServerMonitor" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "SMS/MESSAGING INTEGRATION SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MessagingService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo MessagingService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "PHONE INTEGRATION SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PhoneSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo PhoneSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CONTACT/CALENDAR INDEXING SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PimIndexMaintenanceSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo PimIndexMaintenanceSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "PLAN9 FILE SYSTEM REDIRECTOR" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\P9RdrService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "RETAIL DEMO MODE SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RetailDemo" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "STILL IMAGE SERVICE (SCANNER/CAMERA)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StiSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "USER EXPERIENCE VIRTUALIZATION AGENT" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UevAgentService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo UevAgentService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "USER CONSENT UI SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ConsentUxUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo ConsentUxUserSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CRYPTOGRAPHIC SERVICES" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CryptSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "TRUSTED PLATFORM MODULE PROVISIONING" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Intel(R) TPM Provisioning Service" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo "DISPLAY BROKER FOR DESKTOP" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DispBrokerDesktopSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\DispBrokerDesktopSvc" /v "DelayedAutoStart" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISPLAY COLOR/BRIGHTNESS ENHANCEMENTS" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DisplayEnhancementService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo "WINDOWS EVENT LOG SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\EventLog" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "WINDOWS FONT CACHE SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FontCache" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "WINDOWS INSTALLER SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\msiserver" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo msiserver starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "PROGRAM COMPATIBILITY ASSISTANT SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PcaSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo PcaSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "NETWORK CONNECTIONS SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Netman" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo Netman starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "NETWORK LIST SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\netprofm" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo netprofm starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "PLUG AND PLAY SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PlugPlay" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo PlugPlay starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "STORAGE SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StorSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo StorSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "TEXT INPUT FRAMEWORK NEVER DISABLE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TextInputManagementService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo TextInputManagementService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
echo "[SC] OpenService FAILED 5 (starttype Access is denied)" >> "%log%"

echo "DISTRIBUTED LINK TRACKING CLIENT" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrkWks" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\TrkWks default 2" >> "%log%"

echo "WINDOWS MANAGEMENT INSTRUMENTATION" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Winmgmt" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "WINDOWS SECURITY CENTER SERVICE" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
echo "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc default 2" >> "%log%"

echo "WINDOWS TIME" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\W32Time" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo W32Time starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\W32Time\Parameters" /v "Type" /t REG_SZ /d NTP /f >> "%log%" 2>&1

echo "XBOX STUFF (ONLY NEED XBOXGIP FOR CONTROLLER)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblAuthManager" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo XblAuthManager starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblGameSave" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo XblGameSave starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\xboxgip" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxGipSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo XboxGipSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxNetApiSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo XboxNetApiSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: schtasks /change /tn "\Microsoft\Windows\Shell\XblGameSaveTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\XblGameSave\XblGameSaveTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "BITLOCKER DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BDESVC" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo BDESVC starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "DISABLES OTHER WINDOWS AI STUFF" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsAADCloudSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsCloudSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsMSACloudSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "SafeSearchMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "AllowRecallEnablement" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableAIDataAnalysis" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableCocreator" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableGenerativeFill" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableImageCreator" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetCopilotHardwareKey" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetDenyUriListForRecall" /t REG_SZ /d "*" /f >> "%log%" 2>&1

echo "DISABLE AUTOCORRECT/SPELLCHECK/SUGGESTIONS/PREDICTION/ECT" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\InputPersonalization\TrainedDataStore" /v "HarvestContacts" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableAutocorrection" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\TabletTip\1.7" /v "EnableSpellchecking" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Input\TIPC" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "DisableHwkbAutocorrection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "DisableHwkbTextPrediction" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictCloudSuggestions" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictImplicitInkCollection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\InputPersonalization" /v "RestrictImplicitTextCollection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v "NoLockScreen" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "AllowClipboardHistory" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "AllowCrossDeviceClipboard" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "YOU DON'T WANT SEARCH BOX AND RANDOM IO FROM SEARCHINDEXER/SEARCHHOST AND 6X MSEDGEWEBVIEW2" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableSearch" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSearch" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: echo "YOU WANT SEARCH BOX AND RANDOM IO FROM SEARCHINDEXER/SEARCHHOST AND 6X MSEDGEWEBVIEW2" >> "%log%"
:: reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableSearch" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSearch" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo "FILE HISTORY SERVICE (FHSVC) STUFF DISABLED" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\FileHistory" /v "Disabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows\FileHistory" /v "Disabled" /f
reg add "HKLM\SYSTEM\CurrentControlSet\Services\fhsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo fhsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\FileHistory\File History (maintenance mode)" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "PERSONALIZATION/EXPLORER/CONTROL PANEL TWEAKS/THEMES" >> "%log%"
reg add "HKCU\Control Panel\Desktop" /v "AutoEndTasks" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "HungAppTimeout" /t REG_SZ /d 5000 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "WaitToKillAppTimeout" /t REG_SZ /d 20000 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop\WindowMetrics" /v "MinAnimate" /t REG_SZ /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /ve /t REG_SZ /d "" /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Accessibility" /v "Animation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "AutoCheckSelect" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DisableThumbnailCache" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DontPrettyPath" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "EnableBalloonTips" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "EnableToolTips" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ExtendedUIHoverTime" /t REG_DWORD /d 5000 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "Hidden" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "HideFileExt" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "IconsOnly" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "LaunchTo" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ListviewAlphaSelect" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowInfoTip" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowSuperHidden" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\FileSystem" /v "LongPathsEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowTaskViewButton" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarGlomLevel" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\OperationStatusManager" /v "EnthusiastMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Serialize" /v "StartupDelayInMSec" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Serialize" /v "StartupDelayInMSec" /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects" /v "VisualFXSetting" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Wallpapers" /v "BackgroundType" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IndexerOption" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsDeviceSearchHistoryEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v "AppsUseLightTheme" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v "ColorPrevalence" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v "EnableTransparency" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v "SystemUsesLightTheme" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\DWM" /v "AccentColor" /t REG_DWORD /d 0xff007d00 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\DWM" /v "ColorPrevalence" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FlyoutMenuSettings" /v "ShowHibernateOption" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoAutorun" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoDriveTypeAutoRun" /t REG_DWORD /d 255 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableFirstLogonAnimation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableNotificationCenter" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableSearchBoxSuggestions" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "HidePeopleBar" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "HideSCAMeetNow" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Chat" /v "ChatIcon" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "EnableDynamicContentInWSB" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE WINDOWS FEEDBACK NOTIFICATIONS (SYSTEM INITIATED USER FEEDBACK/SIUF/TELEMETRY/CEP)" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "NumberOfSIUFInPeriod" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "PeriodInNanoSeconds" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Feedback\Siuf\DmClient" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Feedback\Siuf\DmClientOnScenarioDownload" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "DISABLE LOCATION ACCESS PROMPTS" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\ConsentStore\location" /v "Value" /t REG_SZ /d Deny /f >> "%log%" 2>&1

echo "DISABLE CONTENT DELIVERY AND APP SUGGESTIONS" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "ContentDeliveryAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "FeatureManagementEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "OEMPreInstalledAppsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEverEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SilentInstalledAppsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SoftLandingEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SystemPaneSuggestionsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE SUBSCRIBED CONTENT AND RECOMMENDATIONS" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContentEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-310093Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-314559Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-314563Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338387Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338388Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338389Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338393Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353694Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353696Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353698Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-88000326Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE ONEDRIVE RESUME/LOCAL RESUME (CROSSDEVICERESUME)" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsOneDriveResumeAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsResumeAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE CONNECTED DEVICES PLATFORM (CDP)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableCdp" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE BING SEARCH AND SEARCH HISTORY" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "BingSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "DeviceHistoryEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "HistoryViewEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE USER PROFILE ENGAGEMENT PROMPTS" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\UserProfileEngagement" /v "ScoobeSystemSettingEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE WINDOWS EXPERIENCE FEATURES" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowClipboardHistory" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowScreenRecorder" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowVoiceRecording" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\EnableOrganizationalMessages" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE WINDOWS SPOTLIGHT AND CONSUMER CONTENT" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowSpotlightCollection" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowWindowsSpotlight" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowWindowsSpotlightOnActionCenter" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowWindowsSpotlightOnSettings" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowWindowsSpotlightWindowsWelcomeExperience" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\DisableCloudOptimizedContent" /v "Value" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\DisableConsumerAccountStateContent" /v "Value" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\DisableShareAppPromotions" /v "Value" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE FEEDBACK AND BROWSER SYNC" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\DoNotShowFeedbackNotifications" /v "Value" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\DoNotSyncBrowserSettings" /v "Value" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\PreventUsersFromTurningOnBrowserSyncing" /v "Value" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "SIGN IN OPTIONS (PIN/PICTURE PASSWORD/BIOMETRIC/ETC)" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Settings\AllowSignInOptions" /v "value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE SYSTEM TIPS AND ONLINE FEATURES" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Settings\AllowDataSense" /v "value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Settings\AllowFindMyDevice" /v "value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Settings\AllowOnlineTips" /v "value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Settings\AllowWorkplace" /v "value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE CLOUD CONTENT AND SPOTLIGHT FEATURES" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "ConfigureWindowsSpotlight" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableCloudOptimizedContent" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableConsumerAccountStateContent" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableSoftLanding" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableTailoredExperiencesWithDiagnosticData" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableThirdPartySuggestions" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsConsumerFeatures" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightFeatures" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnActionCenter" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnSettings" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightWindowsWelcomeExperience" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "IncludeEnterpriseSpotlight" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE PASSWORD REVEAL AND CREDENTIAL PROMPTS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CredUI" /v "DisablePasswordReveal" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE HANDWRITING DATA SHARING" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\HandwritinReports" /v "PreventHandwritinReports" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "PreventHandwritingDataSharing" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE SETTINGS SYNC (ACROSS DEVICES VIA YOUR MICROSOFT ACCOUNT)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSync" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSyncUserOverride" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE WEB SEARCH AND CLOUD INTEGRATION" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowCloudSearch" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowSearchToUseLocation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchPrivacy" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWeb" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWebOverMeteredConnections" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableWebSearch" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE ONLINE DRM LICENSE CHECKS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\WMDRM" /v "DisableOnline" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE ASSISTANCE AND BIOMETRICS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoActiveHelp" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoExplicitFeedback" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Biometrics" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE SOFTWARE PROTECTION TICKET" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\CurrentVersion\SOFTWARE Protection Platform" /v "NoGenTicket" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "DISABLE MULTICAST DNS" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v "EnableMulticast" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DISABLE PROGRAM COMPATIBILITY ASSISTANT (PCA)" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisablePCA" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "INVENTORY AND COMPATIBILITY APPRAISAL SERVICE (INVENTORYSVC)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\InventorySvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\InventorySvc" /v "DelayedAutoStart" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "WINDOWS TELEMETRY/DATA COLLECTION/CUSTOMER EXPERIENCE PROGRAM" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\ScheduledDiagnostics" /v "EnabledExecution" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Reporting" /v "Disabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CEIPEnable" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CorporateSQMURL" /t REG_SZ /d 0.0.0.0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient\Windows" /v "CEIPEnable" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "AITEnable" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisableInventory" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DisableTelemetryOptInSettingsUx" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Reporting" /v "Disabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\AutoLogger-Diagtrack-Listener" /v "Start" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\SQMLogger" /v "Start" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Error Reporting\QueueReporting" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "CORTANA STUFF" >> "%log%"
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CanCortanaBeEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Windows Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowCortana" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowCortana" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "HIBERNATE DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Power" /v "HibernateEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "CLEAR VIRTUAL MEMORY FOR SECURITY (SLOWS DOWN SHUTDOWN TIME)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v "ClearPageFileAtShutdown" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "SMART APP CONTROL" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\CI\Policy" /v "VerifiedAndReputablePolicyState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "DEFAULT CPU PRIORITY CONTROL" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "IRQ8Priority" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo "MAPS STUFF" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MapsBroker" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\Maps" /v "AutoUpdateEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Maps\MapsToastTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "ALLOW UPGRADES WITH UNSUPPORTED TPM OR CPU" >> "%log%"
reg add "HKLM\SYSTEM\Setup\MoSetup" /v "AllowUpgradesWithUnsupportedTPMOrCPU" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo "MICROSOFT CLOUD IDENTITY SERVICE DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\cloudidsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo cloudidsvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "MICROSOFT CLOUD BACKUP SERVICE/CLOUD RESET DISABLED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CloudBackupRestoreSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo "OEM UTILITIES/LMS/AMT/ME/FIRMWARE/MEIX64/WMANSVC (INTEL ME SENSORS/WMI BASED ME DATA/BACKEND MANAGEMENT UTILITIES)" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\jhi_service" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo jhi_service starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MEIx64" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WManSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo WManSvc starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WMIRegistrationService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
sc triggerinfo WMIRegistrationService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "DISABLE APPLE MOBILE DEVICE SERVICE STUFF" >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Apple Mobile Device Service" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppleKmdfFilter" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppleLowerFilter" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\Office\Outlook\Addins\OutlookChangeNotifier.Connect" /f >> "%log%" 2>&1

echo "EDGE STUFF" >> "%log%"
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AlternatPagesEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AutofillAddressEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AutofillCreditCardEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "BackgroundModeEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "BlockThirdPartyCookies" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "ConfigureDoNotTrack" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "DiagnosticData" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "EnhanceSecurityMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "HideFirstRunExperience" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "NewTabPagePrerenderEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "PasswordManagerEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "PrelaunchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SearchSuggestEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "CreateDesktopShortcutDefault" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "UpdateDefault" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\Main" /v "AllowPrelaunch" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\PhishingFilter" /v "EnabledV9" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\TabPreloader" /v "AllowTabPreloading" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
schtasks /change /tn "\MicrosoftEdgeUpdateTaskMachineCore{3EC71BEB-F725-4450-AD64-9D1C829FDFDA}" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\MicrosoftEdgeUpdateTaskMachineUA{30E97D55-D54A-4306-BE07-817C718A05B7}" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\edgeupdate" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo edgeupdate starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\edgeupdatem" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo edgeupdatem starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\MicrosoftEdgeElevationService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: sc triggerinfo MicrosoftEdgeElevationService starttype= all 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects\{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}" /ve /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects\{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}" /ve /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Ext\CLSID" /v "{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Policies\Ext\CLSID" /v "{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\EdgeUpdate" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects" /f >> "%log%" 2>&1

:: echo "EDGE STUFF MICROSOFTEDGEUPDATETASKMACHINE DISABLED" >> "%log%"
:: for /f "tokens=2 delims=:" %%T in (
::     'schtasks /query /fo LIST /v ^| findstr /i "TaskName:" ^| findstr /i "MicrosoftEdgeUpdateTaskMachine"'
:: ) do (
::     set "raw=%%T"
::     set "task=!raw:~1!"
::     set "task=!task: =!"
::     echo Found task: !task! >> "%log%"
::     schtasks /change /tn "!task!" /disable >> "%log%" 2>&1
:: )

:: echo "EDGE STUFF DELETE EDGE ACTIVE SETUP STUBPATH" >> "%log%"
:: for /f "tokens=*" %%a in (
::     'reg query "HKLM\SOFTWARE\Microsoft\Active Setup\Installed Components" 2^>nul'
:: ) do (
::     echo PROCESSING %%a >> "%log%"
::     for /f "tokens=2,*" %%b in (
::         'reg query "%%a" /v StubPath 2^>nul ^| find /i "StubPath" ^| find /i "edge"'
::     ) do (
::         echo FOUND STUBPATH IN %%a >> "%log%"
::         reg delete "%%a" /v "StubPath" /f >> "%log%" 2>&1
::     )
:: )

:: echo "EDGE STUFF DELETE EDGEAUTOLAUNCH" >> "%log%"
:: for /f "tokens=1,2,*" %%a in (
::     'reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2^>nul ^| find /i "MicrosoftEdgeAutoLaunch_"'
:: ) do (
::     echo FOUND %%a >> "%log%"
::     reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "%%a" /f >> "%log%" 2>&1
:: )

:: echo "NETWORK RESET (WILL WIPE OUT ALL CUSTOM TCP/IP TWEAKS AND RESTORE THE NETWORKING STACK TO ITS ORIGINAL STATE PROBABLY)" >> "%log%"
:: netsh int ip reset
:: netsh winsock reset
:: netsh int tcp reset

:: echo "DEFAULT SECURE/INTENDED BEHAVIOR (TO NOT BE SET TO 1)" >> "%log%"
:: reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "DoNotUseNLA" /f >> "%log%" 2>&1

:: echo "STRICT PARSING" >> "%log%"
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Http\Parameters" /v "HttpAllowLenientChunkExtParsing" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

:: echo "FOR MESSAGE QUEUING (MSMQ) (OPTIONAL FEATURE) (ENABLE-WINDOWSOPTIONALFEATURE -ONLINE -FEATURENAME MSMQ-SERVER -ALL)" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\MSMQ\Parameters" /v "TCPNoDelay" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

:: echo "VARIOUS NETWORK DEFAULTS" >> "%log%"
:: netsh int tcp set supplemental internet congestionprovider=default >> "%log%" 2>&1
:: netsh interface ipv4 set subinterface "Ethernet" mtu=1500 store=persistent >> "%log%" 2>&1
:: netsh interface ipv6 set subinterface "Ethernet" mtu=1500 store=persistent >> "%log%" 2>&1
:: powershell -NoProfile -Command "Enable-NetAdapterChecksumOffload -Name * -ErrorAction SilentlyContinue" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Enable-NetAdapterLso -Name * -ErrorAction SilentlyContinue" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -Chimney disabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -ReceiveSideScaling enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -AutoTuningLevelLocal normal" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -EcnCapability disabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -InitialRto 3000" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -MaxSynRetransmissions 4" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -MinRto 300" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -NonSackRttResiliency enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -ScalingHeuristics enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetTCPSetting -SettingName internet -Timestamps enabled" >> "%log%" 2>&1

:: echo "VARIOUS NETWORK DEFAULTS LEGACY/LOW IMPACT" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_MAXCONNECTIONSPER1_0SERVER" /v "explorer.exe" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_MAXCONNECTIONSPER1_0SERVER" /v "iexplore.exe" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_MAXCONNECTIONSPERSERVER" /v "explorer.exe" /t REG_DWORD /d 6 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_MAXCONNECTIONSPERSERVER" /v "iexplore.exe" /t REG_DWORD /d 6 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v "LargeSystemCache" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v "Size" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v "DefaultTTL" /t REG_DWORD /d 128 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v "MaxUserPort" /t REG_DWORD /d 5000 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v "Tcp1323Opts" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\ServiceProvider" /v "DnsPriority" /t REG_DWORD /d 2000 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\ServiceProvider" /v "HostsPriority" /t REG_DWORD /d 500 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\ServiceProvider" /v "LocalPriority" /t REG_DWORD /d 499 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\ServiceProvider" /v "NetbtPriority" /t REG_DWORD /d 2001 /f >> "%log%" 2>&1

:: echo "-RECEIVESEGMENTCOALESCING DISABLED ✅ CAN LOWER LATENCY JITTER ⚠️ MAY REDUCE THROUGHPUT ON HEAVY TRANSFERS" >> "%log%"
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -ReceiveSegmentCoalescing disabled" >> "%log%" 2>&1
:: echo "-RECEIVESEGMENTCOALESCING DEFAULT" >> "%log%"
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -ReceiveSegmentCoalescing enabled" >> "%log%" 2>&1
:: echo "NETOFFLOADGLOBALSETTING DEFAULTS CHECK WITH (GET-NETOFFLOADGLOBALSETTING)" >> "%log%"
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -ReceiveSideScaling Enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -ReceiveSegmentCoalescing Enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -Chimney Disabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -TaskOffload Enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -NetworkDirect Enabled" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -NetworkDirectAcrossIPSubnets Blocked" >> "%log%" 2>&1
:: powershell -NoProfile -Command "Set-NetOffloadGlobalSetting -PacketCoalescingFilter Enabled" >> "%log%" 2>&1

echo "10 ENABLES NETWORK THROTTLING 4294967295(-1) DISABLES" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "NetworkThrottlingIndex" /t REG_DWORD /d 4294967295 /f >> "%log%" 2>&1

echo "VARIOUS NETWORK TWEAKS" >> "%log%"
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "SystemResponsiveness" /t REG_DWORD /d 10 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Psched" /v "NonBestEffortLimit" /t REG_DWORD /d 10 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v "TcpTimedWaitDelay" /t REG_DWORD /d 60 /f >> "%log%" 2>&1
:: echo "VARIOUS NETWORK TWEAKS DEFAULTS" >> "%log%"
:: reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "SystemResponsiveness" /t REG_DWORD /d 20 /f >> "%log%" 2>&1
:: reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Psched" /v "NonBestEffortLimit" /t REG_DWORD /d 20 /f >> "%log%" 2>&1
:: reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v "TcpTimedWaitDelay" /t REG_DWORD /d 120 /f >> "%log%" 2>&1

:: echo "TCPIP TWEAKS" >> "%log%"
:: echo "TCPACKFREQUENCY=1 ✅ LOWERS LATENCY IN SOME GAMES ⚠️ ADDS OVERHEAD AND CAN REDUCE THROUGHPUT" >> "%log%"
:: echo "TCPNODELAY=1 ✅ IMPROVES RESPONSIVENESS FOR LATENCY SENSITIVE APPS ⚠️ WASTES BANDWIDTH WITH MANY SMALL PACKETS" >> "%log%"
:: for /f %%G in ('powershell -NoProfile -Command "Get-NetAdapter | Where-Object {$_.Status -eq 'Up'} | ForEach-Object {$_.InterfaceGuid}"') do (
::     set "guid=%%G"
::     call :applytweaks
:: )
:: goto :continue
:: :applytweaks
:: set "guid=!guid: =!"
:: if not defined guid (
::     goto :TT
:: )
:: echo APPLYING TCPIP TWEAKS TO !guid! >> "%log%"
:: set "regpath=HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\!guid!"
:: reg add "%regpath%" /v "TcpAckFrequency" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg add "%regpath%" /v "TcpNoDelay" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "DisableTaskOffload" /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "Tcp1323Opts" /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "TcpDelAckTicks" /f >> "%log%" 2>&1
:: goto :TT
:: :TT

:: echo "TCPIP TWEAKS DEFAULT" >> "%log%"
:: for /f %%G in ('powershell -NoProfile -Command "Get-NetAdapter | Where-Object {$_.Status -eq 'Up'} | ForEach-Object {$_.InterfaceGuid}"') do (
::     set "guid=%%G"
::     call :applytweaks
:: )
:: goto :continue
:: :applytweaks
:: set "guid=!guid: =!"
:: if not defined guid (
::     goto :TTD
:: )
:: echo APPLYING TCPIP TWEAKS TO !guid! >> "%log%"
:: set "regpath=HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\!guid!"
:: reg delete "%regpath%" /v "DisableTaskOffload" /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "Tcp1323Opts" /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "TcpAckFrequency" /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "TcpDelAckTicks" /f >> "%log%" 2>&1
:: reg delete "%regpath%" /v "TcpNoDelay" /f >> "%log%" 2>&1
:: goto :TTD
:: :TTD

:: echo "SCHTASKS POWERSHELL SCRIPT FOR INFO RUN TWICE" >> "%log%"
:: SCHTASKS /QUERY /FO CSV | ConvertFrom-Csv |
::     Group-Object TaskName |
::     ForEach-Object {
::         [PSCustomObject]@{
::             TaskName = $_.Name
::             Status   = ($_.Group | Select-Object -First 1).Status
::             Triggers = ($_.Group."Next Run Time" -join ", ")
::         }
::     }

echo "SCHTASKS DISABLE" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser Exp" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Application Experience\PcaPatchDbTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Application Experience\StartupAppTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\ApplicationData\DsSvcCleanup" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\CloudExperienceHost\CreateObjectTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\ConsentUX\UnifiedConsent\UnifiedConsentSyncTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\ConsentUX\UnifiedConsent\UnifiedConsentSyncTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\Consolidator" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsbCeip" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Device Information\Device" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Diagnosis\RecommendedTroubleshootingScanner" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Diagnosis\Scheduled" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskCleanup\SilentCleanup" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticResolver" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\DiskFootprint\Diagnostics" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\BootstrapUsageDataReporting" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\BootstrapUsageDataReporting" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\GovernedFeatureUsageProcessing" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\UsageDataFlushing" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\UsageDataFlushing" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\UsageDataReceiver" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\UsageDataReceiver" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\UsageDataReporting" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\FeatureConfig\UsageDataReporting" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Flighting\OneSettings\RefreshCache" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Maintenance\WinSAT" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\PI\Sqm-Tasks" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyMonitor" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyRefreshTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Sustainability\PowerGridForecastTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Sustainability\PowerGridForecastTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Sustainability\SustainabilityTelemetry" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UsageAndQualityInsights\UsageAndQualityInsights-MaintenanceTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
echo "\Microsoft\Windows\UsageAndQualityInsights\UsageAndQualityInsights-MaintenanceTask Access is denied" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WindowsAI\Recall\InitialConfiguration" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WwanSvc\NotificationTask" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WwanSvc\OobeDiscovery" /disable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

echo "SCHTASKS ENABLE" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Autochk\Proxy" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\License Manager\TempSignedLicenseExchange" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Printing\PrinterCleanupTask" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Printing\PrintJobCleanupTask" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Subscription\EnableLicenseAcquisition" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Time Synchronization\ForceSynchronizeTime" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\TPM\Tpm-Maintenance" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\UPnP\UPnPHostConfig" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\USB\Usb-Notifications" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\WDI\ResolutionHost" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"
schtasks /change /tn "\Microsoft\Windows\Windows Filtering Platform\BfeOnServiceStartTypeChange" /enable 2>&1 | findstr /I "ERROR FAILED" >> "%log%"

:: echo "UPDATE DELETES" >> "%log%"
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\ClipchampUpdate" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\DevHomeUpdate" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OfficeHubUpdate" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OneDriveSetup" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OutlookUpdate" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\TeamsMachineInstaller" /f >> "%log%" 2>&1
:: reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\WebView2RuntimeUpdate" /f >> "%log%" 2>&1

:: cls
echo "check log on desktop find CHANGE* keys and change them in RegCool.exe(or other) manually if you can" >> "%log%"
echo "check log on desktop find CHANGE* keys and change them in RegCool.exe(or other) manually if you can"
echo "check echos for info remember reg changes are hardcore" >> "%log%"
echo "check echos for info remember reg changes are hardcore"
echo "REBOOT PC" >> "%log%"
echo "REBOOT PC"

powershell -c "(New-Object Media.SoundPlayer 'C:\Windows\Media\tada.wav').PlaySync()"
:: RUNDLL32.EXE user32.dll,UpdatePerUserSystemParameters
:: taskkill /f /im explorer.exe && start explorer.exe
endlocal
pause >nul
```
