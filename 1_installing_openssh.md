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
<center><em>PowerShell commands for finding and installing OpenSSH</em></center>

#### Settings Method
1. Open Windows Settings and navigate to the Apps category
   ![Windows Settings home page](/img/settings_menu.png)
   <center><em>Windows Settings home page</em></center>
2. From Apps & features, click 'Optional features'
   ![Windows Settings Apps and features page](/img/settings_apps.png)
    <center><em>Windows Settings Apps page</em></center>
3. From here, click 'Add a feature'
   ![Windows Settings Optional features page](/img/settings_optional_features.png)
   <center><em>Apps -> Optional features</em></center>
4. Search for 'OpenSSH' and select 'OpenSSH Server' then click 'Install' to download and install the service
   ![Windows Settings Add optional feature page with OpenSSH Server selected](/img/settings_install_openssh.png)
   <center><em>Adding the OpenSSH Server optional feature</em></center>


Next: [2. Setting up OpenSSH](./2_setting_up_openssh.md)

[Back to Overview](./overview.md)