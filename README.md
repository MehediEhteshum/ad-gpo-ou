<h1>Windows Domain Controller Server, Active Directory Domain Services (AD DS) Setup, & Group Policy Objects (GPO) & Organizational Unit (OU) Implementation</h1>

<h2>Introduction</h2>
This project demonstrates many basic to advanced usages of domain controller, active directory (AD), Powershell scripting & group policy object (GPO).
</br></br>

`(Click the system architecture schematic below to see it on Figma)`

[![Schematic](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-19%20172551.png)](https://www.figma.com/file/rFRTPwspiE9JIS8MRxIHc1/Windows-Domain-Controller-(Active-Directory-DS)-Schematic?type=whiteboard&node-id=0%3A1&t=cPZLwiZedmpp0mul-1)

The features include -

- Create & setup server VM (domain controller) & client VM using VirtualBox
- Setup server & client VM NICs
- Setup server with AD DS, RAS / NAT, DHCP
- Create admin user
- Create bulk users using Powershell script
- Domain join the client VM
- Check connections from client VM
- Create & implement security rules in the domain controller to restrict client VM connections using GPO and Organizational Unit (OU)

<h2>Index</h2>

- [Downloads](#downloads)
- [Create & setup server VM (Domain Controller)](#create--setup-server-vm-domain-controller)
- [Create bulk users using Powershell script](#create-bulk-users-using-powershell-script)
- [Create & setup client VM](#create--setup-client-vm)
- [Restrict connections using GPO & OU](#restrict-connections-using-gpo--ou)
- [Support](#support)

<h2>Downloads</h2>
Download virtualbox package and extension pack, windows server ISO (2019, 2022 etc.), client ISO.
</br>

[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads),</br>
[Windows Server 2022 | Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022),</br>
[Download Windows 11 (microsoft.com)](https://www.microsoft.com/en-ca/software-download/windows11)</br>

<h2>Create & setup server VM (Domain Controller)</h2>
After installing VirtualBox, add a new VM. To set it up as a DC, pick 'Other Windows (64-bit)' as OS version. When the VM is created, go to its settings, enable adapter2 as 'internal network', so that the client machines in the VirtualBox's internal network can use this NIC as a gateway later. Adapter1 will remain as 'NAT' to be able to communicate with the internet. The system specs can be 2GB RAM, 20GB storage, 2-core processor.</br>
Then, double-click on the VM. Pick the server OS ISO, that was downloaded, when prompted. Setup the server with 'server standard evaluation (desktop experience)' to utilize the desktop GUI. The other settings should be default.
</br></br>

![DC VM](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-17%20235927.png)

Once the VM is started, go to 'network connections' and rename the 2 NICs appropriately for better identification as shown in the picture. Setup the internal NIC with IP address, subnet mask & DNS server IP (basically it is referring to its ownself i.e. loop-back address) as shown in the picture.</br>
Rename the PC as 'DC'. Next, we will setup the VM as a domain controller.
</br>

![NICs](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20154140.png)

Open server manager. Add a few server roles - `Active Directory Domain Services`, `DHCP Server`, `Remote Access` with 'routing' feature enabled.</br>
Once they are installed, start with 'AD DS' configuration. Click on the notification that says 'promote this server to a domain controller'. 
</br>

![Server roles](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20161028.png)

Now, 'AD DS configuration wizard' will pop up. Here, pick 'add a new forest' and enter your desired domain name e.g. `ad-lab.com`. Go ahead and install the AD DS server.</br>
If you want to log out and log back in to your server, you'll notice that you'll be log in to 'AD-LAB'.
</br>

![Domain setup](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20170637.png)

Now that your AD DS server is ready, open 'AD users and computers'. Right click on 'ad-lab.com', then 'New > Organizational Unit' to create a new OU.</br>
Right click on the OU and create a new user e.g. 'Mehedi Ehtehum'. This user is not an admin yet. Go to user 'properties > Member of', and add 'Domain Admins' group to the user.
Now the user will have the global admin privilege. You can now login to the server as this Admin via 'Other user' option on the login screen as shown in the image.
</br>

![Admin user](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20172316.png)
![Login as Admin](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20172757.png)

Next, let's setup RAS / NAT. Open 'routing and remote access'. Right-click on DC and 'configure & enable RAS'. Then, pick 'NAT' option to allow internal clients to connect to the internet using one public IP address that is of 'INTERNET' NIC. Then pick the 'INTERNET' NIC as the public interface to the internet.</br>

![RAS setup](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20173247.png)

Now, we will setup the DHCP server.</br>
Open 'DHCP'. Expand the server name, and add a 'New Scope' on IPv4. Enter the IP address range as shown in the architecture diagram. Add your 'INTERNAL' NIC IP as the router (default gateway). Then on the next page, DNS server will also be the same NIC. Finish the DHCP setup.</br>
Now, you will have a DHCP setup as shown in the picture. If you check the 'Address leases', the list is empty. Because we have not domain joined any client device yet.
</br>

![DHCP](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20173915.png)

CONGRATULATIONS! Your domain controller setup is now complete.</br>
Next we will create users in bulk using a Powershell script.

<h2>Create bulk users using Powershell script</h2>

Download the `CREATE_USERS.ps1` script. Open Powershell ISE 'as admin'. Run command `Set-ExecutionPolicy Unrestricted` for ease (not recommended for a production setup), cd into the script folder and run.</br>
If you now open 'AD users and computers', you'll see the users under the '_EMPLOYEES' OU.
</br>

![Bulk users](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20175949.png)

<h2>Create & setup client VM</h2>

Create the client VM similar to the server VM creation. Add a new VM. To set it up as a client, pick 'Windows 11 (64-bit)' as OS version. When the VM is created, go to its settings, change 'Adapter1' attachment to 'Internal Network' to not be able to communicate with the internet directly, but via DC 'INTERNAL' NIC. The system specs can be 4GB RAM, 80GB storage, 2-core processor.</br>
Then, double-click on the VM. Pick the client OS ISO, that was downloaded, when prompted. Setup the client with 'windows11 pro'. The other settings should be default.
</br>

![Client PC setup](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20190845.png)

Once your client PC is ready, open system properties and change its name to e.g. 'Client1' as well as, most importantly, domain join the PC to `ad-lab.com` as shown in the picture.</br>
Now you'll be able to login to the client PC as any of the AD users (as shown).</br>

![Domain join client](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20204835.png)
![Login to client as AD user](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20205458.png)

If you go back and check 'address leases' in 'DHCP' & 'computers' in 'AD DS users and computers', you'll see that this client PC is listed there now i.e. it has joined the domain.</br>
Open 'Command prompt'. Run `ipconfig`, you'll see the default gateway IP is that of DC 'INTERNAL' NIC. Ping any websites, it will be able to resolve i.e. the connection to the internet via DC NIC is working.
</br>

![Connection check](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20210023.png)

<h2>Restrict connections using GPO & OU</h2>

**Block certain websites**</br>
Open 'AD DS users and computers'. Create a new OU where you want to implement GPO e.g. '_GPOedCLIENT'. Move the client computer to that OU.</br>
Open 'group policy management'. Under your domain, create and link a GPO to the OU e.g. 'Block websites'. The goal of this GPO will be to restrict certain websites.
</br>

![GPO to block websites](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20223151.png)

Right click the GPO and edit. Under the `computer config > windows settings > security settings > windows defender firewall with advanced security`, add an outbound rule that blocks certain websites IP address that you specified in the rule.</br>
To enforce the GPO immediately, run command `gpupdate /force` in both server and client, or restart the client.</br>
Open a browser in the client. Check if the specified websites are blocked. For example, in the picture Facebook, TikTok, Instagram are not working as their IP addresses are blocked, but Google and Youtube are working.
</br>

![Outbound rule](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20223331.png)
![Blocked websites](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-18%20225135.png)

</br>

**Block all websites except specifics**</br>
Unlink the last GPO from the OU. Create and link a new GPO to the OU e.g. 'Block all websites except specifics'. </br>
The main difference with the previous one is that, under 'windows defender firewall with advanced security' properties, you need to turn on the firewall and specify 'block' for all inbound and outbound connections. Then, simply add an outbound rule that allows connections to specific websites such as google.com & openai.com.</br>
Enforce the GPO. Open a browser in the client and check the GPO working. Only google.com & openai.com are working.
</br>

![Firewall properties](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-19%20223510.png)
![Blocked all except specific](https://github.com/MehediEhteshum/adds-gpo/blob/main/screenshots/Screenshot%202023-09-19%20001443.png)

</br>

**Bonus Tip**</br>
If you want to restrict any website company wide, you can simply use its FQDN to block it.</br>
Open DNS manager in the server. Create a new DNS zone for 'forward looking zone' using the FQDN of the website. Then create a new host (A/AAAA) for this DNS zone with an IP address of 0.0.0.0 or 127.0.0.1.</br>
Now the client browser will resolve the website as localhost, so basically the website will not work anymore company wide.

<h2>Support</h2>

☕☕☕ If this project helps you understand the concepts around the topic, please consider buying me a coffee
<br>
☕☕☕
<a href="https://coindrop.to/mehedi_ehteshum" target="_blank"><img src="https://coindrop.to/embed-button.png" style="border-radius: 10px; height: 57px !important;width: 229px !important;" alt="Coindrop.to me"></img></a>
