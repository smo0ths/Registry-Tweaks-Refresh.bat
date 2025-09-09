# Registry-Tweaks-Refresh.bat v0.4.2
Windows 11 Registry Tweaks
#### this is what i use, make the bat file and run it, skim threw the stuff you might not want to change and you need to find 2 of your {GUID}'s
#### %windir%\System32\SystemPropertiesProtection.exe (open and create one)
#### use Autoruns64, OOSU10++ and uninstallers/registry cleaners to get all the other stuff

```python
@echo off
color 02
title %~nx0 - %date% %time%
set "log=%userprofile%\desktop\registry-tweaks-refresh.txt"

:restorechoice
set /P restorechoice=y creates restore point, n skips [y/n]:
if /I "%restorechoice%"=="y" goto yesrestore
if /I "%restorechoice%"=="n" goto norestore 
goto invalidchoice

:yesrestore
echo [creating restore point] >> "%log%"
echo [creating restore point]
wmic.exe /Namespace:\\root\default Path SystemRestore Call CreateRestorePoint "before Registry-Tweaks-Refresh v0.4.2.bat", 100, 7
goto updatechoice

:norestore
echo [no restore point created] >> "%log%"
echo [no restore point created]
goto updatechoice

:updatechoice
set /P updatechoice=y disables windows updates, n resets/enables/defaults [y/n]:
if /I "%updatechoice%"=="y" goto disableupdates
if /I "%updatechoice%"=="n" goto enableupdates
goto invalidchoice

:disableupdates
echo [disabling windows updates] >> "%log%"
echo [disabling windows updates]
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto printerchoice

:enableupdates
echo [enabling windows updates] >> "%log%"
echo [enabling windows updates]
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DriverSearching" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UpdatePolicy\PolicyState" /v "ExcludeWUDrivers" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "SearchOrderConfig" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DoSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UsoSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wscsvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
goto printerchoice

:printerchoice
set /P printerchoice=y disables printer, n resets/enables/defaults [y/n]:
if /I "%printerchoice%"=="y" goto disableprinter
if /I "%printerchoice%"=="n" goto enableprinter
goto invalidchoice

:disableprinter
echo [disabling printer] >> "%log%"
echo [disabling printer]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto copilotchoice

:enableprinter
echo [enabling printer] >> "%log%"
echo [enabling printer]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Spooler" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintDeviceConfigurationService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PrintWorkflowUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
goto copilotchoice

:copilotchoice
set /P copilotchoice=y disables windows copilot, n resets/enables/defaults [y/n]:
if /I "%copilotchoice%"=="y" goto disablecopilot
if /I "%copilotchoice%"=="n" goto enablecopilot
goto invalidchoice

:disablecopilot
echo [disabling windows copilot] >> "%log%"
echo [disabling windows copilot]
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto interestschoice

:enablecopilot
echo [enabling windows copilot] >> "%log%"
echo [enabling windows copilot]
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsCopilot" /v "TurnOffWindowsCopilot" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSAIFabricSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
goto interestschoice

:interestschoice
set /P interestschoice=y disables news and interests, n resets/enables/defaults [y/n]:
if /I "%interestschoice%"=="y" goto disableinterests
if /I "%interestschoice%"=="n" goto enableinterests
goto invalidchoice

:disableinterests
echo [disable news and interests/notifications ect on taskbar 1 manually] >> "%log%"
echo [disable news and interests/notifications ect on taskbar 1 manually]
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /t REG_DWORD /d 0 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnUserService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
goto touchchoice

:enableinterests
echo [enable news and interests on taskbar 1 manually] >> "%log%"
echo [enable news and interests on taskbar 1 manually]
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Feeds" /v "ShellFeedsTaskbarViewMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnService" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WpnUserService" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows" /v "EnableFeeds" /f >> "%log%" 2>&1
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Feeds" /v "EnableFeeds" /f >> "%log%" 2>&1
goto touchchoice

:touchchoice
set /P touchchoice=y disables touch screen stuff, n resets/enables/defaults [y/n]:
if /I "%touchchoice%"=="y" goto disabletouch
if /I "%touchchoice%"=="n" goto enabletouch
goto invalidchoice

:disabletouch
echo [disable touch screen stuff] >> "%log%"
echo [disable touch screen stuff]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
goto endscript

:enabletouch
echo [enable touch screen stuff] >> "%log%"
echo [enable touch screen stuff]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PenService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
goto endscript

:invalidchoice
echo Invalid command run bat again
goto endscript
:endscript

echo [find your {GUID} in the Interfaces address to apply Tcpip tweaks correctly] >> "%log%"
echo [find your {GUID} in the Interfaces address to apply Tcpip tweaks correctly]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\{8e2bcbf1-0f7c-4435-bd5b-88cabdedbf02}" /v "DisableTaskOffload" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\{8e2bcbf1-0f7c-4435-bd5b-88cabdedbf02}" /v "Tcp1323Opts" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\{8e2bcbf1-0f7c-4435-bd5b-88cabdedbf02}" /v "TcpAckFrequency" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\{8e2bcbf1-0f7c-4435-bd5b-88cabdedbf02}" /v "TcpDelAckTicks" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces\{8e2bcbf1-0f7c-4435-bd5b-88cabdedbf02}" /v "TcpNoDelay" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

:: echo [Enhanced Fullscreen Exclusive/Fullscreen Exclusive disabled] >> "%log%"
:: echo [Enhanced Fullscreen Exclusive/Fullscreen Exclusive disabled]
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
:: reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 2 /f >> "%log%" 2>&1

echo [Enhanced Fullscreen Exclusive/Fullscreen Exclusive enabled] >> "%log%"
echo [Enhanced Fullscreen Exclusive/Fullscreen Exclusive enabled]
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_EFSEFeatureFlags" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_FSEBehavior" /t REG_DWORD /d 0 /f >> "%log%" 2>&1

echo [disabling bluetooth] >> "%log%"
echo [disabling bluetooth]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTAGService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthav" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHPORT" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\bthserv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BTHUSB" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DeviceAssociationService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicePickerUserSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DevicesFlowUserSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [disables all IPv6 components] >> "%log%"
echo [disables all IPv6 components]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" /v "DisabledComponents" /t REG_DWORD /d 255 /f >> "%log%" 2>&1

echo [REG_SZ] >> "%log%"
echo [REG_SZ]
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\ConsentStore\location" /v "Value" /t REG_SZ /d Deny /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v "SmartScreenEnabled" /t REG_SZ /d off /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CorporateSQMURL" /t REG_SZ /d 0.0.0.0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\SmartScreen" /v "ConfigureAppInstallControl" /t REG_SZ /d Anywhere /f >> "%log%" 2>&1

echo [disabling sensors] >> "%log%"
echo [disabling sensors]
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{09485F5A-759E-4A45-B622-5C7F2FCE985E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C5C4656-0E77-4F44-BF4C-8C0C8A76E8A7}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{5C7C1A12-3B7D-4B4B-9F4E-B1A1B6E0E1D8}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{76B5CE0A-17DD-414D-93A1-E127F40BDF6E}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{B5C5BB9C-DAF8-4661-BB0F-5A4E3D8C6F58}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{BFA794E4-F964-4FDB-90F6-51056BFE4B44}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Sensor\Overrides\{C04D7B5C-9C2F-4C32-9C47-5C6E2F0E6D98}" /v "SensorPermissionState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SensorService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [SMB stuff] >> "%log%"
echo [SMB stuff]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" /v "AutoShareWks" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [msec do manually] >> "%log%"
echo [msec do manually]
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SecurityHealthService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [msec] >> "%log%"
echo [msec]
reg add "HKLM\SOFTWARE\Microsoft\RemovalTools\MpGears" /v "HeartbeatTrackingIndex" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontOfferThroughWUAU" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\MRT" /v "DontReportInfectionInformation" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "AllowFastServiceStartup" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiSpyware" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableAntiVirus" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableMsMpEng" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableSpecialRunningModes" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "ServiceKeepAlive" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableBehaviorMonitoring" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableOnAccessProtection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableRealtimeMonitoring" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /v "DisableScanOnRealtimeEnable" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\SmartScreen" /v "ConfigureAppInstallControlEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "DisableBlockAtFirstSeen" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SpyNetReporting" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Spynet" /v "SubmitSamplesConsent" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableActivityFeed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreen" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreenForStoreApps" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "PublishUserActivities" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "UploadUserActivities" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WTDS\Components" /v "CaptureThreatWindow" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WTDS\Components" /v "NotifyMalicious" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WTDS\Components" /v "NotifyPasswordReuse" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WTDS\Components" /v "NotifyUnsafeApp" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WTDS\Components" /v "ServiceEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MDCoreSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Sense" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WbioSrvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdBoot" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdFilter" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisDrv" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdNisSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\webthreatdefusersvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WinDefend" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [disabling network resource sharing (apple/smart tv/etc.)] >> "%log%"
echo [disabling network resource sharing (apple/smart tv/etc.)]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\fdPHost" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FDResPub" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [services do manually] >> "%log%"
echo [services do manually]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrkWks" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TrustedInstaller" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WdiSystemHost" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo [caching/indexing for slow drives disabled] >> "%log%"
echo [caching/indexing for slow drives disabled]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SysMain" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WSearch" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [universal windows platform(UWP/app model)/store/backend/framework] >> "%log%"
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppReadiness" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppXSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\camsvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CDPUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ClipSVC" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\InstallService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NcbService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\OneSyncSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StateRepository" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TimeBrokerSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TokenBroker" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UdkUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UnistoreSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UserDataSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo [various disabled services] >> "%log%"
echo [various disabled services]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\AppIDSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BcastDVRUserService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\camsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CaptureService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\cbdhsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DiagTrack" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\dmwappushservice" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DusmSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServer" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FrameServerMonitor" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\iphlpsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\jhi_service" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lfsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\lmhosts" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\MessagingService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Ndu" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\NPSMSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\P9RdrService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PhoneSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PimIndexMaintenanceSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\QWAVE" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteAccess" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RemoteRegistry" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RetailDemo" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\RmSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SSDPSRV" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StiSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\StorSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\UevAgentService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\whesvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\wlidsvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WManSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\WMIRegistrationService" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblAuthManager" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XblGameSave" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxGipSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\XboxNetApiSvc" /v "Start" /t REG_DWORD /d 4 /f >> "%log%" 2>&1

echo [various services] >> "%log%"
echo [various services]
reg add "HKLM\SYSTEM\CurrentControlSet\Services\BITS" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ConsentUxUserSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\CryptSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DispBrokerDesktopSvc" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\DisplayEnhancementService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\EventLog" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\FontCache" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Intel(R) TPM Provisioning Service" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\msiserver" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Netman" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\netprofm" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PcaSvc" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PlugPlay" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\TextInputManagementService" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\W32Time" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Winmgmt" /v "Start" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\xboxgip" /v "Start" /t REG_DWORD /d 3 /f >> "%log%" 2>&1

echo [disables other windows ai stuff] >> "%log%"
echo [disables other windows ai stuff]
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsAADCloudSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsCloudSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsDeviceSearchHistoryEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "IsMSACloudSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\SearchSettings" /v "SafeSearchMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Camera" /v "AllowCamera" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DisableTelemetryOptInSettingsUx" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "DoNotShowFeedbackNotifications" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "AllowRecallEnablement" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableAIDataAnalysis" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableCocreator" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableGenerativeFill" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "DisableImageCreator" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetCopilotHardwareKey" /t REG_SZ /d "" /f >> "%log%" 2>&1
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsAI" /v "SetDenyUriListForRecall" /f >> "%log%" 2>&1

echo [bunch of tweaks] >> "%log%"
echo [bunch of tweaks]
reg add "HKCU\Control Panel\Desktop" /v "AutoEndTasks" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Desktop" /v "MenuShowDelay" /t REG_DWORD /d 400 /f >> "%log%" 2>&1
reg add "HKCU\Control Panel\Mouse" /v "MouseHoverTime" /t REG_DWORD /d 400 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AllowAutoGameMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "AutoGameModeEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "ShowStartupPanel" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\GameBar" /v "UseNexusForGameBarEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Input\TIPC" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\InputPersonalization" /v "RestrictImplicitInkCollection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\InputPersonalization" /v "RestrictImplicitTextCollection" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\InputPersonalization\TrainedDataStore" /v "HarvestContacts" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Personalization\Settings" /v "AcceptedPrivacyPolicy" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "NumberOfSIUFInPeriod" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Siuf\Rules" /v "PeriodInNanoSeconds" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\AppHost" /v "EnableWebContentEvaluation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\AppHost" /v "PreventOverride" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\BackgroundAccessApplications" /v "GlobalUserDisabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "ContentDeliveryAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "FeatureManagementEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "OEMPreInstalledAppsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "PreInstalledAppsEverEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SilentInstalledAppsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SoftLandingEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-310093Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338387Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338388Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338389Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-338393Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContent-353698Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SubscribedContentEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v "SystemPaneSuggestionsEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsOneDriveResumeAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\CrossDeviceResume\Configuration" /v "IsResumeAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "Hidden" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "HideFileExt" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "LaunchTo" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowTaskViewButton" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced\People" /v "PeopleBand" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\OperationStatusManager" /v "EnthusiastMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AllowgameDVR" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\GameDVR" /v "AppCaptureEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "HideSCAMeetNow" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "BingSearchEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CanCortanaBeEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "DeviceHistoryEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "HistoryViewEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\UserProfileEngagement" /v "ScoobeSystemSettingEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Windows Search" /v "CortanaConsent" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoExplicitFeedback" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "ConfigureWindowsSpotlight" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableTailoredExperiencesWithDiagnosticData" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableThirdPartySuggestions" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightFeatures" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnActionCenter" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightOnSettings" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsSpotlightWindowsWelcomeExperience" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "IncludeEnterpriseSpotlight" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\PushNotifications" /v "NoTileApplicationNotification" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableNotificationCenter" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\Explorer" /v "DisableSearchBoxSuggestions" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_DXGIHonorFSEWindowsCompatible" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKCU\SYSTEM\GameConfigStore" /v "GameDVR_HonorUserFSEBehaviorMode" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Input\TIPC" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Browser\AllowSmartScreen" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\Experience\AllowCortana" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableAppInstallControl" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\EnableSmartScreenInShell" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\SmartScreen\PreventOverrideForFilesInShell" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowAutoConnectToWiFiSenseHotspots" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\PolicyManager\default\WiFi\AllowWiFiHotSpotReporting" /v "Value" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\WcmSvc\wifinetworkmanager\config" /v "AutoConnectAllowedOEM" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "NetworkThrottlingIndex" /t REG_DWORD /d 4294967295 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "SystemResponsiveness" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AdvertisingInfo" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AppHost" /v "EnableWebContentEvaluation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "DODownloadMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" /v "SystemSettingsDownloadMode" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Device Metadata" /v "PreventDeviceMetadataFromNetwork" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FlyoutMenuSettings" /v "ShowHibernateOption" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v "MaxTelemetryAllowed" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoAutorun" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoDriveTypeAutoRun" /t REG_DWORD /d 255 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableLUA" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Search" /v "CortanaEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\ScheduledDiagnostics" /v "EnabledExecution" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting" /v "Disabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Assistance\Client\1.0" /v "NoActiveHelp" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Biometrics" /v "Enabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\PushToInstall" /v "DisablePushToInstall" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\SQMClient" /v "CEIPEnable" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\CurrentVersion\Software Protection Platform" /v "NoGenTicket" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v "EnableMulticast" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AdvertisingInfo" /v "DisabledByGroupPolicy" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "AITEnable" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisableInventory" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "DisablePCA" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableCloudOptimizedContent" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableConsumerAccountStateContent" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableSoftLanding" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableThirdPartySuggestions" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CloudContent" /v "DisableWindowsConsumerFeatures" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\CredUI" /v "DisablePasswordReveal" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\FileHistory" /v "Disabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\GameDVR" /v "AllowGameDVR" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\HandwritingErrorReports" /v "PreventHandwritingErrorReports" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocation" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableLocationScripting" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableSensors" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\LocationAndSensors" /v "DisableWindowsLocationProvider" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "AllowBuildPreview" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\PreviewBuilds" /v "EnableConfigFlighting" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSync" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\SettingSync" /v "DisableSettingSyncUserOverride" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\TabletPC" /v "PreventHandwritingDataSharing" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Chat" /v "ChatIcon" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Error Reporting" /v "Disabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowCortana" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "AllowSearchToUseLocation" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchPrivacy" /t REG_DWORD /d 3 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWeb" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "ConnectedSearchUseWebOverMeteredConnections" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableSearch" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v "DisableWebSearch" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AUOptions" /t REG_DWORD /d 2 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AutoInstallMinorUpdates" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "DetectionFrequencyEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoRebootWithLoggedOnUsers" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallDay" /t REG_DWORD /d 4 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallTime" /t REG_DWORD /d 20 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "UseWUServer" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\WMDRM" /v "DisableOnline" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\CI\Policy" /v "VerifiedAndReputablePolicyState" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\FileSystem" /v "LongPathsEnabled" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerThrottling" /v "PowerThrottlingOff" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\PriorityControl" /v "IRQ8Priority" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowFullControl" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Remote Assistance" /v "fAllowToGetHelp" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /v "ClearPageFileAtShutdown" /t REG_DWORD /d 1 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Power" /v "HibernateEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\AutoLogger-Diagtrack-Listener" /v "Start" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Control\WMI\AutoLogger\SQMLogger" /v "Start" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\Maps" /v "AutoUpdateEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SYSTEM\Setup\MoSetup" /v "AllowUpgradesWithUnsupportedTPMOrCPU" /t REG_DWORD /d 1 /f >> "%log%" 2>&1

echo [edge stuff needs your {GUID} found in taskschd.msc to disable EdgeUpdateTask] >> "%log%"
echo [edge stuff needs your {GUID} found in taskschd.msc to disable EdgeUpdateTask]
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "AlternateErrorPagesEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
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
reg add "HKLM\SOFTWARE\Policies\Microsoft\Edge" /v "SmartScreenEnabled" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "CreateDesktopShortcutDefault" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg add "HKLM\SOFTWARE\Policies\Microsoft\EdgeUpdate" /v "UpdateDefault" /t REG_DWORD /d 0 /f >> "%log%" 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\EdgeUpdate" /f >> "%log%" 2>&1
schtasks /change /tn "\MicrosoftEdgeUpdateTaskMachineCore{3EC71BEB-F725-4450-AD64-9D1C829FDFDA}" /disable >> "%log%" 2>&1
schtasks /change /tn "\MicrosoftEdgeUpdateTaskMachineUA{30E97D55-D54A-4306-BE07-817C718A05B7}" /disable >> "%log%" 2>&1

echo [schtasks cannot disable] >> "%log%"
echo [schtasks cannot disable]
schtasks /change /tn "\Microsoft\Windows\Shell\UpdateUserPictureTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Report policies" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Schedule Scan Static Task" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Schedule Scan" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\Start Oobe Expedite Work" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\StartOobeAppsScan_LicenseAccepted" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\StartOobeAppsScanAfterUpdate" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\UIEOrchestrator" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\USO_UxBroker" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UpdateOrchestrator\UUS Failover Task" /disable >> "%log%" 2>&1

echo [schtasks] >> "%log%"
echo [schtasks]
schtasks /change /tn "\Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Application Experience\PcaPatchDbTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Application Experience\StartupAppTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\ApplicationData\DsSvcCleanup" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Autochk\Proxy" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Clip\License Validation" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\CloudExperienceHost\CreateObjectTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\Consolidator" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UnifiedConsentSyncTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataFlushing" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataReceiver" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsageDataReporting" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Customer Experience Improvement Program\UsbCeip" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Diagnosis\RecommendedTroubleshootingScanner" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Diagnosis\Scheduled" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\DiskFootprint\Diagnostics" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Feedback\Siuf\DmClientOnScenarioDownload" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\FileHistory\File History (maintenance mode)" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\License Manager\TempSignedLicenseExchange" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Maintenance\WinSAT" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Maps\MapsToastTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\NetworkDiagnostics\BfeOnServiceStartTypeChange" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\NetworkDiagnostics\ResolutionHost" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\PI\Sqm-Tasks" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\PowerGridForecastTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Power Efficiency Diagnostics\SustainabilityTelemetry" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Printing\PrinterCleanupTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Printing\PrintJobCleanupTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\PushToInstall\LoginCheck" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\PushToInstall\Registration" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\RemoteAssistance\RemoteAssistanceTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Servicing\StartComponentCleanup" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyMonitor" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\FamilySafetyRefreshTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\IndexerAutomaticMaintenance" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\NotificationTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\ThemesSyncedImageDownload" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Shell\XblGameSaveTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Speech\SpeechModelDownloadTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Subscription\EnableLicenseAcquisition" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Subscription\LicenseAcquisition" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Time Synchronization\ForceSynchronizeTime" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\TPM\Tpm-Maintenance" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\UPnP\UPnPHostConfig" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\USB\Usb-Notifications" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\WaaSMedic\MaintenanceWork" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\WiFi\WiFiTask" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cache Maintenance" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Cleanup" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Scheduled Scan" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Defender\Windows Defender Verification" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\Windows Error Reporting\QueueReporting" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\WlanSvc\CDSSync" /disable >> "%log%" 2>&1
schtasks /change /tn "\Microsoft\Windows\WwanSvc\OobeDiscovery" /disable >> "%log%" 2>&1

echo [deletes] >> "%log%"
echo [deletes]
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\DevHomeUpdate" /f >> "%log%" 2>&1
reg delete "HKLM\SOFTWARE\Microsoft\WindowsUpdate\Orchestrator\UScheduler_Oobe\OutlookUpdate" /f >> "%log%" 2>&1

echo [after any windows updates or repairs you will probably need to do this again] >> "%log%"
echo [after any windows updates or repairs you will probably need to do this again]
echo [check registry-tweaks-refresh.txt on your desktop and force errors with a regedit (like RegCool.exe)] >> "%log%"
echo [check registry-tweaks-refresh.txt on your desktop and force errors with a regedit (like RegCool.exe)]
echo [reboot for changes] >> "%log%"
echo [reboot for changes]

pause
```
