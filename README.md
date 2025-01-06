<br>

<p align="center">
<img width="700" src="https://github.com/user-attachments/assets/9b6b0a51-6411-4e01-96c5-1bb31e6fd986" alt="Microsoft Active Directory Logo"/>
<br>

<br>

<br>

<h1 align="center">Building Active Directory Infrastructure: Setup for Active Directory and Network Traffic Analysis Between Azure VMs</h1>

This is the first project in the [comprehensive series of tutorials](https://github.com/vincentchachere/Active-Directory-Deployment-and-Configuration) on Azure and Active Directory implementation. In this initial project, we'll establish the foundational setup for subsequent tutorials.

The primary goal of this project is to create a basic lab environment in Azure that simulates the typical enterprise deployment of Active Directory. The completion of this lab establishes the essential infrastructure to explore Active Directory functionality in an Azure-based network, preparing for more advanced concepts in future tutorials.

<p align="center">
<img src="https://github.com/user-attachments/assets/ef89ffa6-e50e-4996-bca1-85925307f212" height="80%" width="80%" alt="9"/><br />

## On-Premises Active Directory Deployed in the Cloud (Azure)
Active Directory essentially manages user accounts, passwords, permissions, and devices at large scale. This tutorial explains how to implement on-premises Active Directory in Azure Virtual Machines.

<ins>The difference between On-Premise Active Directory and Azure Active Directory</ins>:

- `On-Premise Active Directory`: Refers to infrastructure hosted and managed locally within an organization's physical data centers. This requires direct management and maintenance by the organization.

- `Azure Active Directory`: Refers to infrastructure and services provided remotely by Microsoft, hosted on their global data centers. This offers scalable, managed cloud services with remote access.

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Windows Defender Firewall
- Active Directory Domain Services
- PowerShell

## Operating Systems Used

- Windows Server 2022
- Windows 10 (21H2)

## High-Level Active Directory Infrastructure Steps

- Create Domain Controller (DC-1)
- Set Domain Controller's (DC-1) NIC Private IP Address to be Static
- Create Client-1 VM
- Test Connectivity Between the Client-1 and Domain Controller (DC-1)

<details>

<summary>

## ⚙️ Part 1: Create Domain Controller (DC-1)

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

</details>

<details>

<summary>

## ⚙️ Part 2: Set DC-1's NIC Private IP Address to be Static

</summary>

### 2. ) Set DC-1's NIC Private IP Address to be Static

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

</details>

<details>

<summary>

## ⚙️ Part 3: Create Client-1 VM

</summary>

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

</details>

<details>

<summary>

## ⚙️ Part 4: Test Connectivity Between Client-1 and DC-1

</summary>

### 4.) Test Connectivity Between Client-1 and DC-1

- Remote Desktop (RDP) into: `Client-1` (labuser)

- Once Inside Client-1 Open: `PowerShell`

- Perpetual Ping: `DC-1's private IP Address`

  - Type In: `ping 10.1.0.4 -t`

*The ping request continually times out due to the firewall settings.*

*To fix this, we need to enable the two ICMPv4 inbound rules on DC-1's local Windows firewall.*

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/9006e012-66c0-4e64-9ce7-0554e78a861d">

<br>
<br>
<br>

<ins>Test Connectivity Between Client-1 and the DC-1</ins>:

- Remote Desktop (RDP) into: `DC-1`

- Search: `wf.msc` (Windows Firewall - Microsoft)

*Look for the rules with Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)*

- Enable: `Both ICMPv4 Inbound Rules` (There are <ins>two</ins> you need to enable)

*They should have two green check marks next to them when you enable them just like the others.*

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/3b0ec287-a457-4508-a08c-fb6c0c3b1c39">

<br>
<br>
<br>

<ins>Test Connectivity Between Client-1 and the DC-1</ins>:

- Log back into: `Client-1`

*Notice the command line will automatically begin pinging DC-1 successfully*

<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/58978843-73c7-47b5-a4d9-d4ff062487fc">

</details>

<h2 align="center">Final Thoughts</h2> 

In this lab, we deployed On-premises Active Directory in the Cloud using Azure Virtual Machines. This foundational setup, including the creation of a Domain Controller (DC-1) and Client-1 VM with tested connectivity, establishing the basic infrastructure for simulating enterprise-level Active Directory in Azure.

To continue building on this lab, check out the [next tutorial](https://github.com/vincentchachere/Active-Directory-Deployment-and-Configuration) within this series, where we’ll dive deeper into deploying, configuring, and expanding Active Directory functionality.

Thank you for following along with this project. Your time and effort in learning and implementing these concepts are greatly appreciated.

☎️ For questions or to connect you can reach me at: www.linkedin.com/in/vincentchachere
