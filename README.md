<h1>Windows Domain Controller Server, Active Directory Domain Services (AD DS) Setup, & Group Policy Objects (GPO) Implementation</h1>

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
- Create & implement security rules in the domain controller to restrict client VM connections using GPO

<h2>Index</h2>

- [Downloads](#downloads)
- [Create & setup server VM (Domain Controller)](#create--setup-server-vm-domain-controller)
- [Create bulk users using Powershell script](#downloads)
- 
<h2>Downloads</h2>
Download virtualbox package and extension pack, windows server ISO (2019, 2022 etc.), client ISO.
</br>

[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads),</br>
[Windows Server 2022 | Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022),</br>
[Download Windows 11 (microsoft.com)](https://www.microsoft.com/en-ca/software-download/windows11)</br>

<h2>Create & setup server VM (Domain Controller)</h2>
After installing VirtualBox, add a new VM. To set it up as a DC, pick 'Other Windows (64-bit)' as OS version. When the VM is created, go to its settings, enable adapter2 as 'internal network', so that the client machines in the VirtualBox's internal network can use this NIC as a gateway later. Adapter1 will remain as 'NAT' to be able to communicate with the internet. The system specs can be 2GB RAM, 20GB storage, 2-core processor.</br>
Then, double-click on the VM. Pick the server ISO that was downloaded when prompted. Setup the server with 'server standard evaluation (desktop experience)' to utilize the desktop GUI. The other settings should be default.
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
