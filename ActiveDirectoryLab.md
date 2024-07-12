<h1>Home Lab Active Directory Configuration</h1>

<h3>Microsoft Server 2019 Configuration</h3>

![ADlab1](https://github.com/user-attachments/assets/0b70fdfa-4265-4bd0-918b-4f1e16d1c641)

<b>
For this Lab, I decided to set up two NIC's on the Domain Controller. Adapter 1 is set to NAT, and Adapter 2 is set to internal network
The NAT adapter will bridge the internal network to the WAN while the Domain Controllers internal network adapter will work as the default gateway for client
devices connected to the domain. 
</b>

<h3>Taking a look at the NIC's</h3>

![AD_2](https://github.com/user-attachments/assets/a89f120f-12a4-4e26-9b51-ac7186b3b615)

<b>
Identifying which adapter is which, I labelled them accordingly, the thing to look for when doing this is within the details the adapter in its properties,
the NAT adapter will have an ip like 10.x.x.x while the Internal network adapter has a random address that does not comply to RFC 1918 
</b>

<h3>Addressing the Internal Network</h3>

![AD_Assign_Internal_IP](https://github.com/user-attachments/assets/9352f135-f698-40e6-962c-3b53ce2cbae1)

<b>
Going into the internal network adapters properties, you will find a large list of different configurations you can make, the one you need to look for 
is the IPv4 settings. Once there you can manually configure the IP, Subnet, and the DNS. This is only mandatory to do manually for this adapter since it is the default gateway for the Internal Network<br><br>
I Chose to assign:
  
IP: 172.16.0.1<br>
Subnet: 255.255.255.0<br>
DNS: 127.0.0.1

The Domain Controller is handling the DNS server, and is acting as the default gateway, thus, the DNS address points back to itself via loopback and there is no need to assign a default gateway address.
With that out of the way we have to set up Domain Services next.
</b>

<h3>Domain Service Installation</h3>

![Domain_Services_Install](https://github.com/user-attachments/assets/a95e22fb-b86c-4707-83b1-02ffbf7987ac)

<b>
  This Step is fairly trivial, simply check off the box for Domain Services and go through the install wizard. It takes a minute to install but once that is done, I recommend promoting/establishing 
  the domain as the active domain. 
</b>

<h3>Establishing the Domain</h3>

![Promote_to_activeDomain](https://github.com/user-attachments/assets/60cd41c5-c1e2-4ec3-8280-43806de0dbf8)

<b>
Opening the AD Domain Service Wizard, choose the 'add a new forest' option and then name your domain what you would like I chose mydomain.com as the root name of my domain, it can be anything really
I recommend 'domainname'.xxx as the naming format. As long as you keep a systematic naming format for your domain(s) and network that is maintained and uniform/ has logical reasoning behind it you will be good.
</b>

<h3>Creating an Adming organizational unit and a user</h3>

![CreateAdminOrganizationanduser](https://github.com/user-attachments/assets/9a815043-dd93-4b65-bdbc-323110606d08)

<b>
Opening up the 'Active Directory Users and Computers' under the tools tab, you are given a complete overview of your domain's users and devices established and logged within your domain. 
I created a new organizational unit which is meant to be comprised of all the administrative users. I then created a new user (Myself).
</b>

<h3>Assigning Users to Groups</h3>

![assigntoadmingroup](https://github.com/user-attachments/assets/65568ae6-d78d-43d4-b3e5-9b9f9a884903)

<b>
To assign a user to a group, simply find whichever user you are looking for, either in the user directory, or in our case and organizational unit and then click on their properties option
you can navigate to the add/remove group tab. Then type in the name of the group you would like to add that user to and then click add, and apply. It is as simply as that.
</b>

<h3>Routing and Remote Access Service Installation</h3>

![RASInstall](https://github.com/user-attachments/assets/8be796cc-67e7-48bf-a849-c3925f4faef8)

<b>
This step is similar to the Domain Service installation, simnply navigate to the service installation wizard, simply check off the Remote Access Service and click through until you get to the Role services section
of the Remote Access installation options.
</b>

<h3>Selecting to Installing Routing for RAS</h3>

![RoutingforRAS](https://github.com/user-attachments/assets/80ff6b98-fa26-4738-a0f1-9a7c70f03cf5)

<b>
Make sure to select to install Routing in order to add support to the NAT adapter our domain controller has so the internal network adapter can route packets across to the NAT adapter from say a Clients internal network adapter to the WAN.
This provides WAN internet connectivity to Client devices within the domains internal network. 
</b>

<h3>Enabling RRAS and configuring it for NAT</h3>

![RoutingandRemoteAccess](https://github.com/user-attachments/assets/9589ca78-aae0-46f0-89fc-8fd8679b5794)

![Nat](https://github.com/user-attachments/assets/2b2c41de-acb0-478a-8497-4f6cafa42b95)

<b>
Once installed, you can find RRAS under tools, naviagte to it and select to enable and configure it. I selected NAT since, in my case, that is what I require. You may need to configure it
different for your own needs
</b>

<h3>Installing DHCP</h3>

![DHCP](https://github.com/user-attachments/assets/b503ebf6-ff7b-418a-8b86-43e5c1b9f656)

<b>
Same as the Domain Services and RRAS install, navigate to the services installer and select DHCP. check through the installer
install the DHCP services and Shamwow, now navigate to the DHCP services found under the tools tab
</b>

<h3>Setting up Scope, Default Gateway, and DNS for DHCP services</h3>

![DCRAZNATCONFIG](https://github.com/user-attachments/assets/14f5b871-4289-4f71-b9ae-8e7cd128535f)

![DHCPIPV4Scope](https://github.com/user-attachments/assets/58c85cb2-30c5-4aab-8356-effef8fbc174)

![Scope](https://github.com/user-attachments/assets/2f2eb2a2-be31-475e-9bdf-ac5bcf5968f2)

![DefaultGateway](https://github.com/user-attachments/assets/7f45cb91-f3ec-482a-845d-76a46a9dcf9d)

<b>
Scope was set between 172.16.0.100-200
Default Gateway and DNS was set to 172.16.0.1 (the domain controllers internal network adapter logical address) 
The lease time was left at 8 days per address since this is a home lab and I do not have people coming and going all the time
in real world application, you should configure the lease time to fit the needs of the specific situation
upon completing this step I authorized the DHCP services and refreshed to ensure that there was a green checkmark next to the IPv4 and IPv6 ensuring that it is active and working
</b>

<h3>Mass adding users to domain via Powershell script</h3>

![ps](https://github.com/user-attachments/assets/3824a321-0a8e-4a77-bf52-f48dfffc7c3b)

![addUsers](https://github.com/user-attachments/assets/02e975d8-b366-4d58-9563-e5abc27cd3c5)

![CheckingUsers](https://github.com/user-attachments/assets/a02a36b3-8aaa-4e7c-8a02-b19de874a719)

<b>
I simply downloaded a powershell script off of github which auto creates and adds a bunch of user profiles, this was done to streamline filling the lab environment with users to the domain
It is a lot easier and more efficient than manually adding a bunch of users. 
This was done more so for future lab exercises dealing with groups, access controls, roles, etc.
</b>

<h3>Client system configuration</h3>

![Clientvmconfig](https://github.com/user-attachments/assets/21632891-c9ef-4761-9f76-4dbdfb65573c)

<b>
I used a windows 10 enterprise iso and configured one adapter and set it as Internal Network, the DHCP services on our domain controller will handle all the configuration automatically for the adapter once we log in
</b>

<h3>Joining the Windows 10 Enterprise system to the domain</h3>

![Joindomainasuser](https://github.com/user-attachments/assets/05f779b8-66ac-47be-b088-592c8813858e)

<b>
Under the about section of the system, the section where you can rename the name of your system, you can scroll down to an advanced optioon and select to add/change the domain which the computer is connected to, select domain in the pop-up window
and input whatever your domain is in my case it was 'mydomain.com' upon clicking apply/ok your system will be connected and apart of the domain.
</b>

<h3>Checking whoamI</h3>

![whoami](https://github.com/user-attachments/assets/ce0fc7ef-fccf-4551-ad7f-84b25a593d01)

<b>
I opened a command line and ran the whoami command to check who I am on the network. as you can see I am logged in as my user account and under the mydomain.com/ directory.
</b>

