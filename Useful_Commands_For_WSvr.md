## Reset all network settings
1. Open the Command Prompt (Admin) or PowerShell (Admin).
2. Execute netsh winsock reset
3. Execute netsh int ip reset
4. Execute ipconfig /renew
5. Execute ipconfig /flushdns
6. Execute netcfg –d
7. Restart the computer.

## Disable firewall for Hyper-V Server
### Method 1 @ cmd
```cmd
netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes
netsh advfirewall firewall set rule group="Remote Event Log Management" new enable=yes
```
### Method 2 @ powershell
```powershell
Set-NetFirewallRule -DisplayGroup 'Windows Management Instrumentation (WMI)' -Enabled true -PassThru
Set-NetFirewallRule -DisplayGroup 'Remote Event Log Management' -Enabled true -PassThru
```

### To access `Disk Management` Remotely
powershell on both HyperVserver and AD DS server:
```cmd
netsh advfirewall firewall set rule group="Remote Volume Management" new enable=yes
```

## Reference
1. [Configure network connections with the Command Prompt / PowerShell](http://www.apeswithcomputers.com/article/configure-network-connections-with-the-command-prompt-powershell)
2. [[Windows Hyper-V-Server]Enable or disable firewall rules under powershell / powershell下启用禁用防火墙规则](https://www.cnblogs.com/alfredsun/p/10681646.html)
