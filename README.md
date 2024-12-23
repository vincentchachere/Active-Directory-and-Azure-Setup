<br>

<p align="center">
<img width="700" src="https://github.com/user-attachments/assets/9b6b0a51-6411-4e01-96c5-1bb31e6fd986" alt="Microsoft Active Directory Logo"/>
<br>

<br>

<br>

<h1>Building Active Directory Infrastructure: Setup for Active Directory and Network Traffic Analysis Between Azure VMs</h1>
<br>

<p align="center">
<img src="https://github.com/user-attachments/assets/ef89ffa6-e50e-4996-bca1-85925307f212" height="80%" width="80%" alt="9"/><br />
</p>
<br />

# Lab Overview
This tutorial outlines the implementation of on-premises Active Directory within two Azure virtual machines.<br/>

## On-Premises Active Directory Deployed in the Cloud (Azure)
Active Directory essentially manages user accounts, passwords, permissions, and devices at large scale. This tutorial explains how to implement on-premises Active Directory in Azure Virtual Machines.

<ins>The difference between On-Premise Active Directory and Azure Active Directory</ins>:

- `On-Premise Active Directory`: Refers to infrastructure hosted and managed locally within an organization's physical data centers. This requires direct management and maintenance by the organization.

- `Azure Active Directory`: Refers to infrastructure and services provided remotely by Microsoft, hosted on their global data centers. This offers scalable, managed cloud services with remote access.

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## Operating Systems Used

- Windows Server 2022
- Windows 10 (21H2)

## Deployment and Configuration Steps

<details>

<summary>

## ⚙️ Part 1: Building Active Directory Infrastructure

</summary>

### 1. ) Create Domain Controller (DC-1)

First, create a resource group to host the virtual machines: DC-1 (Domain Controller) and Client-1.

<ins>Here are the following configurations</ins>:

  - Resource Group: `Active-Directory-Lab`

  - Virtual Machine Name: `DC-1`

  - Region: `East US`

  - Image: `Windows Server 2022 Datacenter: Azure Edition - x64 Gen2` (*Make sure to choose the correct VM image, or setting up the Domain Controller may fail.*)
   
  - Size: `Standard_D2s_v3 - 2 vcpus, 8 GiB memory ($137.24/month)`

  - Username: `labuser` (*Whatever you want - Just remember it*)

  - Password: `Whatever you want - Just remember it`

  - Check: `The Two Licensing Boxes` at the bottom

  - Go To: `Networking` Tab to create your Virtual Network and Subnet

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/f4af6123-245c-4ec0-809f-cd0b0109e4cf">

<br>
<br>
<br>

<ins>Within your Network tab</ins>:

  - Virtual Network: `Active-Directory-Vnet`

  - *The Subnet will create itself*

  - Click: `Review + Create`

    Then..

  - Click:  `Create`

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/0eccc2b2-0b13-4351-8315-b84b42e26d1a">

<br>
<br>
<br>

### 2. ) Set Domain Controller's (DC-1) NIC Private IP Address to be Static

 - Resource Group: `Active-Directory-Lab` > VM: `DC-1` > `Network Settings` > Network Interface: `dc-1335_z1` > `ipconfig1`
	   
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/fd418504-f33c-4938-b41d-52e5326486b7">

<br>
<br>
<br>

<ins>Setting DC-1’s Private IP address to be static</ins>:

- Resource Group: `Active-Directory-Lab` > VM: `DC-1` > `Network Settings` > Network Interface: `dc-1335_z1` > `ipconfig1`

  - Select: `Static`

  - Click: `Save`

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/c017c1de-e443-4abe-963b-ede58dceb837">

<br>
<br>
<br>

### 3. ) Create Client-1 VM

*You may need to wait a bit for the VNET (Active-Directory-Vnet) to be created.*

<ins>Similar to creating your domain controller, here are the Client-1 Configurations</ins>:

- Resource Group: `Active-Directory-Lab` (Same as your Domain Controller: DC-1)

- Virtual Machine Name: `Client-1`

- Region: `East US` (Same as your Domain Controller: DC-1)

- Image: `Windows 10 Pro, version 22H2 - x64 Gen2`

- Size: `Standard_D2s_v3 - 2 vcpus, 8 GiB memory ($70.08/month)` (Same as your Domain Controller: DC-1)

- Username: `Use the same one you used for you Domain Controller` for simplicity sake

- Password: `Use the same one you used for you Domain Controller` for simplicity sake

- Check: `The Licensing Boxe` at the bottom

- Go To: `Networking` Tab to select the same VNET as your domain controller (DC-1)

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/d68b9dde-7cf9-4c3b-9a3b-54b07117a782">

<br>
<br>
<br>

### 4.) Test Connectivity Between the Client-1 and Domain Controller (DC-1)

- Remote Desktop (RDP) into: `Client-1` (labuser)

- Once Inside Clien-1 Open: `PowerShell`

- Perpetual Ping: `DC-1's private IP Address`

  - Type In: `ping 10.1.0.4 -t`

*The ping request continually times out due to the firewall settings.*

*To fix this, we need to enable the two ICMPv4 inbound rules on DC-1's local Windows firewall.*

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/9006e012-66c0-4e64-9ce7-0554e78a861d">

<br>
<br>
<br>

<ins>Test Connectivity Between Client-1 and the Domain Controller (DC-1)</ins>:

- Remote Desktop (RDP) into: `DC-1`

- Search: `wf.msc` (Windows Firewall - Microsoft)

*Look for the rules with Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)*

- Enable: `Both ICMPv4 Inbound Rules` (There are <ins>two</ins> you need to enable)

*They should have two green check marks next to them when you enable them just like the others.*

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/3b0ec287-a457-4508-a08c-fb6c0c3b1c39">

<br>
<br>
<br>

<ins>Test Connectivity Between Client-1 and the Domain Controller (DC-1)</ins>:

- Log back into: `Client-1`

*Notice the command line will automatically begin pinging DC-1 successfully*

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/58978843-73c7-47b5-a4d9-d4ff062487fc">

<br>
<br>
<br>

### 5. ) Set Client-1’s DNS settings to DC-1’s Private IP address

Now we will Set Client-1’s DNS settings to DC-1’s Private IP address, which will allow the client-1 VM to resolve domain-related DNS queries through the domain controller (DC-1).

- Go To: Resource Group: `Active-Directory-Lab` > VM: `Client-1` > `Network Settings` > Network Interface: `client-11408_z1` > `DNS servers`

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/2be62c15-ec58-4afe-8579-2e0bd3929243">

<br>
<br>
<br>

<ins>Setting Client's DNS Servers to DC-1's Private IP Address</ins>:

- Go To: Resource Group: `Active-Directory-Lab` > VM: `Client-1` > `Network Settings` > Network Interface: `client-11408_z1` > `DNS servers`

- Select: `Custom`

- Input: `DC-1's Private IP Address` (Example: mine is: 10.0.0.4)

- Click: `Save` when done

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/c0e0d75a-6a25-4fd1-9c76-d50539b68c97">

</details>

<details>

<summary>

## ⚙️ Part 2: Active Directory Delpoyment, Configuration, and User Creation

</summary>

### 6. ) Install Active Directory

- Log back into: `DC-1`

  - Open: `Server Manager`
 
  - Select: `Add Roles and Features > Follow the prompts`
 
  - At Server Roles, check: `Active Directory Domain Services`

    - *Ignore how the picture below already says "Installed"*

  - Select: `Add Features > select Next`

  - *Complete the installation*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/37cea973-22a7-4ca7-baf4-775a24cf20f8"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/51c57e71-3b3a-4629-8461-cba0e0894e9f"><br>

<br>
<br>
<br>

- At the top right of the Server Manager Dashboard, click on: `the flag`

- Select: `Promote This Server to a Domain Controller`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/3b799874-428a-4358-9430-2a500d7f4544"><br>

<br>
<br>
<br>

- Select: `Add a New Forest`

- Root domain name: `mydomain.com`

- Select: `Next`

- Create: `a password`

- Select: `Next` and follow the prompts

- Select: `Install` to complete the installation

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/5a0f1ee2-fada-45da-877f-2c811b7a2dba"><br>

<br>
<br>
<br>

- *DC-1 will automatically restart*

- Log back into DC-1 as user: `mydomain.com\*usernameyoucreated*`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/6d2b5906-21a0-420e-b40c-d6753b1ab97a"><br>

<br>
<br>
<br>

### 6. ) Create an Admin and Normal User Account in Active Directory v1.15.8
     
- On DC-1, open: `Server Manager`

- Click: `Tools` at the top-right of the screen

- Select: `Active Directory Users and Computers`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/1e719e76-76c3-459c-b3ff-09c29116614d"><br>

***

- Right-click: `mydomain.com > New > Select Oranizational Unit (OU)`

- <ins>Create 2 OUs</ins>:

  - Name the first: `_EMPLOYEES`
 
  - Name the second: `_ADMINS`
	
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/934a8716-3682-4e6a-bdcc-7aee9961550d"><br>

***

- Right-click: `mydomain.com`

- Click: `Referesh` to sort the new organizational units to the top

- Go to: the `_ADMINS` OU

- Right-click the name of the OU > New > User

  - First/Last name: `Jane Doe`

  - User login name:`jane_admin`

  - Select: `Next`

  - Create: `a password`

  - Uncheck: `all boxes`
 
  - Select: `Next`

  - Select: `Finish`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/7c06a548-92aa-483f-b9dd-5ed730206d33"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/6092adc5-b3f6-44cd-b5d6-30b5bbb8bcf1"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/7890d83f-e2b2-4b26-8bae-993bd70d342d"><br>

***

- Go to: the `_ADMINS` OU

- Right-click: `Jane Doe > select Properties`

  - Click the tab named: `"Member of" > select Add`

  - Type in the names of your domain administrators

  - Select: `"Check Names" > OK > Apply`

- Log out of: `DC-1 as "labuser"`

- Log back in as: `mydomain.com\jane_admin`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/ffa61100-9141-4b28-a26b-2e64e220a868"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/928096e4-951d-4ee5-a255-88e0d01b30c3"><br>
 
***

### 7. ) Join Client-1 to your domain (mydomain.com)

- Go back to: the `Azure portal`

- Navigate to: the `Client-1 Virtual Machine`

- On the left-hand side of the screen select: `Networking`

- Select: `the link next to the NIC > select DNS Server > Custom`

- Type in DC-1's private IP address

- Click: `Save`

- After it is done updating, select: `Restart`

- Select: `Yes`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/12bf2eb6-6225-48d2-b694-19dd5f7f9b52"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/d4e6163f-4144-43bc-aefe-a1d4f12e88e2"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/f7a0426a-c925-4c62-806a-0968dba915f8"><br>

***

- Log back into: `Client-1` using Microsoft Remote Desktop `as the original local admin (labuser)`

- Right-click: the `Start` menu

- Select: `System`

- On right-hand side of the screen, select: `Rename This PC (Advanced) > Change`

- Under 'Member of', select: `Domain`

- Type: `mydomain.com`

- Select: `OK`

  - Username: mydomain.com\jane_admin

  - Type in: *`password`*

  - Press: `OK`

- Restart the computer			

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/b4e6e062-ab06-46c5-92cc-8c619caf866b"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/61e0ef71-6a75-4f11-9841-510cc4c7c275"><br>

***

### 8. ) Setup Remote Desktop for non-administrative users on Client-1

- Log back into <ins>Client-1</ins> as user: `mydomain.com\jane_admin`

- Right-click: the `Start` menu

- Select: `System`

- On the right-hand side of the screen, select: `Remote Desktop`

- Under User Accounts, Click: `Select Users That Can Remotely Access This PC > Add`

- Type in: `the name of your domain users`

- Select: `Check Names > OK > OK`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/2564a83b-1be8-485b-af22-f5b26814086b"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/182150f1-f40d-43e7-90e2-9139f6ef15d9"><br>

***

### 9. ) Create as many additional users as you would like and attempt to log into Client-1 with one of the users' profiles

- Log back into: `DC-1 as jane_admin`

- Search: `Powershell_ise`

- Right-click: `Powershell_ise` and open it as an administrator

- At the top-left of the screen select: `New Script` and paste the contents of the following script into it
	- You can find the script [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/9bf54bf4-5d89-4abf-8d12-5c7c4adc5853"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/c5121196-448d-45cd-ac92-a9c37eb96ff6"><br>

***

- Click: `the green arrow button` near the top-middle of the screen

  - This will run the script

- Once the users have been created, go back to: `Active Directory Users and Computers > mydomain.com > _EMPLOYEES`

  - You will see all the accounts that were created

- You can now log into: `Client-1 - with one of the accounts that were created`

- Use the Password: `Password1`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/88f0f8eb-4bdc-4564-be94-4ebada27a2c9"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/8b1bc4f2-6771-4513-93a8-819d99d6bd02"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/c99eb48e-e21d-4c48-b7d0-55401a5f3788"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/af519bc2-7a69-4417-81f0-8d59dc3ca289"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/1d86bdfb-28d1-4205-a264-1b4a6fedfcc1"><br>

</details>

🎉Congratulations! You have implementated on-premises Active Directory and created users within an Azure virtual machine!🎉

☎️ For any questions, or just to connect, you can message me at: www.linkedin.com/in/vincentchachere
