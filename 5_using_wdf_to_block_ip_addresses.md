### 5. Using Windows Firewall to Block IP Addresses

Once the IP address of a potential attacker is discovered through the OpenSSH logs, a new rule can be created in Windows Firewall to prevent future connections from that address. First, open Windows Defender Firewall with Advanced Security. This is most easily done via the Run window (hotkey Win+R) by entering 'wf.msc'.

Once open, select 'Inbound Rules' from the left-hand panel, and then click 'New Rule...' either directly in the right-hand panel, or under the Action dropdown in the menu bar.

![Windows Defender Firewall with Advanced Security, open to the list of inbound rules](/img/firewall_inbound.png)
<center><em>Windows Defender Firewall, ready to create a new rule for inbound traffic</em></center>

This will open the Inbound Rule Wizard

1. To block SSH connections from a specific IP address, make a Port rule, then click 'Next'
   ![The first page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_type.png)
      <center><em>The type of rule to be implemented</em></center>
2. Select TCP, and the specific local port 22, then click 'Next'
   ![The second page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_port.png)
      <center><em>Which protocol and ports the rule covers</em></center>
3. Select 'Block the connection', then click 'Next'
   ![The third page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_action.png)
      <center><em>The desired action for the rule</em></center>
4. Make sure the the boxes for Domain, Private, and Public profiles are all checked, then click 'Next'
   ![The fourth page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_profiles.png)
      <center><em>Which types of network the rule should cover</em></center>
5. Give the rule a descriptive name (e.g. "Block SSH from Specific IP") then click 'Finish'
   ![The fifth page of the New Rule Wizard of Windows Defender Firewall](/img/firewall_rule_name.png)
      <center><em>The name and optionally a description for the rule</em></center>

Once the rule is created, it will still have to be configured to apply to the IP addresses found in the logs. Find the new rule in the list, right-click to bring up the context menu, and select 'Properties'.
![The context menu of the newly created "Block SSH from Specific IP" rule, with 'Properties' highlighted](/img/firewall_open_properties.png)
<center><em>This can also be done from the bottom section of the right-hand panel while the rule is selected</em></center>

In the Properties window, open the 'Scope' tab. In the the 'Remote IP address' section, select 'These IP addresses:' and click 'Add...' to enter the relevant address
![The Add IPs window of the rule's Scope Properties](/img/firewall_scope_entry.png)
<center><em>This window even allows for blocking whole subnets</em></center>

Once finished, click 'Apply' or 'OK' to finish the process.
![The Scope Properties of the rule, with an IP address listed under the Remote IPs section](/img/firewall_scope_finished.png)
   <center><em>The IP address will now be blocked from connecting again</em></center>

Confirm that the rule is working by attempting to connect over SSH from the same machine as earlier. If everything is properly configured, the connection will eventually time out.
![A terminal shell in Kali Linux, showing an SSH connection that has timed out](/img/kali_ssh_failed.png)
   <center><em>A failed SSH connection due to being blocked</em></center>

Previous: [4. Viewing Logs in Event Viewer](./4_viewing_logs_event_viewer.md)

[Back to Overview](./overview.md)