# Azure VM Server Deployment Project


![Step 1 Image](https://i.imgur.com/Jb075xF.png)

## Objective

The objective of this project is to manually set up a virtual machine and deploy a web server in Azure networks, while adhering to security best practices.

## Tools

- Azure Virtual Network
- Azure Subnet
- Azure Network Security Group
- Ubuntu 20.04 LTS Virtual Machine
- whatsmyip.com

## Step 1

Firstly, a resource group was created. For this lab, we'll use the East US region and name the resource group.

[![Step 1 Image](https://i.imgur.com/nN986Ff.jpeg)]()

## Step 2

Next step is to create a virtual network within the resource group in the same "East US" region. Navigated to the IP address column to create an IP address for the VN. Deleted the default address already there and adding “172.10.0.0/16” as the chosen IP address for the VN.

[![Step 2 Image](https://i.imgur.com/sNQuPBs.png)]()

Navigated to the IP address column to create an IP address for the VN. Deleted the default address already there and adding “172.10.0.0/16” as the chosen IP address for the VN.

## Step 3

A subnet was then added to the VN and Named “SNET-USE-Nextcloud”, with an IP address range set at 172.10.0.0/24.

[![Step 3 Image 1](https://i.imgur.com/Tj3r9nF.jpeg)]()
[![Step 3 Image 2]()]()

## Step 4

Next, an NSG was created to help filter traffic to and from the virtual network by allowing us to set inbound and outbound rules to the Azure resources within the virtual network and also the internet.

[![Step 4 Image 1](https://i.imgur.com/Fru8AmV.jpeg)]()
[![Step 4 Image 2](https://i.imgur.com/O97LOGh.jpeg)]()

As expected, the NSG already has some default inbound and outbound rules preset.

[![Step 4 Image 3](https://i.imgur.com/They0mF.jpeg)]()

The rules above at the moment are only permitting traffic to and from Azure load balancer and the VNET, every other traffic is denied, this is okay at this point of the lab.
## Step 5

[![Step 5 Image](https://i.imgur.com/LwoTqLO.jpeg)]()

The subnet was then connected to the NSG in order for it to be protected by the same inbound/outbound rules.

## Step 6

A Bastion instance needs to be created in order to connect to the virtual machine/server through a secure private SSH key. But before that a separate subnet was created for Azure Bastion named “AzureBastionSubnet”.

[![Step 6 Image](https://i.imgur.com/j8ot6qz.jpeg)]()

Once that was done the next step was to create the Azure Bastion resource. Named the resource and created region and attached it to the resource group. Linked it to the subnet created prior. (AzureBastionSubnet 172.10.1.0/24) and create public IP address name (BASTIONIP-USE-Nextcloud).

[![Step 6 Image 1](https://i.imgur.com/e3nwTlb.jpeg)]()

## Step 7

It's time to create the virtual machine. Selected the Ubuntu Server 18.04LTS from Azure marketplace, named it as per usual, left the availability zone and option as default. Chose a B1s size machine. Left SSH public key authentication selected and created a username. Selected none for public inbound ports and in the networking section none for public IP as that will be allocated manually down the line.

[![Step 7 Image 2](https://i.imgur.com/aojQyvr.jpeg)]()
[![Step 7 Image 3](https://i.imgur.com/BFrRwNG.jpeg)]()

No network security group selected either as the connected subnet is already linked to one.

Once it's been created Azure will prompt to download the private SSH key that will be used to log into the VM using Azure Bastion.

[![Step 8 Image 1](https://i.imgur.com/r34maDx.jpeg)]()

## Step 8

To connect to the VM, Bastion was selected, configured the authentication credentials by entering the username previously set up and select SSH Private Key from Local File. Uploaded the SSH local file previously downloaded and connected.


[![Step 8 Image 2](https://i.imgur.com/mnZlMSM.jpeg)]()

Once connected, Nextcloud server was installed by using `sudo snap install nextcloud`.
[![Step 8 Image](https://i.imgur.com/HnwgAJN.jpeg)]()

## Step 9

Nextcloud was installed so I created an admin account on the VM by typing `sudo nextcloud.manual-install *username* *password*`.

Next step is to create a self-signed certificate by typing `sudo nextcloud.enable-https self-signed`. Once the certificate was established, I exited the SSH connection by simply typing `exit`.

In order to see the Nextcloud working finally we would need to set up a public IP for https connections. I navigated back to the networking tab in Azure to configure IP in the network interface that was automatically created for the VM by Azure.



Changed the settings in ipconfig1 to create a new public address. Named it and saved the configuration and Azure generated the new IP address.
[![Step 9 Image 2](https://i.imgur.com/tTrB3qn.jpeg)]()
Navigated to the NSG to change inbound rule to allow https traffic to the Nextcloud server, but only from my current IP. I found my current IP through “whatsmyip.com”.
[![Step 9 Image](https://i.imgur.com/qZ4AIDk.jpeg)]()


## Step 10

Entered the found IP address into the source column in the inbound rules section and the destination was the IP for the VNET 172.10.0.4 and kept service as https. Allowed action and named the rule.



Copied and pasted the new public IP into a browser to access the server.

[![Step 10 Image 2](https://i.imgur.com/nDS5xBK.jpeg)]()

Side note (A page stating access through untrusted domain might appear in that case navigate back to the network interface controller (which should be connected to the public IP) to configure a custom DNS for the public IP.)

Once this is done you will have to communicate the new DNS label to Nextcloud, by connecting through SSH and typing `sudo nextcloud.occ config:system:set trusted_domains 1 –value=*new dns label*`

Type in the new DNS label in your browser and Nextcloud should be up and running.

