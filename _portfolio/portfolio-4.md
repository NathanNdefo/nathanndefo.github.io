---
title: "Part 3 - Creating a SOC home lab"
excerpt: "To conclude my project, I will introduce the IPS capabilities of the FortiGate NGFW to block brute force attacks via RDP. The logs will be sent to a Linux syslog server and with the aid of data connectors and data collection rules (DCR), will be forwarded to a Log Analytics workspace. additionally, custom KQL queries will be created to generate alerts for these brute force attempts in Microsoft Sentinel.<br/><img src='/images/Project2/Picture26.png' width=500>"
#collection: portfolio
---

Now I had virtual network, subnets and the firewall up and running. I was time  to try and implement the IPS features the FortiGate NGFW has as well as forwarding these logs to the Linux VM which would be forwarded to the log analytics workspace.

## IPS Signature Configuration

Within the FortiGate browser interface, I navigated to Security Policies > Intrusion Prevention and created a new IPS Sensor. Here I selected signatures to look for RDP Brute Force attempts on server.

<img src='/images/Project2/Picture15.png' width=500>

The preset signatures available from FortiGate didn’t seem to be alerting to any brute force attempts and at this point by viewing the FortiGate Forward Traffic Logs there were many! I decided to try a custom IPS signature I found online and set the action to Block.

Query I used:

F-SBID( --attack_id 7170; --name "MS.RDP.Connection.Brute.Force."; --protocol TCP; --dst_port 3389; --flow from_client; --seq 1, relative; --pattern "|e0|"; --distance 5,packet; --within 1,packet; --rate 5,20; --track SRC_IP ; )


After implementing this custom IPS signature the firewall started blocking attempts:  

<img src='/images/Project2/Picture17.png' width=500>

A quick look on VirusTotal shows that these IP addresses are indeed bad news!

<img src='/images/Project2/Picture18.png' width=500>


## Setting up Log Analytics workspace, Data Connectors, DCRs & More!

To collect logs from the VMs and forward them to Microsoft Sentinel, I needed to set up a Log Analytics workspace and install an agent on the Linux VM to link it to the workspace and configure data collection rules. I also had to adjust some settings in the FortiGate CLI to enable log forwarding to the Linux VM. I won’t include screenshots here as there were quite a few, and it’s not the focus of this post!

<img src='/images/Project2/Picture20.png' width=500>

Once everything was set up, I proceeded to configure Microsoft Sentinel for the new Log Analytics workspace I had created. I previously detailed the setup of Sentinel in the project “Introduction: Setting Up Microsoft Sentinel and Creating Alerts,” so feel free to refer to that post for those steps. However, in this instance, I needed to install a different data connector to receive the FortiGate syslog files in Common Event Format (CEF). This was the “Common Event Format via AMA” data connector, which was responsible for forwarding the syslog logs to Sentinel. On my Linux VM, I ran the Sentinel AMA troubleshooter to ensure everything was set up correctly.

<img src='/images/Project2/Picture21.png' width=500>

Following the documentation I also open Log analytics and checked if logs were received by “CommonSecurityLog” schema which it was. (See below)

<img src='/images/Project2/Picture22.png' width=500>


## Final Step: Creating Alerts

To create an alert for a brute force attempt, I triggered the IPS sensor myself by repeatedly failing the password. This allowed me to filter and identify the relevant log from the FortiGate firewall and create an alert based on it.

<img src='/images/Project2/Picture23.png' width=500>

The query I created examines the syslog files sent from the FortiGate firewall to my Linux VM, then forwarded to Sentinel via the data connector. It searches for logs containing the IPS signature for activity and messages related to brute force attempts.

<img src='/images/Project2/Picture24.png' width=500>


After confirming that the query produced the desired results, I created an alert based on this query. I then tested the alert by simulating a brute force attempt and it worked as expected—I received an incident notification. When creating the alert, I also mapped the entity IP to the IP address and source IP. As shown in previous screenshots, the alert correctly associated with my IP address.

<img src='/images/Project2/Picture26.png' width=500>


That concludes my project - Creating a SOC home lab. From this I now have a solid foundation where I can introduce new VMs and attempt different types of attacks and create alerts based on them.


## What I learnt


- **Handling Log Forwarding and Analysis:** I learnt how to forward FortiGate logs to Microsoft Sentinel through a Log Analytics Workspace by installing an agent, using data connectors and DCR.
- **Deploying Next-Generation Firewalls:** I gained practical experience deploying and configuring FortiGate firewalls in Azure, understanding their integration into the overall security strategy.
- **Customising Network Policies:** The project taught me how to create and fine-tune firewall policies to control traffic and handle network security challenges, including implementing NAT and custom IPS signatures.
- **Utilising Azure Bastion:** I discovered the benefits of using Azure Bastion to securely access virtual machines without needing public IP addresses.