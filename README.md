# Active-Directory-Setup
Setup of a home lab for Windows Server 2019 and Windows 10 to simulate a small business environment. This lab assumes you have a little bit of prior knowledge working with VMware or Virtual Box along with some basic command-line tools such as ping. Most of this lab follow's InfoSec Pat's YouTube series on setting up and configuring your own home lab server. See here: https://www.youtube.com/playlist?list=PLxTwjzMO9Zf4FZJ0BTtQlv5iErouqkbkk

<h1>Initial Installation</h1>
Once Windows Server 2019 is setup and you're logged in, change the date/time to your timezone. It's now time to change the name of the computer to show that it's a Domain Controller. I named mine "WIN2019-DC01". 

<img src="https://user-images.githubusercontent.com/107446796/184459223-b83faac1-d34c-403d-bd3e-26a60cad433b.png">

<h1>Active Directory Domain Services</h1>
It's time to configure IPv4 and other network settings. Inside of Server Manager and Local Server, click on the Ethernet0 link to open up your network connections. Double click on the interface and select properties. Navigate to IPv4 properties and set a static IP address for your Domain Controller, as well as your default gateway. For now, I'll be utilizing DNS settings from my default gateway.

<img src="https://user-images.githubusercontent.com/107446796/184459701-03bea839-516e-4c81-a621-d6a47925b254.png">

Now navigate back to your Dashboard and and click on Mange in the top right, Add roles and features, and select Active Directory Domain Services. 

<img src="https://user-images.githubusercontent.com/107446796/184459775-fef15444-87b8-49af-97c8-966686c85588.png">

We are going to do a role-based installation.

<img src="https://user-images.githubusercontent.com/107446796/184459810-36a8a50f-9104-4200-bddd-a3033ed495a9.png">

Click next and verify that the static IPv4 address you assigned is there. If not, exit out of the installer, refresh the server in the top right corner, and come back to this step. If you're still having issues, verify that you configured the static address correctly by running "ipconfig /all" in the command prompt.

<img src="https://user-images.githubusercontent.com/107446796/184459907-5cf1ca57-c0ab-4463-b4e5-3e6940893f61.png">

Under Server Roles, select Active Directory Domain Services as the service to install, click "Add Features" on the pop-up, and click next.

<img src="https://user-images.githubusercontent.com/107446796/184459951-beee0fd3-1606-4930-96a2-dd30739f7261.png">

Click next through the next few sections until you hit the Installation page. Click Install and wait for the installation to finish. 

<img src="https://user-images.githubusercontent.com/107446796/184460003-2f9660aa-400d-4b46-b21c-20ffcbe58c5a.png">

Once this is finished, you'll see a link to promote to a Domain Controller. Click it. 

<img src="https://user-images.githubusercontent.com/107446796/184460232-b32d4533-fa7a-45b9-9765-e7a1d2657aaa.png">

Now since we don't already have any existing Domain Controllers, we're going to add a new forest and name your domain whatever you'd like. 

<img src="https://user-images.githubusercontent.com/107446796/184460280-c16374bf-c606-4a4f-b1ed-9f58022ce48c.png">

After clicking next, you'll have to set a Domain Services Restore Mode password. DSRM is like safe boot for a Domain Controller and will allow you to restore or repair an Active Directory database.

<img src="https://user-images.githubusercontent.com/107446796/184460495-87a5b94b-7255-4785-b5aa-c81179fc907c.png">

Click next through the DNS options and ignore the warning banner. Under additional options, wait for the NetBIOS name to populate and click next. Click next on paths and you should get to the review options. This has a PowerShell script that you can copy and save elsewhere in order to automate additional or future installations.

<img src="https://user-images.githubusercontent.com/107446796/184460743-1d834d76-cb62-4945-b6e5-843d904c7b49.png">

Your prerequisite check should be completed and good to go, now you can click install and wait for the reboot to finish.

<img src="https://user-images.githubusercontent.com/107446796/184460790-5f9e9b90-8aa3-45ff-b8a6-ac4e43fffdc2.png">

<h1>Active Directory Users, Computers and Organizational Units</h1>
Now we'll get into playing around within Active Directory.</br></br>

Once your server is booted back up and you're logged back in, go to Tools and select Active Directory Users and Computers. Just like in traditional desktop experiences of Windows, this is like a file tree. 

<img src="https://user-images.githubusercontent.com/107446796/184461255-a0e25c5e-95bb-4fb8-bc2a-397f4cdf9da4.png">

Under the domain name of RYANLAB.LOCAL (which will be different for you with what you named your domain), I created two Organizational Units: one for New Jersey and one for Utah. Within each of these two OUs, I created four separate OUs within each: Users, Computers, Groups, and Servers. In the future, I plan on expanding my Active Directory lab to simulate a larger business environment. For the time being, we'll stick with one Domain Controller and two simulated offices. 

<img src="https://user-images.githubusercontent.com/107446796/184461456-0e9d398f-e356-49d2-bbb6-6183433271fd.png">

Let's create a new user inside of both of these offices. You can right click inside of the white space or right click on the Users OU and add a new user.

<img src="https://user-images.githubusercontent.com/107446796/184461527-129c26ca-e61c-492e-8786-09dcf2624ae3.png">
<img src="https://user-images.githubusercontent.com/107446796/184461544-5843c757-25a1-4dcf-af2b-cd0c245ddb60.png">

For all my people wanting to get into a Help Desk type of role, you can right click on a user and reset passwords and unlock accounts. I encourage you to explore and poke around these settings as well. 

<img src="https://user-images.githubusercontent.com/107446796/184461676-3267d564-ba2a-4c14-85e0-0e0e977f1a02.png">

I encourage you to explore adding new groups (such as Human Resources, IT, Sales, etc) and play with adding different users to these groups. </br></br>

<h1>Group Policies</h1>
We're going to setup a Group Policy Object for our domain in the form of firewall rules. Back inside of Server Manager, click on Tools, and select Group Policy Management. We're going to right click on the domain and create a new Group Policy Object in the domain. It's going to be firewall rules, so we'll name it firewall.

<img src="https://user-images.githubusercontent.com/107446796/184462861-caa3ded5-0042-47f9-8d61-614b90ba0e5a.png">

Right click on the new firewall rule, and click edit. Go to Computer Configuration, Policies, Windows Settings, Security Settings, and then Windows Defender Firewall. 

<img src="https://user-images.githubusercontent.com/107446796/184463052-c3bef4c3-2bf0-42db-ba80-40a81d40bd2b.png">

We're now going to go into Inbound Connections and right click in the white space, click "new rule", then predefined, and add the following rules one-by-one: Active Directory Domain Services, File and Printer Sharing, and Windows Management Instrumentation (WMI).  

<img src="https://user-images.githubusercontent.com/107446796/184463502-4d8a28fc-f129-42bd-a6a7-8012fa0659d8.png">

We now have to worry about Outbound Connections, so go to outbound rules and add the same exceptions. Make sure that you ALLOW these connections. 

<img src="https://user-images.githubusercontent.com/107446796/184463520-17853cca-41bd-4136-a002-2643079a3198.png">

Lastly, we're going to create one final Inbound rule for ICMP packets so that connected computers on the domain are able to ping your Domain Controller. Add a new rule but select custom rule, ICMPv4, any/any, allow, and make sure to select domain only. 

<img src="https://user-images.githubusercontent.com/107446796/184463564-6ee4f102-9c8a-4af5-9cfd-8be5276f1220.png">

Finally, let's use that knowledge from the A+ and run a Group Policy Update command. Open a terminal and type "gpupdate /force" to update our newly created policies.

<img src="https://user-images.githubusercontent.com/107446796/184463589-96e67ac5-0e29-4d7c-b648-ce920ed2a41b.png">

Congrats! You've successfully set up a server with Active Directory, configured a Domain Controller, and set your first Group Policy! I'll be adding more to this lab and talking about connecting computers to the domain, configuring users and groups, and getting more technical within Active Directory, maybe even some hacking. 


