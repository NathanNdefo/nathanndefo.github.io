---
title: "Part 1 - Creating a SOC home lab"
excerpt: "In this first part of my project, I deployed and configured two virtual machines: a Windows Server and a Linux Syslog Server. The Syslog server will be used to store logs from the FortiGate Next Generation Firewall (NGFW). The Windows Server will eventually be accessible via RDP, with the firewall’s Intrusion Prevention System (IPS) capability detecting and blocking brute force attacks. Logs from the firewall will be sent to the Syslog server, which will then forward them to a Log Analytics Workspace, where Microsoft Sentinel will use custom queries to generate alerts.<br/><img src='/images/project2/thumbnail.png' width=500>"
#collection: portfolio
---

After exploring setting up VMs, creating a log analytics workspace and creating alerts in Sentinel in intro to Microsoft Sentinel. I decided to try up my game a little by deploying and configuring a NGFW, along with a Windows Server accessible via RDP and a Linux Ubuntu VM which will act like a syslog server for the FortiGate logs.

In this part 1 of my project I will cover:

Resource creation: Creating two virtual machines (VM) - A Windows VM and a Linux VM.
Deploying and configuring a Next-Generation Firewall: I learn how to deploy a FortiGate Firewall, set up rules and implement a IPS for greater security.

## Creating the Virtual Network (VNet)

The first thing I did was create a virtual network (VNet). As my plan for this project was to have two or more virtual machines, I decided to create a new resource group with a name more fitting than in my introduction project. Using proper naming conventions helped in the future as the project grew in size. I created the resource group "Project_NGFW" and made sure to select the region closest to me (Europe UK South).

After creating the resource group, I jumped straight in and started creating the virtual network. I ensured that the correct resource group and region were selected and named my virtual network "Project1_Network". Again, the name I chose was one that I am certain I wouldn’t be confused about in the future.

<img src='/images/project2/picture1.png' width=500>"