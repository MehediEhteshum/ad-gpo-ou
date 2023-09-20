<h1>Windows Domain Controller Server, Active Directory Domain Services (AD DS) Setup, & Group Policy Objects (GPO) Implementation</h1>

<h2>Introduction</h2>
This project demonstrates many basic to advanced usages of domain controller, active directory (AD), Powershell scripting & group policy object (GPO).
</br></br>

`(Click the schematic diagram to see it on Figma)`

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
