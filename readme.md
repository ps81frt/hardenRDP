```powershell
&{

$port=55555
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value $port
New-NetFirewallRule -DisplayName "RDP port $port" -Direction Inbound -Protocol TCP -LocalPort $port -Action Allow
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "UserAuthentication" -Value 1
Restart-Service -Name "TermService" -Force

powercfg /hibernate off
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Power" -Name "HiberbootEnabled" -Value 0

Set-Service -Name "RemoteRegistry" -StartupType Disabled
Stop-Service -Name "RemoteRegistry"
Disable-ScheduledTask -TaskName "npcapwatchdog"

Set-Service -Name "WSearch" -StartupType Disabled
Stop-Service -Name "WSearch"

Stop-Service -Name "Spooler"
Set-Service -Name "Spooler" -StartupType Disabled

Set-Service -Name "SNMPTrap" -StartupType Manual
Stop-Service -Name "SNMPTrap"
cls
}


&{
Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" | Select-Object PortNumber;
Get-NetFirewallRule | Where-Object { $_.DisplayName -like "*Bureau*" -or $_.DisplayName -like "*Remote Desktop*" -or $_.DisplayName -like "*RDP*" } | Select-Object DisplayName, Enabled, Action | Format-Table -AutoSize;
Get-Service -Name "TermService*" | Select-Object Status, Name, DisplayName, StartType, BinaryPathName | Format-Table -AutoSize;
Get-Service -Name "RemoteRegistry*" | Select-Object Status, Name, DisplayName, StartType, BinaryPathName | Format-Table -AutoSize;
Get-Service -Name "WSearch*" | Select-Object Status, Name, DisplayName, StartType, BinaryPathName | Format-Table -AutoSize;
Get-Service -Name "Spooler*" | Select-Object Status, Name, DisplayName, StartType, BinaryPathName | Format-Table -AutoSize;
Get-Service -Name "SNMPTrap*" | Select-Object Status, Name, DisplayName, StartType, BinaryPathName | Format-Table -AutoSize
}

```
