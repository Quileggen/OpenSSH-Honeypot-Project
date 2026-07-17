### 1. Installing OpenSSH

To set up OpenSSH, it first must be installed. This can be done in one of two ways: either with PowerShell or through the Settings GUI.
#### Powershell Method
Open PowerShell as an Administrator. Once this is done, confirm whether OpenSSH Server is already installed, using the command:
```powershell
Get-WindowsCapability -Online | Where-Object name -like 'OpenSSH'
```
This command searches the repository of packages that can be downloaded to Windows for any packages with names that start with 'OpenSSH'. The Client may already be installed, but the Server will not be by default. Assuming the Server is not installed, download it with the command:
```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```
The output should look similar to this: 

![A PowerShell Window with commands to find and install OpenSSH](/img/ps_install_openssh.png)  
<em>PowerShell commands for finding and installing OpenSSH</em><br/>

#### Settings Method
1. Open Windows Settings and navigate to the Apps category
   ![Windows Settings home page](/img/settings_menu.png)  
   <em>Windows Settings home page</em>
2. From Apps & features, click 'Optional features'
   ![Windows Settings Apps and features page](/img/settings_apps.png)  
    <em>Windows Settings Apps page</em>
3. From here, click 'Add a feature'
   ![Windows Settings Optional features page](/img/settings_optional_features.png)  
   <em>Apps -> Optional features</em>
4. Search for 'OpenSSH' and select 'OpenSSH Server' then click 'Install' to download and install the service
   ![Windows Settings Add optional feature page with OpenSSH Server selected](/img/settings_install_openssh.png)  
    <em>Adding the OpenSSH Server optional feature</em><br/>
<br/>

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

### 3. Accessing the Honeypot via SSH

Now that OpenSSH is set up, make sure everything is working as expected by using another device to connect to the SSH Server. This is most conveniently tested on a local network, as it will be in this example. It can be done over the internet with the proper infrastructure, but that case is outside the scope of these instructions. 

First, determine the IP address of the honeypot. This can be done through a terminal (either the command prompt (cmd.exe) or PowerShell) with the `ipconfig` command. 

![A PowerShell terminal after running `ipconfig` displaying several IP addresses](/img/ps_ipconfig.png)  
<em>Output of the ipconfig command. The relevant IP for this machine is 10.0.2.3</em><br/>

Next, open a terminal on another device that has SSH installed (many operating systems include this by default), and use that to connect to the honeypot. SSH follows the format:
```
ssh [Username]@[IP_Address]
```
To log on to the default Administrator account on a device with an IP Address of 10.0.2.3, the command would be:
```
ssh Administrator@10.0.2.3
```

If this is the first connection from that client to the host, it may provide a prompt asking whether to continue with the connection, then it will prompt for the password of the desired account. After these prompts are answered, the SSH connection is established and commands can be entered.

![A terminal shell in Kali Linux showing the prompts when using SSH](/img/kali_ssh_connect.png)  
<em>An example of connecting to a host via SSH the first time</em><br/>

![A terminal shell in Kali Linux after successfully connecting to the honeypot via SSH](/img/kali_ssh_success.png)  
<em>An established SSH connection</em><br/>
<br/>

### 4. Viewing Logs in Event Viewer

Key to the purpose of this honeypot is the ability to view incoming connections so they can then be blocked. The OpenSSH Server, by default, writes its logs to the Event Viewer. These logs can be accessed by opening the Event Viewer, either by searching in the Start Menu or opening the Run window (hotkey Win+R) and entering 'eventvwer.msc'. Once it is open, use the left-hand panel to navigate to the path 'Applications and Services Logs -> OpenSSH' which has the Admin and Operational categories. Connections fall under the latter.

![Event Viewer, open to the Operational logs of OpenSSH](/img/eventvwr_openssh_operational.png)  
<em>Event Viewer, open to the OpenSSH/Operational logs</em><br/>

This list is where the IP addresses attempting to connect can be located, as well as whether the password provided by the client was accepted or not. The logs here can be looked through by hand to find incoming connections, but it will likely be more time-efficient to cross-reference these logs with the system's Security logs to find logon attempts. This can be done by creating a custom view, either through the context (right-click) menu of the navigational panel, or under the Action dropdown in the menu bar at the top; in either case, select 'Create Custom View' and a window to do so will appear. Switch to the XML tab and ensure the 'Edit query manually' box is checked, then you can create a query for the relevant data. For example:
```xml
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data and (Data='C:\Windows\System32\OpenSSH\sshd.exe')]]
      and *[System[(EventID='4624' or EventID='4625')]]
      and *[EventData[Data[@Name='LogonType'] and (Data='8' or Data='10')]]</Select>
		<Select Path="OpenSSH/Operational">*</Select>
	</Query>
</QueryList>
```
This displays all events from the Security log that are generated by OpenSSH, result in an EventID of 4624 or 4625 (successful and unsuccessful logons, respectively), and use LogonType 8 or 10, which both correspond to attempted authentication over a network. It also displays all Operational events from OpenSSH, as they are not formatted in a way that allows Event Viewer to pick out logs for specific actions.

![The custom view editor in Event Viewer, with the above XML query input](/img/eventvwr_make_view.png)  
<em>Creating a custom view to better sort through logs</em><br/>

When this view is active, it is easy to see Security logs that correspond to logon attempts, and these can be matched by their timestamp with OpenSSH logs that record incoming connections, allowing for an easy way to find logs that include important data such as the IP address of a potential attacker.

![A custom view in Event Viewer, collating Security logs and OpenSSH ones](/img/eventvwr_custom_view.png)  
<em>A custom view to make finding attacker IP addresses convenient</em><br/>

Lists of logs, either in a custom view or a default one, can be exported to a file for external analysis. This can be done through the context menu of a listing in the navigational panel or the Action dropdown in the menu bar, under 'Save All Events As...'

![The context menu of the OpenSSH/Operational entry in the navigation panel. 'Save All Events As...' is highlighted](/img/eventvwr_save_events.png)  
<em>The option to export events in a list to a file</em><br/>

Different filetypes can be selected based on the requirements of destination software, including the .evtx, .txt, .xml, and .csv. Exporting in .evtx (Event Viewer's native file type) prompts for exporting display information as well, which may be necessary when transferring the log files between computers. See the [logs folder](./logs) of this repository for example exports.<br/>
<br/>

### 5. Using Windows Firewall to Block IP Addresses

Once the IP address of a potential attacker is discovered through the OpenSSH logs, a new rule can be created in Windows Firewall to prevent future connections from that address. First, open Windows Defender Firewall with Advanced Security. This is most easily done via the Run window (hotkey Win+R) by entering 'wf.msc'.

Once open, select 'Inbound Rules' from the left-hand panel, and then click 'New Rule...' either directly in the right-hand panel, or under the Action dropdown in the menu bar.

![Windows Defender Firewall with Advanced Security, open to the list of inbound rules](/img/firewall_inbound.png)  
<em>Windows Defender Firewall, ready to create a new rule for inbound traffic</em><br/>

This will open the Inbound Rule Wizard

1. To block SSH connections from a specific IP address, make a Port rule, then click 'Next'
   ![The first page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_type.png)  
      <em>The type of rule to be implemented</em>
2. Select TCP, and the specific local port 22, then click 'Next'
   ![The second page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_port.png)  
      <em>Which protocol and ports the rule covers</em>
3. Select 'Block the connection', then click 'Next'
   ![The third page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_action.png)  
      <em>The desired action for the rule</em>
4. Make sure the the boxes for Domain, Private, and Public profiles are all checked, then click 'Next'
   ![The fourth page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_profiles.png)  
      <em>Which types of network the rule should cover</em>
5. Give the rule a descriptive name (e.g. "Block SSH from Specific IP") then click 'Finish'
   ![The fifth page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_name.png)  
      <em>The name and optionally a description for the rule</em><br/>

Once the rule is created, it will still have to be configured to apply to the IP addresses found in the logs. Find the new rule in the list, right-click to bring up the context menu, and select 'Properties'.  
![The context menu of the newly created "Block SSH from Specific IP" rule, with 'Properties' highlighted](/img/firewall_open_properties.png)  
<em>This can also be done from the bottom section of the right-hand panel while the rule is selected</em><br/>

In the Properties window, open the 'Scope' tab. In the the 'Remote IP address' section, select 'These IP addresses:' and click 'Add...' to enter the relevant address  
![The Add IPs window of the rule's Scope Properties](/img/firewall_scope_entry.png)  
<em>This window even allows for blocking whole subnets</em><br/>

Once finished, click 'Apply' or 'OK' to finish the process.  
![The Scope Properties of the rule, with an IP address listed under the Remote IPs section](/img/firewall_scope_finished.png)  
<em>The IP address will now be blocked from connecting again</em><br/>

Confirm that the rule is working by attempting to connect over SSH from the same machine as earlier. If everything is properly configured, the connection will eventually time out.  
![A terminal shell in Kali Linux, showing an SSH connection that has timed out](/img/kali_ssh_failed.png)  
<em>A failed SSH connection due to being blocked</em><br/>
<br/>

[Back to Overview](./README.md)