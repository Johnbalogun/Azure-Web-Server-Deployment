<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
   
</head>
<body>

<h1>Azure VM Server, Manual Deployment Project</h1>

<img src="https://i.imgur.com/Jb075xF.png" alt="Step 1 Image">

<h2>Objective</h2>
<p>The objective of this project is to manually set up a virtual machine and deploy a web server in Azure networks, while adhering to security best practices.</p>

<h2>Tools</h2>
<ul>
    <li>Azure Virtual Network</li>
    <li>Azure Subnet</li>
    <li>Azure Network Security Group</li>
    <li>Ubuntu 20.04 LTS Virtual Machine</li>
    <li>whatsmyip.com</li>
</ul>

<h2>Step 1</h2>
<p>Firstly, a resource group was created. For this lab, we'll use the East US region and name the resource group.</p>
<img src="https://i.imgur.com/nN986Ff.jpeg" alt="Step 1 Image">

<h2>Step 2</h2>
<p>Next step is to create a virtual network within the resource group in the same "East US" region. Navigated to the IP address column to create an IP address for the VN. Deleted the default address already there and adding “172.10.0.0/16” as the chosen IP address for the VN.</p>
<img src="https://i.imgur.com/sNQuPBs.png" alt="Step 2 Image">

<h2>Step 3</h2>
<p>A subnet was then added to the VN and Named “SNET-USE-Nextcloud”, with an IP address range set at 172.10.0.0/24.</p>
<img src="https://i.imgur.com/Tj3r9nF.jpeg" alt="Step 3 Image 1">
<!-- Image 2 Missing -->

<h2>Step 4</h2>
<p>Next, an NSG was created to help filter traffic to and from the virtual network by allowing us to set inbound and outbound rules to the Azure resources within the virtual network and also the internet.</p>
<img src="https://i.imgur.com/Fru8AmV.jpeg" alt="Step 4 Image 1">
<img src="https://i.imgur.com/O97LOGh.jpeg" alt="Step 4 Image 2">
<img src="https://i.imgur.com/They0mF.jpeg" alt="Step 4 Image 3">

<p>The rules above at the moment are only permitting traffic to and from Azure load balancer and the VNET, every other traffic is denied, this is okay at this point of the lab.</p>

<h2>Step 5</h2>
<img src="https://i.imgur.com/LwoTqLO.jpeg" alt="Step 5 Image">

<p>The subnet was then connected to the NSG in order for it to be protected by the same inbound/outbound rules.</p>

<h2>Step 6</h2>
<img src="https://i.imgur.com/j8ot6qz.jpeg" alt="Step 6 Image">
<img src="https://i.imgur.com/e3nwTlb.jpeg" alt="Step 6 Image 1">

<h2>Step 7</h2>
<p>It's time to create the virtual machine. Selected the Ubuntu Server 18.04LTS from Azure marketplace, named it as per usual, left the availability zone and option as default. Chose a B1s size machine. Left SSH public key authentication selected and created a username. Selected none for public inbound ports and in the networking section none for public IP as that will be allocated manually down the line.</p>
<img src="https://i.imgur.com/aojQyvr.jpeg" alt="Step 7 Image 2">
<img src="https://i.imgur.com/BFrRwNG.jpeg" alt="Step 7 Image 3">

<p>No network security group selected either as the connected subnet is already linked to one.</p>

<p>Once it's been created Azure will prompt to download the private SSH key that will be used to log into the VM using Azure Bastion.</p>
<img src="https://i.imgur.com/r34maDx.jpeg" alt="Step 8 Image 1">

<h2>Step 8</h2>
<p>To connect to the VM, Bastion was selected, configured the authentication credentials by entering the username previously set up and select SSH Private Key from Local File. Uploaded the SSH local file previously downloaded and connected.</p>
<img src="https://i.imgur.com/mnZlMSM.jpeg" alt="Step 8 Image 2">

<p>Once connected, Nextcloud server was installed by using `sudo snap install nextcloud`.</p>
<img src="https://i.imgur.com/HnwgAJN.jpeg" alt="Step 8 Image">

<h2>Step 9</h2>
<p>Nextcloud was installed so I created an admin account on the VM by typing `sudo nextcloud.manual-install *username* *password*`.</p>
<p>Next step is to create a self-signed certificate by typing `sudo nextcloud.enable-https self-signed`. Once the certificate was established, I exited the SSH connection by simply typing `exit`.</p>

<p>In order to see the Nextcloud working finally we would need to set up a public IP for https connections. I navigated back to the networking tab in Azure to configure IP in the network interface that was automatically created for the VM by Azure.</p>
<img src="https://i.imgur.com/tTrB3qn.jpeg" alt="Step 9 Image 2">

<p>Navigated to the NSG to change inbound rule to allow https traffic to the Nextcloud server, but only from my current IP. I found my current IP through “whatsmyip.com”.</p>
<img src="https://i.imgur.com/qZ4AIDk.jpeg" alt="Step 9 Image">

<h2>Step 10</h2>
<p>Entered the found IP address into the source column in the inbound rules section and the destination was the IP for the VNET 172.10.0.4 and kept service as https. Allowed action and named the rule.</p>

<p>Copied and pasted the new public IP into a browser to access the server.</p>
<img src="https://i.imgur.com/nDS5xBK.jpeg" alt="Step 10 Image 2">

<p>Side note (A page stating access through untrusted domain might appear in that case navigate back to the network interface controller (which should be connected to the public IP) to configure a custom DNS for the public IP.)</p>

<p>Once this is done you will have to communicate the new DNS label to Nextcloud, by connecting through SSH and typing `sudo nextcloud.occ config:system:set trusted_domains 1 –value=*new dns label*`</p>

<p>Type in the new DNS label in your browser and Nextcloud should be up and running.</p>

</body>
</html>

