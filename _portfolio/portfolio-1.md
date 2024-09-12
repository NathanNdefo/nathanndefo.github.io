---
title: "Introduction: Setting up Microsoft Sentinel and creating alerts"
excerpt: "Making use of Azure's free trial to spool up a VM, configure Microsoft Sentinel and create alerts!<br/><img src='/images/dashboard.png' width=500>"
#collection: portfolio
---
## Network Diagram


<img src='/images/Project1/NetworkDiagram.png'>


## Creating the VM (Virtual Machine)

Signing up to Microsoft Azure trial which gives you $200 credit to use towards hosting VMs and various other cloud services, I got started creating a VM.

<img src='/images/Project1/Picture1.png'>

As the purpose of this project is to set up Sentinel and receive alerts, I wanted to create a way for me to get attempted connections to this VM. By leaving port 3389 (RDP) open this should get some RDP attempts from someone malicious out there!

<img src='/images/Project1/Picture2.png'>

## Setting up the workspace

Once I configured my VM and waited for it to deploy, I decided to make a start on setting up a Microsoft Sentinel workspace. This was simple I just had to ensure that the resource group of the workspace matched the group that the VM was in. I also needed to give it a name which I boringly named "LogAnalytics".

<img src='/images/Project1/Picture3.png'>

By the time I had created the workspace my VM had successfully been deployed. To verify that port 3389 was open I navigated to the VM > Network Settings > Rules and as seen below in the screenshot you can see the port rule for RDP is open allowing both Source and Destination traffic.

<img src='/images/Project1/Picture4.png'>

## Sentinel, installing data connectors and creating rules!

Here was my first glimpse of the Sentinel GUI... looks very empty at the moment. That is because I had yet to pull the logs from the VM to the workspace, which will then forward to Sentinel.

<img src='/images/Project1/Picture5.png'>

Within the "Data Connectors" tab, I went ahead and installed Windows Security Events. This uses AMA (Azure Monitoring Agent) which allowed me to stream the Event logs from my VM into the Log Analytics workspace.

After the data connector was installed, I headed back over to the Data connectors tab. I opened up the settings to my newly installed connector “Windows Security Events via AMA” and began setting up a new data collection rule.

<img src='/images/Project1/Picture6.png'>

I allocated the resource group I wanted the data to be collected from, along with the VM I created at the very start of this project. As well as choosing to have all security events streamed to the workspace.

Once this was all setup I headed back over to Sentinel logs and tested to see if the Windows event logs were being pulled by running a simple query in the Logs section – “SecurityEvent”. As you can see from the screenshot Windows Security Event Logs from the VM I created were indeed being forwarded to Sentinel.

<img src='/images/Project1/Picture7.png'>

I now wanted to test creating a sentinel rule that sends an alert when there is a successful login to the VM I created. This rule looks for successful login attempts where the account being signed in is not a system account. Using the Analytics rule wizard, I set the MITRE ATT&CK technique as Initial Access as this would be the first foothold a potential malicious actor would have in the network via RDP.

This is the review of the rule created:

<img src='/images/Project1/Picture8.png'>

To test this alert, I downloaded the RDP file for the VM and signed in. What I expected to happen, is after 5 minutes I should receive an alert on Sentinel letting me know that there was a successful login.

After 5 minutes checking in on my Incidents tab, we can see I got my first incident!

<img src='/images/Project1/Picture9.png'>

## Creating an alert for a potential brute force attack

Now I wanted to experiment and see if I could simulate a brute force attack with more than 5 failed login attempts on a user account creating an alert.
As I used a free trial of Azure, I was unable to setup Azure Event Hubs, that allow real-time streaming of security data for immediate processing and monitoring. So instead, I had to make do with the 5 minute query search.

Below is the rule I created, this query checks for failed login attempts related to the account nathanndefo in the SecurityEvent table. It searches for events where the Activity contains "failed" or "login failure" and retrieves key details like the time generated, account name, activity type, computer, and IP address. The goal was to track login failures for a specific user in an easy-to-read format.

<img src='/images/Project1/Picture10.png'>

I then attempted to login more than 5 times with random incorrect passwords for the user "nathanndefo" via RDP.

<img src='/images/Project1/Picture11.png'>

After waiting 5 minutes for the query to be run, we can see I had an alert that came through notifying me of a possible brute force attack with the severity "High" that was set in the rule.

<img src='/images/Project1/Picture12.png'>

Running this query in the logs tab in Sentinel, we can see the multiple failed login attempts by me, along with the specific columns I wanted to be outputted using the "project" operator.

<img src='/images/Project1/Picture13.png'>

## What I learnt 📝

From this project, I learnt how to set up and configure Microsoft Azure virtual machines (VMs) and Microsoft Sentinel. My key takeaways were:

1. **Creating and Configuring VMs in Azure:** I learnt how to create a virtual machine in Microsoft Azure and configure its network settings, such as leaving port 3389 (RDP) open to capture potential login attempts.
2. **Data Collection and Monitoring:** I installed the Windows Security Events data connector using the Azure Monitoring Agent (AMA) to stream event logs from the VM to the Sentinel workspace. I also set up data collection rules to forward security events to Sentinel.
3. **Querying and Monitoring Logs:** By running queries like "SecurityEvent" in Sentinel, I learnt how to pull event logs from the VM, monitor them for specific security events and verify that the logs were successfully collected.
4. **Creating Custom Alerts in Sentinel:** I set up rules in Sentinel to generate alerts for specific activities, such as a successful login to the VM. I configured the alert based on the MITRE ATT&CK framework to detect potential initial access attempts through RDP.
5. **Simulating and Detecting Brute Force Attacks:** I experimented with creating a Sentinel rule to simulate a brute force attack with more than five failed login attempts. By running queries, I was able to detect multiple failed login attempts and receive alerts in Sentinel with customized severity settings.
