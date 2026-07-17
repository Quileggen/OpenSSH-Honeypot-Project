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

Previous: [2. Setting up OpenSSH](./2_setting_up_openssh.md)
Next: [4. Viewing Logs in Event Viewer](./4_viewing_logs_event_viewer.md)

[Back to Overview](./README.md)