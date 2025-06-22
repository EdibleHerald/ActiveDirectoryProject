# ActiveDirectoryProject

This drawing was made with draw.io !
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
Once I made my Vultr account, I setup 3 machines using the "Deploy" button under "Compute" in the dashboard:
![Screen of Computer dashboard, with red circle over "Deploy" button](https://github.com/user-attachments/assets/f8ea08bb-07f9-487c-8d56-5cb691791b90)

From there, I was under the "Deploy a server" tab. Now, I configured all three of my machines:
- Active Directory Domain Controller : Shared CPU, plan name: vhf-2c-4gb, OS: Windows Standard 2022 x64
- Test Machine: Shared CPU, plan name: vhf-1c-2gb, OS: Windows Standard 2022 x64
- Splunk Enterprise Machine: Shared CPU, plan name: vc2-4c-8gb, OS: Ubuntu 22.04 x64

Last choices to make: 
- I chose server location closest to me, if you are using this as a guide, please note that you **MUST choose the SAME server for EVERY machine** for VPC to work properly.
- I disabled backups since I didn't need them
- Add a hostname for each machine, I named them: myDomain-ADDC04, myDomain-Splunk, myDomain-TM02 respectively to easily tell them apart. 
> But, why not Windows Standard 2025 x64? --> As of writing, Windows Standard 2025 x64 on Vultr is relatively new and very buggy. I experienced constant crashes and eventually I had to give up on using it. I had no issues with the 2022 verison.

> Why Ubuntu 22.04 LTS and not 25.04 or 24.10? --> Ubuntu LTS (Long Time Support) are generally supported for longer after release. There is a Ubuntu 24.02 LTS but I chose 22.04 LTS, it shouldn't make any difference for this project. 

Now I opened up myDomain-ADDC04 and downloaded Active Directory and set it up. I also setup my Test machine's windows as well. I won't go too in depth here since I think its unnecessary. 
In short: 
- Open machine that will be domain controller, download active directory from server dashboard (click on Add roles and features) 
- Once the download is finished, restart the machine
- Once machine is back online, open server manager, go to flag icon that should have a yellow triangle warning sign, then click "Promote this server to domain controller" and make a new forest.
- Add the domain name and password
- Finish setup and restart machine again.
- Now, go to test machine, go to settings --> About --> Rename this PC --> Member of Domain
- Then put in domain name and attempt to join
- Then restart PC to complete the joining process
> If this fails, then its probably because you haven't properly setup VPC yet (or if you're doing this using locally hosted VMs like Oracle Virtualbox, you may have added the "Internal" network adapter in vm setting)
> Go down to "Issues that I ran into" for more details on how to fix this. 
  
One important thing to remember is to correctly configure the network adapter that handles the VPC connection to give the machines their respective assigned VPC IP Addresses and subnet masks given by Vultr. Also remember to make the DNS server of the test machine the VPC IP address of the Domain Controller. More on that right under here: 

### (WIN 2022 VPC) Issue I ran into: 
> It says "Destination Host Unreachable" when I try to ping my other machine using the VPC IP Address!
> This happens because while VPC was enabled in the other machines, it has not automatically been configured in the windows machines.
> ![Instructions for changing IP](https://github.com/user-attachments/assets/91e8c992-aec0-46b4-ada6-0fba761f77db)
> To fix:
> - Go to each machine's Vultr page, go to settings, then go down to VPC Network
> - Write down each machine's given VPC IP Address and Subnet mask
> - Go to each Windows machine, go to "View Network Connections" in the seach bar and click on the adapter with "2" at the end of the connection (The name above the "Redhat ethernet adapter #")
> - Right Click --> Properties --> Double click TCP/IPv4 under Networking tab
> - Replace the IP Address and Subnet mask with the one given by Vultr
> - **IMPORTANT**: For the DNS server, put 127.0.0.1 for the machine that's the Domain Controller. For the Test machine, put the DNS server as the VPC IP Address of the Domain Controller.  
>
> Now, the VPC is fully setup 

### Firewall setup
After that, I need to do a few things: 
I need to setup a firewall and apply it to my machines. Here is what my rules look like: 
![Vultr's Firewall Dashboard](https://github.com/user-attachments/assets/a73f80dd-07b4-493f-ae06-38d88c04dcc8)
I used ports:
 - 22(SSH) to connect to my Splunk server remotely
 - 389(LDAP) to allow Shuffler to connect to my active directory for changes
 - 3389(RDP) to allow my computer to remotely access my Test Machine which is what my custom alert will be tracking
 - 8000 is the port that Splunk Enterprise uses by default to allow web connections to my Splunk Server's dashboard
 - 9997 is the port that Splunk Enterprise uses for data forwarding from Splunk Universal Fowarders to the server.
I allowed each port to be opened but only if it came from certain IPs (E.g. since I want to remotely connect to my machines using RDP, I use my computer's IP address under "source" and it'll only allow that ip address to use that port)
After my firewall is configured, I need to apply the firewall to each machine under their respective firewall settings.

### VPC setup
This is very simple, just go to: 
Each machine's settings --> VPC Network --> Enable VPC
Do this for every machine and they'll be connected to a VPC and Vultr will provide them static ips to use. 

### Splunk Enterprise Setup
Open Ubuntu machine that will be the Splunk Enterprise server. I connected remotely using Windows power shell:
> ssh root@[ip Address here]
> Use IP address (not VPC IP address) given by Vultr
> It'll ask for a password, just copy and paste the one given by Vultr

First: download updates for Ubuntu
`apt-get update && apt-get upgrade -y`
After that finishes, it'll prompt you to use arrow keys and ENTER to choose some options. But you can just press Enter a few times until it goes away. 

Try pinging the Windows ADDC and Test machine using the VPC Ip address, it should respond!
> ping [Insert VPC IP address here]

Now, I need to download Splunk Enterprise on this Ubuntu machine, here's how to do it:
- Go to Splunk downloads (you need an account)
- Go to "Get your free trial" for Splunk Enterprise
- Go to Linux downloads, look for the .deb file, then press "copy .wget link" and copy the link
- Run this command, insert link where needed
`wget -O [Insert download link here]`

-Run:
`ls`
<br>
-Check if the Splunk Enterprise file is in the directory. Then run:
`dpkg -i [filename here]`
> For the filename, you can just type "splunk" then press tab to let it autocomplete the filename for you.
-Starting from root directory,run:
`cd /opt/splunk/bin`
-Then
`./splunk start`
- Agree to license and then create a admin username and password
- Lastly, run:
`ufw allow 8000` AND `ufw allow 9997`
> We're allowing Ubuntu firewall to accept and send from these ports which we need to receive data from our other machines and to be able to connect to the web dashboard.

Now, when I go to my web browser and try connecting to the ip address of my Ubuntu machine at port 8000, I connect to Splunk Enterprise dashboard!
The username and password I just created works!
![Image of Splunk Enterprise Admin Dashboard Login](https://github.com/user-attachments/assets/004f14a6-f08e-44a5-a781-75e9e8d09de0)

### Using Splunk Enterprise Dashboard to Create a Custom Alert
Now that I'm in the admin dashboard, I need to create a custom alert and configure it.

First step is to open the "Search and Reporting" app under the apps section in the admin dashboard.
![Splunk Enterprise Admin Dashboard, with a Red Arrow pointing at the "Search and Reporting" App under the apps button](https://github.com/user-attachments/assets/b8f2b868-84e3-4634-b6aa-3e0d367ee5e0)

Now, I go to "Search" and here I can search for any alert that I want. I can then make that search into a custom alert. 
Before I start, I have to explain how exactly this event search works in Splunk Enterprise. 

Splunk Enterprise Search and Reporting's search feature needs multiple details to pinpoint the alert we want. Notably, we need the index where the unfiltered alerts are. 

According to Splunk Enterprise documentation:
> "The index is the repository for Splunk Enterprise data. Splunk Enterprise transforms incoming data into events, which it stores in indexes."
This would make the Windows Active Directory an index which can be confirmed by checking the "Indexes" page under settings.
![Indexes pages](https://github.com/user-attachments/assets/d0114b71-91e2-4128-b4f7-4f386b54161c)

We need to select the event we're trying to make into an alert, we want to look for a sign on event code. 
Simplest way to find this is to sign into one of the Windows machine then using the "Search & Reporting" app to look for that event using "index=mydomain-ad". 
Another way is to google it which shows:
> Successful Login Event code 4624

Now we have the event code but there are different types of successful logins. Checking online (I used www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624), you can see the different logon types. 
The one that is most relevant is logon type 10 and 7, which we will use to help filter out alerts. 
> Why Logon type 10 and 7?
> Logon type 10 explicitly tracks remote connections through terminal services, RDP, or remote assistance
> Logon type 7 helps track a remote session by noting if a session is unlocking their screen from a screen saver

Now that we have the most important bits, here is the custom search that I used:
> index = "mydomain-ad" EventCode=4624 (Logon_Type=7 OR Logon_Type=10) Source_Network_Address=* | stats count by _time,ComputerName,Source_Network_Address,user,Logon_Type

I then press enter to search, then, above the search bar on the right side of the screen, I press "save as" then "alert".
For the alert configuration, I add it to the triggered alerts section and add a webhook. 

> How did you know how to format the specific details you needed?
> ![Screenshot of event details given](https://github.com/user-attachments/assets/93bf8fc2-667c-46c5-9943-7fe8f514fa1a)
> When you click on an event, it gives you details and importantly, variable names. All I did was take those variable names and added a stats count which would take the original alert, and then make a new one that uses only the information that I need.
> Notes:
> The "|" line just seperates commands.
> The "*" means any input
> "stats count by" means to save ONLY the following information
> 
Now, my custom alert is setup!

### Final Setups

#### Download Splunk Universal Fowarder
Self-explanatory, go to Splunk website and download splunk universal fowarder onto both the domain controller and test machines.

Now we can test the custom alert! Go to "Alerts" away from "Search" and enable the custom alert. When I login remotely into a user I made, it shows the following:
![Screenshot of my custom alert](https://github.com/user-attachments/assets/3fd80f82-2248-44c0-8689-8bf5b1c8a82d)
Which means that it works. 

### Setup Shuffler





