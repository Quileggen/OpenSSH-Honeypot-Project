### 2. Setting up OpenSSH

OpenSSH is a service that will function effectively with its default configuration settings, so there is little need to provide configuration; it can simply be started through PowerShell, using the command:
```powershell
Start-Service sshd
```
For the purpose of a honeypot, it should be running whenever the computer is. To enable it to start running when a machine is booted up, use the following command:
```powershell
Set-Service -Name sshd -StartupType Automatic
```

![A PowerShell terminal with the above commands run](/img/ps_start_openssh.png)  
<em>The command </em>`Get-Service -Name sshd`<em> command can show that the service started successfully</em><br>

Neither of these commands have any output when they are successful, so to confirm that they went into effect, check the Services application of Windows. This can be found either by searching for 'Services' in the Start Menu, or opening the Run window (hotkey Win+R) and opening 'services.msc' through it. Once the Services window has opened, find 'OpenSSH SSH Server' and make sure its status is 'Running' and its Startup Type is 'Automatic'.

![Windows Services page, with the OpenSSH Server selected. Its Status is 'Running' and Startup Type is 'Automatic'](/img/ssh_services.png)  
<em>Windows Services page, with the OpenSSH Server selected</em>><br/>

Additionally, the default shell accessible by users connecting via SSH is the Windows command prompt (cmd.exe), but PowerShell is more robust in its use as well as in the logs it produces, so it may be worthwhile to change the default shell to PowerShell. This can be done with the following command:
```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name "DefaultShell" -Value "C\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
```

![A PowerShell terminal with the above command run. Output shows the information on the registry key created by it](/img/ps_set_shell.png)  
<em>This command does have output to show that it worked</em><br/>
<br/>

Previous: [1. Installing OpenSSH](./1_installing_openssh.md)
Next: [3. Accessing the Honeypot via SSH](./3_accessing_honeypot_via_ssh.md)

[Back to Overview](./README.md)