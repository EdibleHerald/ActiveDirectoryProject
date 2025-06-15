# ActiveDirectoryProject
Hello! This is my active directory project!
Please see the outline below that I made using Draw.io ! 

![An Image of the outline of this project.](https://github.com/user-attachments/assets/bd8e4c21-1433-48b6-a64d-bb5c85165999)



## Goals and Objectives
**NOTE: I will be using Vultr's cloud computing service to setup all of my machines in this project**

1. Setup a Windows Active Directory (AD)
   - I will be using Windows Server 2022 (I had a lot of issues with Windows Server 2025 and had to switch to 2022)
   - This means I need to setup a test machine that acts as a regular user within the domain.
     - I also have to add this machine to my domain first which means setting up static ip addresses and network configurations for both machines.
  
2. Setup Splunk Enterprise within the local network using a Ubuntu machine
   - Requires downloading Splunk Universal Fowarder on both my Domain Controller and Test machines in order for them to properly connect.
   - Requires setting up a CLI Ubuntu machine first.
  
3. Setup a custom alert in Splunk that will trigger an action through Shuffler.io
   - The custom alert will focus on unauthorized successful remote logins (More on that below)
   - Shuffler.io is an online workflow website which focuses on helping to automate responses to potential threats or any SIEM alert.
     - Shuffler provides webhooks which I will be attaching to my Splunk custom alert for Shuffler to receive the needed information.
    
4. Shuffler will prompt an authorized user through email about the alert
   - I aim to allow this user to disable the suspected account through email notification
  
5. Send a message to a Slack channel containing alerts
   - Slack is like discord but for a more professional setting, fortunately they have an application within Shuffler.io that allows me to automate messages
   - Will notify people of the suspected behavior and will indicate if an account has been disabled
  
## Tools Used

### Vultr
For this project, I'm using Vultr to setup the cloud server. They'll what I'll be using to setup every Virtual Machine (VM), Firewall, and Virtual Private Cloud (VPC). 
Here's what their main page looks like: 
![Main page when you go on Vultr site](https://github.com/user-attachments/assets/bc5253ea-57fb-4552-983e-98deedc2041f)

I relied on their referral program to get free $300 in credits which was far more than enough to complete this project. 
Here is a referral if you're interested in using Vultr: https://www.vultr.com/?ref=9771417-9J

### Splunk Enterprise
I used Splunk Enterprise's SIEM tools to set custom alerts and send data to my webhook in this project. 
I used Splunk Enterprise on a Ubuntu machine as the main instance and downloaded Splunk Universal Fowarder on each Windows machine to allow the Splunk Enterprise instance to recieve data.
![Splunk Download Page, where Splunk Enterprise and Splunk Universal Fowarder downloads are available](https://github.com/user-attachments/assets/1566e674-835f-4c00-abbf-1d5c979baf02)

### Slack
Slack is a messaging app typically used for professional communications. Think of it like Discord but for businesses. I'll be using this to notify users about my custom alert and action taken. 
![Slack Logo](https://github.com/user-attachments/assets/0107b09b-072b-4cd1-ae12-5cd00e2e04f5)

### Shuffler.io
Shuffler is an online workflow automation tool that I'll be using. Shuffler provides web applications such as an Active Directory app (to make AD changes), a Slack app (to send automated messages), and a webhook app (for data to be received from my custom Splunk alert)
![Shuffler.io Logo](https://github.com/user-attachments/assets/8fe7c133-5bdf-4f40-ad97-3cbc9c2c7e3e)

### Draw.io
This is the website I used to make the diagram that you first saw!

## Step 1: Setup a Windows Active Directory





