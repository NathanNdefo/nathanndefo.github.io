---
title: "Part 1 - Creating a SOC home lab"
excerpt: "In this first part of my project, I create the virtual network and subnets for my project which will be the foundation to build on top of. I also deploy and configure a FortiGate Next-Generation Firewall (NGFW). <br/><img src='/images/Project2/thumbnail.png' width=500>"
#collection: portfolio
---
<br/>  

<br/>

<img src='/images/Project2/NetworkDiagram.png' width=500>"



After exploring setting up VMs, creating a log analytics workspace and creating alerts in Sentinel in intro to Microsoft Sentinel. I decided to try up my game a little by deploying and configuring a NGFW, along with a Windows Server accessible via RDP and a Linux Ubuntu VM which will act like a syslog server for the FortiGate logs.

In this part 1 of my project I will cover:

Resource creation: Creating two virtual machines (VM) - A Windows VM and a Linux VM.
Deploying and configuring a Next-Generation Firewall: I learn how to deploy a FortiGate Firewall, set up rules and implement a IPS for greater security.

## Creating the Virtual Network (VNet)

The first thing I did was create a virtual network (VNet). As my plan for this project was to have two or more virtual machines, I decided to create a new resource group with a name more fitting than in my introduction project. Using proper naming conventions helped in the future as the project grew in size. I created the resource group "Project_NGFW" and made sure to select the region closest to me (Europe UK South).

After creating the resource group, I jumped straight in and started creating the virtual network. I ensured that the correct resource group and region were selected and named my virtual network "Project1_Network". Again, the name I chose was one that I am certain I wouldn’t be confused about in the future.

<img src='/images/Project2/Picture1.png' width=500>"

I also decided to use Azure Bastion for this project. This allowed me to RDP to the virtual machines over TLS without the need for public IP addresses. I was a bit nervous about this, as it is a paid service and I did not want to end up with a surprising bill at the end of the month. However, with my $200 credit and research on Microsoft Forums, I believed that I shouldn’t incur any significant costs!

I did, however, configure cost alerts just in case!



Creating the general IP address range along with the subnets! This took a bit of time to wrap my head around but having the network diagram planned before starting this project did help of course.

For the network itself it is setup on 10.10.0.0/16:  

<img src='/images/Project2/Picture3.png' width=500>"

Below is a screenshot of the subnets I created as per my network diagram. These include a DMZ, WAN and Bastion subnet:

<img src='/images/Project2/Picture4.png' width=500>"

After about a 5-minute wait for the deployments it was successful:
<img src='/images/Project2/Picture5.png' width=500>"

## Deploying and configuring the FortiGate NGFW

For my firewall solution, I went to the Azure Marketplace and created my FortiGate firewall. There were several options for FortiGate firewalls when searching the marketplace. I selected Microsoft’s preferred solution; after doing a bit of research, I found out that this solution required less configuration, which was perfect for me as someone who had not configured a FortiGate firewall before!

The setup for the FortiGate firewall was the same as adding any other entity in Azure: selecting the resource group, region, and naming the firewall (which I named Project1-FW), etc. Until I reached the "Networking" section, it was important to ensure that I assigned the correct subnets to the external and internal options in the FortiGate setup. For external, I selected "Project1_WAN," as this was my outside-facing subnet according to my network diagram. The internal subnet was "Project1_DMZ."

<img src='/images/Project2/Picture6.png' width=500>"

After going through the rest of the deployment setup, the deployment was successful!

<img src='/images/Project2/Picture7.png' width=500>"

I was now able to access the FortiGate firewall through the browser interface and sign into the Firewall with the credentials I created during the setup.

<img src='/images/Project2/Picture8.png' width=500>"

Navigating to Network > Interfaces, I changed Port1 and Port2 to their respective names, Project1_DMZ and Project1_WAN. **I was aware that it is not best practice in cyber security to expose the management of the firewall to the internet.** However, as this was a project for learning, I did not set up a management network, etc. Therefore, I kept HTTPS enabled on the internal interface (Port1).

<img src='/images/Project2/Picture9.png' width=500>"
