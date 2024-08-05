<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Domain Controller and Client Virtual Machine
- Install Active Directory
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://github.com/user-attachments/assets/a3ea1a56-d1dd-4654-84ae-447b789efa46"/> <img src="https://github.com/user-attachments/assets/bdc0314b-0b7e-4119-a052-effaea3581f7"/>
</p>
<p>
First, we will create our Domain Controller Virtual Machine which will run Windows Server 2022 and name it "DC-1". Once created, we will make the NIC Private IP address static by going to Networking>Network Settings>IP Configuration>ipconfig1 changing the Allocation to static.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/d4a83894-39b9-488b-a609-198f91d4c9b0"/>
</p>
<p>
Create a client VM running Windows 10 named "Client-1" using the same resource group and Vnet as DC-1.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/feb5ca00-ef60-4be7-a727-ae3de6d8ec35"/>
</p>
<p>
In the Domain Controller, enable ICMPv4 on the local windows Firewall. Windows Defender Firewall with Advanced Security>Inbound Rules>Sort Protocol. Enable both "Core Networking Diagnostics - ICMP Echo Request". In Client-1, check to see if pinging the Domain Controller's private IP succeeds now.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/db1a5aad-bf57-4960-8f27-8b117b3b281d"/> <img src="https://github.com/user-attachments/assets/d9121532-e618-4ca4-9880-fd58c136661b"/> <img src="https://github.com/user-attachments/assets/162fe1ba-4bdd-47fc-85f1-52cec1a24a4e"/>
</p>
<p>
To install Active Directory, in the Domain Controller, we will open Server Managers and Add roles and features and install Active Directory Domain Services. Once completed, click the flag in the top right corner and promote server to a domain controller. Add a new forest, provide a root domain name (ex: mydomain.com) and password and install.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/826e257b-ac4e-44ef-bff9-8470b680344c"/>
</p>
<p>
Once Active Directory is installed, you will be prompted to restart. Log back in as user: mydomain.com\labuser
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8ed141d5-6750-401c-b386-e9947ba38f42"/>
</p>
<p>
To add Admins and Users, go Tools>Active Directory Users and Computers, right-click "mydomain.com" and create a new Organizational Unit (OU). Make one OU called "_ADMINS" and another called "_EMPLOYEES". 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b91315e7-dde5-438f-aef9-44508d0d4304"/> <img src="https://github.com/user-attachments/assets/4b8e64e3-a910-49ba-b0a3-e9de5d5b59e9"/>
</p>
<p>
In the Admins OU, create a admin user. Once that user has been created, right-click the user and go Properties>Member of>Add and add the user to the Domain Admins group. Log out of Domain Controller and log back in as admin user.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/02c168d2-8597-4cb7-8e3c-33e9a0bef1b0"/>
</p>
<p>
In Azure, set Client-1's DNS to the DC's Private IP Address. Network Settings>Ip Configuration>DNS Servers and add custom DNS with the private IP. From the Azure Portal, restart Client-1 and log back in once restarted.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/2dea9111-c18d-4eef-88a1-b601c825b56e"/>
</p>
<p>
In Client-1, go Settings>System>About>Rename this PC>Change Domain or Workgroup, change to Member of Domain and insert Domain name (mydomain.com). Sign in using credentials of admin user previously created.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/5cb4b491-0c9c-4c39-a2d3-b6511ff37abf"/>
</p>
<p>
Log into Client-1 as user admin (mydomain.com\jane.admin). Open system properties and go to Remote Desktop. All "Domain Users" to access remote desktop. All users connected to the Domain can now log into Client-1.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/cc961767-cdca-4a6d-81aa-b1cce7db3426"/>
</p>
<p>
We can create some additional users that can be used to log into Client-1. Logging into DC-1 as jane.admin, and opening PowerShell ISE as an administrator, create a new file and paste the contents of the script (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) and run the script, observing all the accounts being created. We can now log into Client-1 using one of these new accounts.
</p>
<br />
