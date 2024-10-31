<h1>Active Directory Home Lab</h1>

<h2>Description</h2>

This project guides users through setting up a VirtualBox home lab with Windows Server 2019, configured to run Active Directory (AD) and related network services. It includes installing VirtualBox, configuring network adapters, and setting up a Windows Server with AD Domain Services, DNS, and DHCP. The lab also covers creating a Windows 10 client and automating the bulk creation of AD users using PowerShell. This setup provides a hands-on environment to explore AD management, networking basics, and client-server interactions.
<br>
<br>
Credit to Josh Madakor for creating the lab [tutorial](https://youtu.be/MHsI8hJmggI?si=2hyOxmObgueOZa8P) on YouTube


<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)
- <b>Windows Server 2019</b>
- <b>Oracle VM VirtualBox</b>

<h2>Lab walk-through:</h2>

<b>Step 1. Set up Environments</b>

Set up Windows Server 2019 on Oracle VirtualBox using a Server 2018 ISO file.

This machine will be the Domain Controller (DC) which will house Active Directory. The DC will have two network adapters: 

1) connection to the outside internet
2) internal connection client machines will use

![DC NAT](https://github.com/user-attachments/assets/1bb9e65d-4c8f-41d4-9c59-60b61eb36767)

![DC Internal Network](https://github.com/user-attachments/assets/3c2b01d3-daf3-46c7-a9a7-0432e56fb612)

The DC will be assigned a private IP address from the home network to access the intenet. We will need to assign IP addressing for the internal network.

![Network Connections](https://github.com/user-attachments/assets/7c2b7dea-5bed-437b-9ccc-6681235f9996)

Once the VM for Sever 2019 is set up, the first step will be setting up IP addressing. In the following image you can see the DC has been assigned an IP address from the home network which is how the machine can access the internet. 

![DC Connection Details](https://github.com/user-attachments/assets/7bc3aa80-1382-4fcd-a3c6-a792495226e1)

The internal network does not have an IP address assigned to the internal NIC, which will be set up under the TCP/IPv4 properties. A default gateway is not needed here because the "external" NIC will serve as the default gateway.

![Internal Network IP set up](https://github.com/user-attachments/assets/e26b95d3-6b0a-4af1-9eec-68dd5334ba55)

When we use Active Directory, DNS is automatically installed on the server. So we can either use the loopback address of 127.0.0.1 for the DNS Server Address or the IP address of the DC internal NIC which was just assigned above as 172.16.0.1. 

The internal NIC will be given a class B private IP address of 172.16.0.1 with a /16 subnet mask.

<b>Step 2. Install Active Directory Domain Services</b>

AD is installed using Server Manager/Add roles and features. The Root domain name is: mydomain.com

![ADAS Install](https://github.com/user-attachments/assets/0e1abf11-977f-4b03-98b1-4ed9f7206d09)

<b>Step 3. Set up Remote Access Server / Network Access Translation (RAS/NAT)</b>

This will allow our Windows 10 clients on the internal network to access the internet through the DC.

Set up RAS through the Server Manager/Add roles and features. Then set up NAT using the 'Tools' menu and selecting Routing and Remote Access.

![Install RAS](https://github.com/user-attachments/assets/11b0626f-8376-4879-8554-32566f0d0ac6)

RAS/NAT set up is completed.

<img src="https://imgur.com/a/92RbOTf" height="80%" width="80%" alt="Routing and Remote Access"/>

<b>Step 4. Set up DHCP</b>

This will allow IP address assignment to local machines that join the network, allowing internet access.



<p align="center">
Launch the utility: <br/>
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
