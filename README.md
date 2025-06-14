# ActiveDirectoryProject
Hello! This is my active directory project!
Please see the outline below that I made using Draw.io ! 
![An Image of the outline of this project.](https://github.com/user-attachments/assets/ce55c8f0-4510-46db-885b-a411f983bdfe)


## Goals and Objectives
**NOTE: I will be using Vultr's cloud computing service to setup all of my machines in this project**

1. Setup a Windows active directory
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
  
## Step 1: Setup a Windows Active Directory
   


