---
title: "Setting up Microsoft Sentinel and creating alerts"
excerpt: "In the project I will be making use of Azure's free trial to spool up a VM and configure Microsoft Sentinel<br/><img src='/images/dashboard.png' width=500>"
#collection: portfolio
---

## Creating the VM (Virtual Machine)

Once signing up to Microsoft Azure trial which gives you $200 credit to use towards hosting VMs and various other cloud services, I got started creating a VM.
<img src='/images/Project1/Picture1.png'>

As the purpose of this project is to set up Sentinel and receive alerts, I wanted to create a way for me to get attempted connections to this VM. By leaving port 3389 (RDP) open this should get some RDP attempts from someone malicious out there!

<img src='/images/Project1/Picture2.png'>

Once I configured my VM and waiting for it to deploy I decided to make a start on setting up a Microsoft Sentinel workspace. This was simple I just had to ensure that the resource group of the workspace matched the group that the VM was in. I also needed to give it a name which I boringly named "LogAnalytics".

<img src='/images/Project1/Picture3.png'>

By the time I had created the workspace my VM had successfully been deployed. To verify that port 3389 is open I navigated to the VM > Network Settings > Rules and as seen below in the screenshot you can see the port rule for RDP is open allowing both Source and Destination traffic.

<img src='/images/Project1/Picture4.png'>

Here was my first glimpse of the Sentinel GUI... looks very empty at the moment. That is because I have yet to pull the logs from the VM to the workspace which will then forward to Sentinel.
<img src='/images/Project1/Picture5.png'>

Within the "Data Connectors" tab in Sentinel I went ahead and installed Windows Security Events. This uses AMA (Azure Monitoring Agent) and will allow me to stream the Event logs from my VM into the Log Analytics workspace.

Once the data connector was installed, I headed back over to the Data connectors tab. I opened up the settings to my newly installed connector “Windows Security Events via AMA” and began setting up a new data collection rule.

<img src='/images/Project1/Picture6.png'>

I allocated the resource group I wanted the data to be collected from, along with the VM we created at the very start of this project. As well as choosing to have all security events streamed to our workspace.

Once this was all setup I headed back over to Sentinel and tested to see if the Windows event logs where being pulled into Sentinel by running a simple query in the Logs section – “SecurityEvent”. As you can see from the screenshot Windows Security Event Logs from the VM I created were indeed being forwarded to Sentinel.
<img src='/images/Project1/Picture7.png'>
