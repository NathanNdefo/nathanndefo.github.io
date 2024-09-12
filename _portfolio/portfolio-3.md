---
title: "Part 2 - Creating a SOC home lab"
excerpt: "In second part of my project, I deploy and configure two virtual machines: a Windows Server and a Linux Syslog Server. The Syslog server will be used to store logs from the FortiGate Next Generation Firewall (NGFW). The Windows Server will eventually be accessible via RDP, with the firewall’s Intrusion Prevention System (IPS) capability detecting and blocking brute force attacks.<br/><img src='/images/project2/thumbnail.png' width=500>"
#collection: portfolio
---

Continuing on from Part 1 where I have setup my Virtual Network (VNet), subnets and have done the basic firewall configurations. 

## Creating the Virtual Machines

Starting with the setup of the Windows virtual machine, the process was largely the same as in the Introduction project. **However, there was one crucial difference: previously, I had enabled public inbound port 3389 (RDP) to attempt RDP via the public network. This time, I disabled this option to ensure that all traffic was routed through the FortiGate firewall I had created.** Essentially, this approach followed proper security practices by having the firewall intercept all attempted connections to the machines.

<img src='/images/Project2/picture10.png' width=500>

Also, I ensured that the VM was assigned to the correct subnet and virtual network in the network interface settings before creating the VM. I then went ahead and created the Linux VM following similar steps to the Windows VM, making sure that it was in the correct subnet and disabled public inbound ports.

## Ensuring Proper Network Routing and Connectivity

To ensure that the virtual machines were routing through the FortiGate firewall and out to the internet, I wanted to remotely access the machines and test pinging an IP outside the network to check if it would appear in the FortiGate logs. However, as I had not opened any public ports (RDP via 3389), I needed to use the Azure Bastion service. I first tested by pinging the Linux VM from the Windows machine, which was successful.

Succesful ping to Linux VM:
<img src='/images/Project2/Picture11.png' width=500>
<img src='/images/Project2/Picture12.png' width=500>

As the FortiGate firewall policies were not configured in Part 1 of this project, the implicit deny default policies prevented me from pinging IP addresses outside of the network. To address this, I created a firewall policy that allowed traffic from within the network to use both PING and DNS services. After implementing these changes, I was able to ping external IP addresses. Since I had configured the VMs with no public IP addresses, I also needed to enable NAT in the firewall policy to allow the firewall to create a public IP address for the VMs.

<img src='/images/Project2/Picture13.png' width=500>

After making these changes I was now able to ping IP addresses from outside the private network. Below is a screenshot pinging Google.

<img src='/images/Project2/Picture14.png' width=500>

I also created a “Virtual IP” and firewall policy to enable RDP access to the Windows machine via requests from the public internet through the firewall, rather than using the Azure Bastion Service. 

Below, you can see a screenshot of the FortiGate logs showing the RDP sessions to the Windows VM from my personal PC.

<img src='/images/Project2/Picture15.png' width=500>