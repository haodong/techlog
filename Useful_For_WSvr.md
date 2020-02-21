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

## Solve `0xc0000001` error on WDS
- Error Code: `0xc0000001`
- Error Message: ***A required device isn't connected or can't be accessed.***
### Method 1
- Disable TFTP Variable Window Extension on WDS Server properties
- Uncheck "Enable Variable Windows Extension" in the TFTP tab on WDS Server could solve it.
### Method 2
1. Open WDS
2. Right-click your server in the left pane and open properties.
3. Open tab “TFTP” and change the maximum block size to eg 1024.
4. Restart your WDS server.

## Reference
1. [Configure network connections with the Command Prompt / PowerShell](http://www.apeswithcomputers.com/article/configure-network-connections-with-the-command-prompt-powershell)
2. [[Windows Hyper-V-Server]Enable or disable firewall rules under powershell / powershell下启用禁用防火墙规则](https://www.cnblogs.com/alfredsun/p/10681646.html)
3. [WDS PXE suddenly stopped working.Error 0xc0000001](https://social.technet.microsoft.com/Forums/en-US/387e5279-11f6-41e5-8b6b-23310343e73c)
