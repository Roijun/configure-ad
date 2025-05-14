
![acitvedirectory](https://github.com/user-attachments/assets/281aa924-786b-4692-9111-427bb727ea92)


<h1>Active Directory - Setup and Configuration </h1>


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory

<h2>Operating Systems Used </h2>

- Windows 10</b> (21H2)

<h2>List of Prerequisites</h2>

Have a Microsoft Azure account ready so we can create and configure our virtual machines and configure Active Directory. 
 
<h2>Overview</h2>

In this walkthrough we're going to be creating and configuring some virtual machines, before using them to deploy and configure Active Directory.

<h2>Active Directory</h2>

<h3>Setup and Configuration</h3>

- Setup & Configure Domain Controller in Azure

We're going to simulate an environment, starting with a virtual machine that's going to be running windows server, and acting as our domain controller. We're also going to create another virtual machine running Windows 10, acting as our client joining our domain. Later on we'll be able to log onto the client with users that exist within the domain controller. We're also going to set the DNS IP address and our clients network interface card to the same IP address as the domain controller. 

First thing we need to do is create a resource group and a virtual network. We'll use the basic settings while creating both of these prerequisites. Our next step is to create the virtual machine that we'll be using as our domain. We'll name it DC-1, and we'll want to make sure we place it into the same resource group that we just recently created. We also want to make sure that they're both inside the same region. When selecting our image, we want to make sure that we select Windows Server 2022. 

![dc-1](https://github.com/user-attachments/assets/52528d18-6611-49c9-b07e-057889b54e7a)

The size doesn't matter as much, but it's nice to have at least 8gb of RAM so the machine isn't incredibly slow when we're going through and using it. Give your new machine a custom username and password, and make sure to record it somewhere so we can use it later on. Last thing we need to do before creating this virtual machine, is going into the 'Network' tab and confirm that the network is set to the one we created just before creating the virtual machine, no need to worry about the subnet. With all that in order we can go ahead and create our first virtual machine.

After dc-1 has finished deploying within the Azure platform we're going to set the domain controllers NIC private IP address to be static. To navigate to the place to do that we need to first click on our virtual machine. Once there we're going to click on 'Networking' and then go to 'Network Settings'. Underneath 'Network interface / IP Configuration' is the virtual network interface card, and this is where we'll want to navigate to next. 

![NEtwork Interface](https://github.com/user-attachments/assets/8b2e9498-1d4e-4034-8190-e167ec75a298)

![ipconfig](https://github.com/user-attachments/assets/8481567c-0bd9-415d-8323-9e9e1eff3190)

Once there click on 'ipconfig1' and underneath 'Private IP address settings' we can switch our IP address to static. 
The last thing we're going to do is log on to our dc-1 machine and disable the windows firewall so we may test for connectivity. In order to do that we've got to use RDP to connect to our machine using it's private IP address. Use the login information that you created and saved earlier for logging in. Once logged in we'll go ahead and right click the windows icon in the bottome left and click on run. We'll type WF.msc to pull up the Windows Firewall settings. 

![windows fireawall](https://github.com/user-attachments/assets/13a85971-e3d0-4f63-a7d1-218b9f5ed01b)

In the 'Overview' section at the bottom of the page we'll want to click on 'Windows Defender Firewall Properties'. In this window we want to go through on turn the firewall off for each of the profile tabs listed.

![windows firewall](https://github.com/user-attachments/assets/3c2e1945-b70b-4005-9f7d-293e1466b4bc)

Once we're done with this step we can go ahead and click apply and ok. This marks the end of the setup phase for our domain controller within our simulated envirnoment.

- Setup and Configure Client-1 in Azure

For the client's virtual machine setup we're going to put it in the same resource group as the previous machine, and we're going to name it 'Client-1'. For the image we can use the normal Windows 10 machine, with a preferred 8gb of ram. The last thing we've got to do before finishing the creation of the machine is make sure the machine is attached to the same region and virtual network as dc-1, then we can hit review & create. 

The next step is to set Client-1's DNS settings to dc-1's private IP address. To do this we just need to copy dc-1's private IP address, then navigate on over to the Client-1 machine. Once there we'll want to go into 'Networking' and then into 'Network Settings'. Once again we'll want to click on Client-1's virtual network interface card, and once there we want to select DNS Servers on the left hand side of the screen.

![dns servers](https://github.com/user-attachments/assets/11f177ea-3c8d-436c-b2a5-53db779c9917)

We're going to change ther server type over to custom, and then enter the private IP address from dc-1 that we copied before hitting save up at the top of the page. We're going to do some simple testing between the two machines, but before we do that we need to restart the Client-1 virtual machine within Azure. Once restarted we're going to log into our Client-1 machine using our username and password and we're going to attempt to ping dc-1's private IP address. If we did the setup correctly the ping should be successful.

![ping](https://github.com/user-attachments/assets/7f58f69e-5984-49a4-9680-3d12bcfc2229)

Next we are going to run 'ipconfig /all' within the powershell application and when we get a response we are going to look for dc-1's private IP address as the output for DNS setting. Once we view and confirm that they match, we are done with the setup and configuration of these two machines.

![ipconfigall](https://github.com/user-attachments/assets/192d0bba-a040-47ff-8232-188bdcf79bd6)

<h3>Installation and Deployment</h3>

We're going to go through and actually install active directory on the domain controller and we're going to create a domain admin for us to use. We're also going to join our client-1 onto the domain. Doing so allows us to use all of the users that exist in the domain to log onto the client-1 machine. 

- Install Active Directory

To install active directory we're going to want to get logged in using our dc-1 machine. Once there hit the start button in the bottom left and go to open the server manager application. Once there we'll want to select 'Add roles and features' from the top of the window. Once in this window we'll want to select next until we reach the 'Server Roles' section. Once here you need to check the 'Active Directory Domain Services' box, and make sure to apply features when prompted.

![server roles](https://github.com/user-attachments/assets/fddd2fc0-f786-4c05-8e78-e240fa9412c0)

We can hit next until we reach the 'Confirmation' section, where we'll make sure to check the box near the top of the window that reads 'Restart the destination server automatically if required'. Once we've done that we can go ahead and hit install and close out the window. 

Next we need to go in and promote dc-1 to the domain controller within the system. We need to open server manager once again, and in the top right you'll want to click on the flag icon and select 'Promote this server to a domain controller'. Once here we'll select the Add a new forest option. We'll make our domain name 'mydomain.com' and hit next. 

![activedirec](https://github.com/user-attachments/assets/ecfc9723-f096-4280-bece-424b12582678)

The next window will prompt you for a password, just make it something you can remember easily. We can hit next until we reach the DNS options page. We want the 'Create DNS Delegation' box to be unchecked. With all that in place we can go ahead and hit next unti we get to the last page, then we can hit install when the system will allow us. Once it's finished installing the computer now becomes a domain controller, and the system will auto restart itself. 

- Create a Domain Admin user within the domain

When signing in back onto the dc-1 system we'll want to make sure we use 'mydomain.com/' followed by our username. We need to specifiy that we are logging in onto the domain, and not just logging in as a local user. After we sign back in we can go ahead and open our active directory. We're going to use the start button in the bottom left and search for 'Active Directory Users and Computers'.

![activedirectory](https://github.com/user-attachments/assets/4bc82bba-8915-459e-921e-19b78cb158e9)

Next we're going to create a new organizational unit called '_EMPLOYEES'. From the ADUC window we want to right click our domain, hover over new, and then finally click organizational unit. We'll create one unit named _EMPLOYEES and other named _ADMINS. 



