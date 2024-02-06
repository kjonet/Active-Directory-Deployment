![img](https://i.imgur.com/DgBauZZ.png)


# Active Directory Setup & Deployment

<h2>Objective</h2>
This lab will document the setup and deployment process of Windows Active Directory using Server Manger 2019. The purpose of this lab is to experience what the process might be for setting up a domain controller in a professional environment. 
By the end of this lab I'll have a better understanding of the following:
<br>

- Domain Controllers
- Active Directory Users and Groups 
- Network Adapters 
- Assigning IP Addressing 
- NAT and DHCP configuration

<h2>Tools and Service Used</h2>

  - Virtual Box
  - Windows Server 2019
  - Windows 10 Pro
 
 # Network Topology

We'll be creating two virtual machines with the help of Virtual Box. One VM will act as our domain controller (DC) and the other will act as our client computer. The DC will be what houses the Active Directory(AD). For our DC, we're going to set up two network interface cards. One car will be used to connect to the public internet, while the other will be used for users to connect to the internal network (private network). For the client VM to connect to the internal network, we will need to assign the DC a static IP address. We won't need to worry about setting up IP addresses for the network interface that connects to the public internet, that IP address is already NAT'ed thanks to my home router. After defining a static IP address, we'll need to establish RAS/NAT and a DHCP server on the DC so our client can connect to the DC.

 # Setting Up The Proper Network Configurations With Virtual Box

When setting up the virtual machine for my domain controller,  I needed to make sure that my network settings had two network adapters established. One NAT, so the DC could reach the public internet, and one for the internal network. 

![img](https://i.imgur.com/0GD0N52.png) ![img](https://i.imgur.com/MefqxcA.png)

 # Setting Up A Static IP For the Domain Controller 

 To set up my domain controller's static IP, I went to the following path: <b>Control Panel\Network and Internet\Network Connections</b>. I selected the internal network adapter that I had set up in Virtual Box and gave it a static IP of 172.16.0.1 and a subnet mask of 255.255.255.0. I refrained from setting any values for the default gateway because the DC alone is already considered a default gateway. As for the DNS server options, when installing Active Directory, a DNS server is installed by default. So I have the option to either use the same IP address I assigned or a loopback address, 127.0.0.1.

 ![img](https://i.imgur.com/6nRFLhM.png)

<b> Why are we setting up a static IP address for the domain controller?</b>
- In the real world, assigning a static IP address to a domain controller is considered a best practice. A static IP address ensures stability, as it does not change, which is crucial when other devices are joined to the domain controller. This consistency facilitates easy joining of devices to the domain and helps alleviate IP conflicts. Moreover, having a fixed address mitigates the risk of devices joining malicious domains unintentionally.

# Installing Active Directory and Domain Creation

Now that the IP address has been set, it's time to install Active Directory and create our domain. I opened up Server Manager and selected <b>Add roles and features</b>. This will bring up a separate window. Next, under <b>Server Selection</b> I will select the server I want to install. Under <b>Server Roles</b> I selected <b>Active Directory Domain Services</b> and proceeded with the rest of the installation. What this essentially does is install the software for Active Directory, however, the domain still needs to be created. 

Next, I'll select <b>"Prompt this server to a domain controller"</b>. A new window will pop up. Under <b>Deployment Configuration </b> I'll select <b>Add a new forest</b> and type in the name of my domain. For this lab, I'll make it Harvestco.com (named after my cat <3). I proceeded with the rest of the configurations and installed them. 

# DNS Configuration 

