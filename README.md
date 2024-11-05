<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# On-Premises Active Directory Deployed in the Cloud (Azure)
This tutorial outlines the implementation of on-premises Active Directory within two Azure virtual machines.<br/>


## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## Operating Systems Used

- Windows Server 2022
- Windows 10 (21H2)

## Deployment and Configuration Steps

### Step 1: Setup Resources in Azure

<ins>Create 2 virtual machines</ins>
- If you need help creating your virtual machines, please see my tutorial [here](https://github.com/roslyndwilliams/virtual-machine)

- The first virtual machine will be the Domain Controller

  - Name: `DC-1`

  - Image: `Windows Server 2022`

  - *Take note of the virtual network (VNET) that is automatically created*
 
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/a820996f-834e-4c6f-91c1-13f9624d6580"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/696ce759-a437-4bb2-91a6-4a54599e7671"><br>

*** 

- Set DC-1's Virtual Network Interface Card (vNIC) Private IP address to be static

  - Go to: `DC-1's network settings`

  - Select: `Networking`

  - Select: `the link next to Network Interface`
	   
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/b7e0618a-7a21-4ff1-96c3-a5700eb2eb5d"><br>

***

- Select: `IP Configurations`

- Select: `ipconfig1`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/5bacddf0-79dc-4dbd-a00d-7d49200ce547"><br>

***

- Change the assignment from dynamic to: `static` *(This ensures DC-1's IP address will not change)*

- Click: `Save`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/ea66a662-42a4-46b8-ab24-4c50a67c8dc9"><br>

***

- The second virtual machine will be the Clients

  - Name: `Client-1`

  - Image: `Windows 10 Pro`

  - *Use the same resource group and VNET as DC-1*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/2d3a2248-4b64-4bc0-bafa-234ba19b22d4"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/24782cca-2981-49fb-b0e1-72dfbe75f155"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/d3ad4161-60fe-4cc0-9196-ae40884d3f60"><br>

***

### Step 2: Ensure Connectivity Between the Client and Domain Controller

- Login to: `Client-1 using Microsoft Remote Desktop`

- Search: `Command Prompt` and open it

- Ping: `DC-1's private IP Address` (for example, 10.1.0.4)

  - Type: `ping -t 10.1.0.4` into the command-line interface

    - *The ping request continually  times out due to the firewall settings*

    - *To fix this, we need to enable ICMPv4 on DC-1's local Windows firewall*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/20931f76-739c-4511-b784-237e8ec83d56"><br>

***

- Login to DC-1 using Microsoft Remote Desktop

  - Go To: `Start > Windows Administrative Tools > Windows Defender Firewall with Advanced Security > Inbound Rules`

  - Sort the list by: `protocols`

  - Find and enable these two inbound rules: `Core Networking Diagnostics` and `ICMPv4`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/2c3a9298-cec6-4a55-9996-79d0f3861afa"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/d0f69592-b926-46bb-a1eb-38e4fbb36554"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/4a4a9db9-1d7c-4bb9-9f63-93e3c65c2a73"><br>

***

- Log back into: `Client-1` *(the command line will automatically begin pinging DC-1 successfully)*
    
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/d63d76b9-18a3-4360-8db8-e1ecb4b67aa2"><br>

***

### Step 3: Install Active Directory

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

***

- At the top right of the Server Manager Dashboard, click on: `the flag`

- Select: `Promote This Server to a Domain Controller`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/3b799874-428a-4358-9430-2a500d7f4544"><br>

***

- Select: `Add a New Forest`

- Root domain name: `mydomain.com`

- Select: `Next`

- Create: `a password`

- Select: `Next` and follow the prompts

- Select: `Install` to complete the installation

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/5a0f1ee2-fada-45da-877f-2c811b7a2dba"><br>

***

- *DC-1 will automatically restart*

- Log back into DC-1 as user: `mydomain.com\*usernameyoucreated*`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-on-prem-ad/assets/161680745/6d2b5906-21a0-420e-b40c-d6753b1ab97a"><br>

***

### Step 4: Create an Admin and Normal User Account in Active Directory v1.15.8
     
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

### Step 5: Join Client-1 to your domain (mydomain.com)

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

### Step 6: Setup Remote Desktop for non-administrative users on Client-1

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

### Step 7: Create as many additional users as you would like and attempt to log into Client-1 with one of the users' profiles

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

🎉Congratulations! You have implementated on-premises Active Directory and created users within an Azure virtual machine!🎉

☎️ For any questions, or just to connect, you can message me at: www.linkedin.com/in/vincentchachere
