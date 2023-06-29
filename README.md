<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- Powershell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Deployment and Configuration Steps</h2>

- Step 1: Create a new Network Group in Azure, named Active Directory, with 2 VMs; one running a Windows database, the other running Windows 10 
- Step 2: Set the DC’s NIC private IP address from Dynamic to Static
- Step 3: Connect to both VMs using Remote Desktop
- Step 4: Initiate a perpetual ping from the Client to the DC; if there is no reply, enable Core Networking Diagnostics in the DC’s firewall
- Step 5: Install Active Directory on the DC and promote it to a domain controller
- Step 6: Create an Admin account and Organizational Units (OU) in Active Directory Users and Computers (ADUC), then log back in under the Admin account
- Step 7: Set the Client’s DNS settings to the DC’s Private IP address, then join the Client to the DC
- Step 8: Enable Remote Desktop for domain users to access the Client
- Step 9: Create user accounts using a PowerShell script (run PowerShell ISE as administrator)
- Step 10: Connect to the Client with Remote Desktop using one of the newly created user accounts


<h2>Setup Resources in Azure</h2>

<p>
Hello! Welcome to my Active Directory tutorial. Today we're going to start of by creating a new resource group in Azure named Active Directory, and 2 Virtual Machines, one running Windows Server 2022 and the other with Windows 10. Make sure that both VMs are in the same Net work and subnet. The Windows Server 2022 VM will serve as the Domain Controller (DC) and the Windows 10 VM will serve as the Client. 
</p>
<p>
<img src="https://i.imgur.com/fgVZSMA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<p>
Now we need to set the DC’s NIC (Network Inteface Controller) private IP address from Dynamic to Static. Later in the lab when we configure the Client’s DNS settings, the DC’s private IP address, the Static IP address will make it easier for any services to access where a device is. 
</p>
<p>
<img src="https://i.imgur.com/uxbTjwd.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<h2>Ensure Connectivity between DC and Client</h2>
  
<p>
After connecting to both VMs using Remote Desktop, to ensure connectivity we will initiate a perpetual ping from the Client to the DC. ICMPv4 is the protocal that ping uses. Now will try and ping the DC from Client with ping -t (perpetual ping).
</p>
<p>
<img src="https://i.imgur.com/Agb7Vvz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Now we will remote access into DC1 and poke a hole in the firewall to allow ICMPv4 traffic. We will be able to see the ping on the Client side go thru. To do this, we will need to access the firewall settings in DC_1 thru the Windows Defender Firewall with Advanced Security -> Inbound Rules -> and enable ICMPv4 Core Networking Diagnostics.
</p>
<img src="https://i.imgur.com/UlHs1ZB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p> 
<img src="https://i.imgur.com/yHP1K55.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p> 
<br />

<h2>Install Active Directory</h2>

<p>
Login to DC-1 and install Active Directory Domain Service. Be sure to select the Active Directory Domain Services in Server Roles to install the proper version. Just continue on setting up the defaults. There is a bit more configuration to do before everthing is ready to use.
</p>
<p>
<img src="https://i.imgur.com/8MfOmMa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
We will need to create a new forest, name it anything you'd like! I'm going to choose Deans_Domain.com, most walkthroughs I've seen have just used MyDomain.com as a default. Most likely the vm will automaticlly, then log back in under the new domain that we just created.  For example, when i go to connect to DC_1, I will log in as Deans_Domain.com\Labuser1.
</p>
<p>
<img src="https://i.imgur.com/PflKIDE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
<img src="https://i.imgur.com/XKw2BPK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Active Directory is all set up! Let's create two Organizational Units in our domain named _ADMINS and _EMPLOYEES. To do so, in the upper left hand side of the Server Manager Dashboard, click on tools, then Active Directory Users and Computers.  
</p> 
<img src="https://i.imgur.com/nJAyHQg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p> 
Now create a new User, I choose Jane Doe, as an Administrator with the username: Jane_admin and add her as a member of Domain Admins Security Group. Log out from the default account we were in and log back in under Jane.
</p>
<img src="https://i.imgur.com/zwBeNnj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://i.imgur.com/vchgXRe.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
To be able to continue setting up the domain, I will join Client-1 to the domain (Deans_Domian.com). From Azure, we will change client-1's DNS settings to the DC's Private IP address. After you do that restart Client-1 from within Azure. Restarting Client_1 will flush the dns cashe, this will make more sence in later labs.
</p>
<img src="https://i.imgur.com/Ugf8277.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Now we will set up remote desktop for non-administrative users on Client-1. We have to log into Client-1 as an admin and open system properties. Click on "Remote Desktop", allow "domain users" access to remote desktop. Enabling this for Domain Users would allow for any user accounts in the domain to be able to log into Client-1 as a normal user.
</p>
<img src="https://i.imgur.com/ZtM4tbW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br /> 
</p>
Finally, to verify that noraml users can RDP into Client-1, we will use a Powershell script to generate 10,000 (Thousands) of users into the domain. After the users are created we will randomly select one and RDP into Client-1.
</p>
<img src="https://i.imgur.com/jNlb1r0.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />


