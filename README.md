# Active-Directory-Setup
A simple guide of how to set up Windows Server 2019 with Active Directory Domain Services and join other Windows machines to the domain. 

<h1>Server 2019 Initial Installation</h1>
After creating the virtual machine for Windows Server 2019, boot it up and begin the installation process. Create your disk partitions, create your Administrator password, and install your VMware tools if using VMware. Make sure you rename the server before rebooting. In my lab, I named my server RYAN-DC. 
<img src="https://user-images.githubusercontent.com/107446796/185997847-c6ff9464-181d-4a66-8500-f4611282a8a2.png">

Once the PC reboots and you're back inside of Server Manager, we're going to install Active Directory Domain Services. In the top right corner, click on Manaage > Add Roles & Features > select Role-Based > under Server Selection click next > check the box Active Directory Domain Services > Click Add Features and then click next > under Features click next > under AD DS click next > under Confirmation click install. 
<img src="https://user-images.githubusercontent.com/107446796/185998741-f7089175-1b8a-4590-bd06-6b5aeba558ec.png">

Once the installation is complete, hit the close button. In the top right, you should see the flag notification. Click it and click on the blue link to promote to a domain controller. 
<img src="https://user-images.githubusercontent.com/107446796/185999030-67194c5a-b7d6-4c9c-abb1-a91f95e7033b.png">

Now we're going to go through this process of promoting this server to a domain controller. Select "Add a new forest" and type a root domain name in. This could be anything you want. For my lab, I named this "RYANLAB.local", then click next > set a DSRM password and click next > under DNS Options click next > under Additional Options wait for the NetBIOS name to populate and click next > under Paths click next > under Review Options click next > under Prerequisite Check click install and wait for the reboot. 
<img src="https://user-images.githubusercontent.com/107446796/185999658-ac75f478-8990-44b6-8c45-a1a45586e008.png">

<h1>Windows 10 Initial Installation</h1>
For this lab, I have two virtual machines with Windows 10 and a third virtual machine with Windows 11. Let's install the Windows 10 virtual machine and go through the quick initial setup. Once the disk is partitioned, you'll reach a Microsoft sign-in page. Rather than that, we're going to click in the bottom left for "Join Domain instead". Create a username and a password for a user. In my example, my username was Alice Cooper. Set the password, recovery questions, turn off all tracking information, disable/skip Cortana, and let Windows get set up. The only thing we're going to do at this time is change the name of the computer. Since this is my second Windows 10 machine, I'm going to rename this PC to RYANLAB2. Let the computer reboot and go ahead and log back in. For now, we're going to go back to Windows Server.
<img src="https://user-images.githubusercontent.com/107446796/186002424-ec81eac2-9ae8-4ff0-b083-686e8aa57efb.png">

<h1>Active Directory: Users, Groups, Policies</h1>
Let's start configuring some users, groups, organizational units, policies, etc. Once back inside of Server Manager, click on Tools > Active Directory Users and Computers. 
<img src="https://user-images.githubusercontent.com/107446796/186003413-7d76476f-5a11-4e70-b210-3a92614884e6.png">

You can right click the dropdown on your domain to see all of the current Organizational Units (OUs). In here, you can see that we have some built-in security groups under the Users OU. Let's create a new OU called Groups and move all of these default security groups into that OU. You can select them all and drag-and-drop into the new Groups OU or you could right click and select Move. 
<img src="https://user-images.githubusercontent.com/107446796/186004666-fa0d9cbd-fcd3-4746-98f1-898b1d2ed113.png">
<img src="https://user-images.githubusercontent.com/107446796/186004683-8e1dcc51-40e6-44e2-a8fc-b88ddc22d1ed.png">
<img src="https://user-images.githubusercontent.com/107446796/186004689-5b74a0eb-f22c-495c-8eae-e70224e5e9db.png">

Time to create some new users. Inside of our Users OU, we can right click in the white space and select New > User. In my example, I created Alice Cooper, Bob Cooper, and Chad Cooper and set passwords for each of them. In the real world, you should make sure to force the user to change their password upon next login and make sure that your organization has strong password policies in place. I also created a test administrator account and a service account (with the password in plaintext in the description) since this is my penetration testing environment as well. Again, do not do this in the real world.
<img src="https://user-images.githubusercontent.com/107446796/186006095-c4f3da28-5de9-4783-af7f-8c3cd895c871.png">
<img src="https://user-images.githubusercontent.com/107446796/186007118-a72cf68e-08de-408c-b520-7a75218960b7.png">

For all of my help desk and desktop support people, becoming familiar with Active Directory Users and Computers will be important. This is where you will be resetting passwords and potentially managing groups. Now it's time to get some file shares going. Back inside of Server Manager, click on File and Storage Services on the left, click on Shares, and in the top right, click Tasks and select New Share. 
<img src="https://user-images.githubusercontent.com/107446796/186008152-15e0f72e-a978-4f81-869f-d241fe102db5.png">

Select Profile: SMB Share - Quick</br>
Share Location: RYAN-DC (or whatever your domain is called)</br>
Share Name: hackable (again for my hacking lab)</br>
Other Settings: click Next</br>
Permissions: click Next (using defaults)</br>
Confirmation: Create</br>
<img src="https://user-images.githubusercontent.com/107446796/186008833-19c43925-07ed-41e1-95e7-c20fe09d6114.png">

Now, I'm following along to Heath Adams' walkthrough of setting up some Active Directory attacks, so if you aren't interested in penetration testing then feel free to stop here and skip to the next header where we join our Windows 10 PCs to the domain. In order for some of the attacks to work against the SQLService that was set up earlier, we have to set the Service Principle Name. I'm still learning too, so more information can be found here: https://docs.microsoft.com/en-us/windows/win32/ad/service-principal-names </br></br>
If you're still following along, open up a command prompt as Administrator. In brackets, replace the text with what your Domain Controller name is as well as your Domain name. </br></br>
setspn -a [DOMAIN CONTROLLER NAME]/SQLService.[DOMAIN NAME]:55000 [DOMAIN NAME]\SQLService</br>
<img src="https://user-images.githubusercontent.com/107446796/186010609-4e981ef7-4ae6-4a9f-a6fa-5aec6950c174.png"></br>
Now the next command to verify it was successful: </br></br>
setspn -T [DOMAIN NAME] -Q \*/\*</br>
<img src="https://user-images.githubusercontent.com/107446796/186010769-f5782774-a27c-476b-b5bb-711d414b4333.png"></br>

Let's set up some group policy now. On your desktop, search for Group Policy Management and right click to open as an Administrator. Once you drop the menus down to find your domain, right click it and select "Create a GPO in this domain". We're going to name it "Disable Windows Defender" so that we don't have to go through the trouble of obfuscating our payloads in the future. 
<img src="https://user-images.githubusercontent.com/107446796/186011968-b99a6736-4967-41f2-8854-339d2769acdb.png">

Now that we have the Group Policy Object (GPO) in place, let's right click on it and select edit. Now we're going to play the dropdown game and click on Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Defender Antivirus. From here, double click on "Turn off Windows Defender Antivirus" and click on Enable, then click Apply and OK. You can see in the third screenshot that the rule is now applied. 
<img src="https://user-images.githubusercontent.com/107446796/186013019-f735e2fe-38c6-4102-b099-f877cf7aa32a.png">
<img src="https://user-images.githubusercontent.com/107446796/186013053-f8b9b0a8-81b0-4631-8682-c8d9b4795728.png">
<img src="https://user-images.githubusercontent.com/107446796/186013079-5dcc2dcb-92cd-44c4-8d3c-44bf3c0105f8.png">

One last thing is to make sure this policy is set to Enforced. You can switch back to the Group Policy Management tab, and right click on your domain name to select Enforced. 
<img src="https://user-images.githubusercontent.com/107446796/186013640-90a5435a-ed85-4920-a618-d32ae00326be.png">

Before we get into connecting the other Windows 10 machines to the domain, let's add a certificate to the domain. Under Server Manager, click on Manage > Add Roles & Features, and click next until you get to the Server Roles page. From here, select the Active Directory Certificate Services feature, and click next until you get to the confirmation page. Make sure to select the checkbox to restart the destination server if required. Once installed, click the notification box at the top and click Configure Active Directory Certificate Services. From here, click next until you get to Role Services. Select the "Certification Authority" box and click next. Keep clicking next until you get to the Validity Period. Change this from 5 years to something like 99 years. Click next, next, next, and finally configure. Go ahead and reboot the server, and then we're onto connecting our Windows 10 machines.
<img src="https://user-images.githubusercontent.com/107446796/186024460-d82b81dc-7e44-41e1-9cd7-1dbaab5c94a3.png">
<img src="https://user-images.githubusercontent.com/107446796/186024488-ae31eb8e-3cb2-445e-a725-f497c0f73fde.png">



<h1>Joining Windows 10 Enterprise Machines to the Domain</h1>
First thing that we'll do is create a new folder for our first Windows 10 machine. I named it "Share1". Right click and select Properties, click on Share, click on Share again, and make sure you select Yes for network discovery. 
<img src="https://user-images.githubusercontent.com/107446796/186016537-6507f46f-c27d-4ecd-bcaf-fa2e23ca2638.png">

Let's grab the IP address from our Domain Controller and note it down. In my case, it was 192.168.74.131. Let's go to our Windows 10 machine and configure TCP/IPv4 settings to make sure we set our DNS server as the IP address of our Domain Controller. 
<img src="https://user-images.githubusercontent.com/107446796/186018244-2f73dd3c-c005-4a69-a36c-67a5158e4af1.png">

In the search field on the desktop, type "access work or school" to bring up the feature to join the domain. Click on the Connect button and choose "Join to a local Active Directory domain". Type in your domain name (in my case, RYANLAB.local) and login with the Administrator account. Go ahead and skip the next prompt and restart the computer. 
<img src="https://user-images.githubusercontent.com/107446796/186019101-84408f4a-6335-4e67-bfc7-244699c38770.png">
<img src="https://user-images.githubusercontent.com/107446796/186019115-5073ba01-6cbe-40e6-abb7-4f1013d09c31.png">

Now when we reboot, rather than logging into the machine as the local user, we're going to select Other User. You'll see now you're signing into the domain. Rather than logging in as Alice Cooper, I'm going to use the username and password I created inside of Active Directory Users and Groups (acooper). 
<img src="https://user-images.githubusercontent.com/107446796/186019740-03846fdd-1cf2-4e62-8cc4-27f544ab8a52.png">

So again - we are going to configure these machines as part of a hacking lab. Feel free to skip over these next bits if you don't want to do the L337 H4x0r stuff. If you do, carry on. </br></br>

So now we're going to make acooper a local administrator of the first Win10 machine (RYANLAB1) and a local administrator of the second Win10 machine (RYANLAB2). So log off and log into the first Win10 machine as an administrator of the domain (note the RYANLAB\administrator as the username, otherwise you're not signing in as the domain administrator). Once signed in, open up Computer Management > Local Users and Groups > Groups > and double click on Administrators. From there, you can type in the login name of the user you want to add (acooper). If you hit Check Names, acooper is the only one so he gets added, otherwise it will give you a list of names to select. Click Apply and OK. Before we go, let's make sure that network discovery is enabled. Open up File Explorer, click on Network, and right click the banner message to turn on network discovery and file sharing. Make sure to do all of the same on the second Win10 machine. Also on that second Win10 machine, let's set up bcooper as an administrator of just that second machine as well. </br>

<img src="https://user-images.githubusercontent.com/107446796/186021608-252867e7-4cb3-436f-80c5-7de27bc45c2f.png">
<img src="https://user-images.githubusercontent.com/107446796/186021507-ef734023-fdfc-44aa-9a15-c141ae15f22a.png">
<img src="https://user-images.githubusercontent.com/107446796/186025115-f5542d26-cf6c-45a9-b4cd-374060b2ca08.png">
<img src="https://user-images.githubusercontent.com/107446796/186022283-78711b1e-6113-4247-b61f-86db2034f655.png">

That's it! We were able to successfully set up a Domain Controller, join two computers to the domain, create a few new users, manage Group Policy Objects, and configure a SOHO environment. If you followed all of these steps, you now have an Active Directory environment to practice Help Desk/Desktop Support skills or penetration testing skills.
