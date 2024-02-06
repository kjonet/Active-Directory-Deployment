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
Now that the domain controller is set up, we'll need to set up the RAS/NAT on the domain controller. This will allow the client's PC to be on a private virtual network while still being able to access the public internet.

<b>What is a RAS?</B>
- A RAS stands for Remote Access Service and is a type of technology that enables users to connect to a network remotely so they can access resources.

<b>What is a NAT?</B>
- A NAT stands for Network Address Translation and allows for multiple devices within a private network to share a single IP address. NAT also works as a barrier between the internal and external network, allowing a sense of security. When an internal IP address gets translated into a new one, it allows the internal network to remain hidden from potential outside threats. It works as a mask in a sense.

To configure remote access, I went back to the Server Manager dashboard and selected <b>Add roles and features</b>. I selected my server and for <b>Server Roles</b>, I then selected <b>Remote Access</b>. On the next slide, select <b>Routing</b> and proceed with the rest of the installation. 

On the Server Manager dashboard, under <b>Tools</b>, I selected <b> Routing and Remote Access</b>. At the window, right-click on the <b>DC (local)</b> select <b>Configure and Enable</b>, and select the option that says <b>NAT</b>. 
Next, select the network adapter that displays our internet interface. 

# DHCP Configuration 

To set up the DHCP server, we'll need to go back to <b>Add roles and features</b>, select out server and <b>DHCP</b> under Server Roles, and finish up with the rest of the install.

<b>What is DHCP?</b>
- DHCP stands for Dynamic Host Configuring Protocol and it allows computers on the network to automatically receive an IP address.

For the devices on our network to get a DHCP, we'll need to set a scope. This is a range of IP addresses that will be assigned to the devices that join the domain. To get started, right-click on the IPv4 server and select <b>New Scope</b>. 
Name the scope, add the preferred IP addresses, add any exclusions if any, and set the lease duration. 

![img](https://i.imgur.com/tGwhYw3.png)


<b>What is a lease duration?</b> 

- A lease duration is the length of time a device is allowed to have an assigned IP address. This could range from a few hours to a few days. 

Next, over in <b>Configure DHCP Options</b> select, <b>Yes, I want to configure these options now </b>. Next, add the IP address that will act as a router for the client's PC. This will help forward traffic to the clients from the internet. 
I will add the IP address of the domain controller, 172.16.0.1. Next, add the domain controller as the parent domain, activate the scope, and finish up with the rest of the installation. 


Once finished, right-click the DHCP server and select <b>Authorize</b> to get the DHCP scope activated.

![img](https://i.imgur.com/PMRaox2.png)

# User Creation 

Now that the domain controller is up and running, let's add a user whose credentials we'll use for the client PC. To get started, open up <b>Active Directory Users and Groups </b>, create an <b>Organizational Unit</b>, and name it <b>"Users"</b>. Right-click on the <b>Users</b> folder and select <b>New</b> in a new window titled <b>New Object - User</b>. Put in the appropriate information, such as name and login. Set a password, review the credentials, and select <b>Finish</b>

![img](https://i.imgur.com/1iif7iH.png)
![img](https://i.imgur.com/2Nx8zyB.png)
![img](https://i.imgur.com/l7ierWt.png)
![img](https://i.imgur.com/gs5aCwl.png)

# Joining The Client PC To The Domain Controller 

Before joining the client PC to the domain, let's check to see if the network configures have been set properly. To test this, open up the command prompt on the client pc and type, <b>ipconfig</b>. The client PC should have an assigned IP and the default gateway should be the IP of the domain controller. 

![img](https://i.imgur.com/YyUUlPG.png)

Head over to <b>Settings</b> and then <b>About</b>. Scroll down to the option <b> Rename this PC (advanced)</b>. This is where we'll rename the PC and join it to the domain. 

![img](https://i.imgur.com/inlaiwM.png)

Enter administrator credentials and select ok. 

Congratulations! The PC client has now connected to the DC!

![img](https://i.imgur.com/aNDlG54.png)
