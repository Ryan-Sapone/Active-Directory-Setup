# Active-Directory-Setup
Setup of a home lab for Windows Server 2019 and Windows 10 to simulate a small business environment

<h1>Setting Up Windows Server 2019 with Active Directory Services, DNS, and DHCP</h1>
I'm going to skip over going how to install things in Oracle Virtualbox or VMware and jump into the initial setup after successful installation. </br></br>

Once Windows Server 2019 is installed and you're logged in, you should see that Server Manager opens up automatically. If for some reason it doesn't, you can access this by going to the Start menu and selecting it. Once here, click on Add roles and features. </br>
<img src="https://user-images.githubusercontent.com/107446796/183272727-530b3d54-5e7b-4722-be5a-374bf3aa4f55.png"></br>

Select Active Directory Domain Services from the checkboxes. </br>
<img src="https://user-images.githubusercontent.com/107446796/183272756-a453f344-5108-498c-a932-7b520e511ffb.png"></br>

Next, next, next, until you finish the installation. You should see the notification in the top right to finish setting up Active Directory and to promote this server to a Domain Controller. We'll do that soon, but first we're going to mess with the network settings. </br>
<img src="https://user-images.githubusercontent.com/107446796/183272849-8f18a074-3d3d-462c-a287-5947e30ab6f4.png"></br>

I started by changing the name of the server, which requires a reboot. But before doing that, open up your network settings and IPv4 properties and assign a static IP address, subnet mask, and default gateway to the server. For my lab, I wanted to keep my server at the .100 host. Below this, change your DNS settings to your loopback address and make your secondary DNS server your gateway. </br>
<img src="https://user-images.githubusercontent.com/107446796/183272853-c204ada7-229f-406c-8e6b-eb7a04ef92da.png"></br>

I then went ahead and went back to Server Manager, add roles and features, and added DNS and DHCP. </br>
<img src="https://user-images.githubusercontent.com/107446796/183272951-746ec51b-ca53-4250-87af-b2824717f9d8.png"></br>

Now it's time to circle back and promote this server to a Domain Controller. Click back on your notifications inside of Server Manager and click on the link to promote the server. Click on "Add a new forest" and select a domain name. For my lab, I named this domain RyanLab.local. </br>
<img src="https://user-images.githubusercontent.com/107446796/183273005-d69f9b8d-2ea2-483c-9a79-04b3e5af2c18.png"></br>

After clicking next, you'll have to set a Domain Services Restore Mode password. DSRM is like safe boot for a Domain Controller and will allow you to restore or repair an Active Directory database. </br>
<img src="https://user-images.githubusercontent.com/107446796/183273071-28e07aaf-4dbc-4daa-a1c9-f9431195a016.png"></br>
<img src="https://user-images.githubusercontent.com/107446796/183273091-87c5f280-1ccb-4416-b785-8c134bbea20f.png"></br>
<img src="https://user-images.githubusercontent.com/107446796/183273094-817bb738-fa3c-49a0-a205-00433a06f161.png"></br>

PowerShell is a powerful tool, and you can see that the configuration of Active Directory can be done through PowerShell. You can copy this script and save it/modify it in the event you wanted to replicate this in the future. </br>
<img src="https://user-images.githubusercontent.com/107446796/183273121-4cecf8c8-97be-4bcb-9011-476bbc292672.png"></br>

Once this is finished, you might see some errors regarding DHCP or DNS; you can skip these for now and proceed to reboot the server. Once you log back in, congratulations! You now have Windows Server 2019 configured with Active Directory services, DNS, and DHCP. Now it's time to start playing around and replicate the normal IT administration stuff. </br></br>




