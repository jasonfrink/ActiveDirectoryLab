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

![Routing and Remote Access](https://github.com/user-attachments/assets/6cd270b1-9d02-4f49-a547-a4e929428f43)

<b>Step 4. Set up DHCP</b>

This will allow automatic IP address assignment to local machines that join the network, allowing internet access.

Setting up DHCP using Server Manager. Tools menu --> select DHCP
![dhcp setup](https://github.com/user-attachments/assets/ae679e25-1c92-4747-a984-7f3ad0c300e6)

The scope of IP addresses that will be used is 172.16.0.100 to 172.16.0.200 with a /24 subnet mask. For the purposes of the lab, the lease time is left at 8 days.  

![image](https://github.com/user-attachments/assets/7fe820f8-aba2-4238-9ca7-6f9fe1527074)

The default gateway will be the IP address of the DC: 172.16.0.1.

<b>Step 5. Create Active Directory Users</b>

A PowerShell script was used to create test usernames. The script takes a list of randomly generated first and last names and creates a new username and password.

Credit to Josh Madakor for creating the script. 
![powershell script](https://github.com/user-attachments/assets/76f9adbc-a373-4fc7-93ef-5b0761f658c6)

Once the script is complete we can verify the users were added.
![AD Users](https://github.com/user-attachments/assets/17050a6f-4bb3-41c3-b248-151268df13d7)

<b>Step 6. Create Windows 10 client VM</b>

The VM set up process is the same as the server set up in step one, except using a Windows 10 ISO
![CLIENT1 VM SET-UP](https://github.com/user-attachments/assets/5d002ff6-ad39-488b-99cd-e581f945ca28)
![Installing Windows CLIENT1](https://github.com/user-attachments/assets/b52bf8cb-6237-4087-b96c-d28104d9cfec)

For the Network Adapter, the Internal Network will be selected. This will allow the client machine to be assigned an IP address from the DHCP server, giving access to the local network and internet. 
![DC Internal Network](https://github.com/user-attachments/assets/61c964f2-5a58-484e-9af8-2133984cc5e4)

<b>Step 7. Verify Client Machine Connection</b>
The last step will be to verify 1) we can log on to the network using one of the user id's created in Active Directory 2) the client machine has been assigned an IP address and can connect to the internet.

The client user login works and we can see there is a connection to the DC network (mydomain.com)
![CLIENT1 - Desktop](https://github.com/user-attachments/assets/a7779868-4792-4860-9415-3a41a1c2245b)

We can also open the terminal and use the ipconfig command. The output shows the client was assigned an IP address of 172.16.0.100 and the default gateway for the machine is the ip address of the DC.
![Client - ipconfig](https://github.com/user-attachments/assets/59785e42-5e32-4e09-9562-0f35b647246c)

To test the connection we the ping a couple of IP addresses. The first test is the DC IP address of 172.16.0.1. The second ping test is for a public IP address. 9.9.9.9. Both tests work the which confirms our connection is working as expected. 
![ping 9 9 9 9](https://github.com/user-attachments/assets/176e7031-e59e-4771-8f73-07e1aabcf119)

<b>Step 8. Add Client to domain</b>
Our final step will be to rename the computer to CLIENT1 and make it a member of mydomain.com. This will allow any user listed in Active Directory to log into the network with this machine.
![CLIENT1 rename](https://github.com/user-attachments/assets/6627a628-68fb-461e-9624-4971326b99f0)

<b>Step 9. Check DHCP/Active Directory settings on DC</b>

One last thing we can do is take a look at the DHCP reservations on the DC machine. We can see below the IP address assigned to client machine and the lease expiration on 11/9/24. 
![DHCP lease](https://github.com/user-attachments/assets/03132156-9bd7-4b5a-8075-e5607abb2af8)
![ping dc](https://github.com/user-attachments/assets/a0c7ca3e-bf2b-45b4-aedb-c7c73e5c9ed0)

Lastly, under the AD settings we see the CLIENT1 computer is part of the mydomain.com domain. 

<b>Final Observations</b>
Overall this lab is a great hands on way to get familiar with Windows server, IP addressing, setting up a small network, and using Active Directory.   

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
