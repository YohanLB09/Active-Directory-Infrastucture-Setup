<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory - Infrastructure Setup</h1>
In this guided lab, we will setup a domain environment in Azure by setting up a domain controller and a client machine which will be able to communicate to one another.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 Pro, version 22H2

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Create a Resource Group
- Step 2: Create a Virtual Network and Subnet 
- Step 3: Create a Domain controller VM
- Step 4: Set a static IP address for the Domain Controller VM
- Step 5: Disable the Domain Controller VM's Firewall
- Step 6: Create a Client VM
- Step 7: Configure the client VM's DNS settings
- Step 8: Restart the client
- Step 9: Ping the Domain Controller VM from the client VM
- Step 10: Verify the Client VM's IP configurations

<h2>Deployment and Configuration Steps</h2>

<h3>Step 1: Create a Resource Group</h3>

<p>
<img src="https://i.imgur.com/h3N5bbe.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Once logged in to your Azure account, browse to "Resource groups" from the navigation pane.

-Click on "Create".

-Make sure the correct Subscription is choosen.

-Enter a Resource group name.

-Choose the appropriate region depending on your needs

-Click on "Review + Create" to complete the configuration process.
</p>
<br />




<h3>Step 2: Create a Virtual Network and Subnet</h3>

<p>
<img src="https://i.imgur.com/KlWmQ69.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Browse to "Virtual Networks" from the navigation pane.

-Click on "Create".

-Select the previously created Resource group.

-Create a name for the Virtual Network.

-Choose the same Region as the Resouce group.

-Leave all other settings as default and click on "Review + Create".

The virtual network and subnet provide an isolated network space in Azure, enabling communication between hosts within the same logical network.
</p>
<br />




<h3>Step 3: Create a Domain controller VM</h3>

<p>
<img src="https://i.imgur.com/OdBIFfT.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Browse to "Virtual machines" from the navigation pane.

-Click on "Create".

-Select the previously created Resource Group.

-Create a name for the Domain controller VM.

-Pick the same Region as the Resource group and Virtual network.

-For the image, choose: "Windows Server 2022 Datacenter: Azure Edition - x64 Gen2".

-Select a VM size; having at least 2 vcpus and 8 GiB memory is recommended.

-Create a username and a password (write them down in Notepad for futur use).

-Leave the other options at their default configuration and move on to the Networking section.

-In the Networking section, select the previously created Virtual network.

-Leave all the other options at their default configuration and click on "Review + create".
</p>
<br />




<h3>Step 4: Set a static IP address for the Domain Controller VM</h3>

<p>
<img src="https://i.imgur.com/SczcnpO.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-From within the Domain controller VM's settings in Azure, select "Networking" -> "Network settings" -> click on the "link" under Network interface / IP configuration to access the NIC settings -> "ipconfig1" -> Next to Allocation, select "Static" ->  "Save".

-Additionally, take note of the Domain Controller VM's private IP address, as we will need it for later (use Notepad).

We set a static IP address for the Domain Controller VM because the client VM needs a reliable and unchanging address to consistently locate the domain services, especially for DNS resolution which is crucial for joining the domain.
</p>
<br />




<h3>Step 5: Disable the Domain Controller VM's Firewall</h3>

<p>
<img src="https://i.imgur.com/tR8CUix.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Connect to the domain controller VM using its public IP address via Remote Desktop Connection.
</p>
<br />


<p>
<img src="https://i.imgur.com/atfLXeH.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Once logged in, use the "Windows key" + "R", then type "wf.msc" (shortcut to access the Windows Defender firewall).

-From there, disable the firewall (all profiles).
This is not best practice, it's only for the purpose of this lab, for testing purposes (tbc).
</p>
<br />




<h3>Step 6: Create a Client VM</h3>

<p>
<img src="https://i.imgur.com/eARDjKc.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Back in Azure, browse to "Virtual machines" from the navigation pane.

-Click on "Create".

-Select the previously created Resource Group.

-Create a name for the client VM.

-Pick the same Region as the Resource group and Virtual network.

-For the image, choose: "Windows 10 Pro version 22H2".

-Select a VM size; having at least 2 vcpus and 8 GiB memory is also recommended for this VM.

-Create a username and a password for the Windows VM (write them down in Notepad for futur use).

-Leave the other options at their default configuration and move on to the Networking section.

-In the Networking section, select the previously created Virtual network.

-Leave all the other options at their default configuration and click on "Review + create".
</p>
<br />




<h3>Step 7: Configure the client VM's DNS settings</h3>

<p>
<img src="https://i.imgur.com/fOuZjAp.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-From within the client VM settings, under Networking, navigate to "Network settings" -> click on the "link" under Network interface / IP configuration to access the NIC settings -> click on "DNS servers" on the left -> under DNS servers, select "Custom" -> paste the Domain Controller VM private IP address -> click on "Save".

Configuring the client VM's DNS settings with the domain controller's private IP address directs the client to the domain controller for name resolution, which is essential for joining the domain and accessing domain resources.
</p>
<br />




<h3>Step 8: Restart the client</h3>

<p>
<img src="https://i.imgur.com/Wx46q4S.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Restart the Client VM
  
Restarting the client VM ensures it immediately clears its existing DNS resolver cache and requests a new DHCP lease. This new lease will then provide and consistently apply the domain controller's IP address as the primary DNS server for all subsequent name resolution requests
</p>
<br />




<h3>Step 9:Ping the Domain Controller VM from the client VM</h3>

<p>
<img src="https://i.imgur.com/OKrz1Ub.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-Login to the Client VM, navigate to PowerShell and ping the Domain Controller VM using its private IP address; "ping 10.0.0.4" (yours may be different). 

This is to ensure that the client VM can effectively communicate with the Domain Controller which is on the same Virtual Network. If your are not able to successfully ping the Domain controller VM from the Client VM, ensure the Domain Controller VM's firewall isn't blocking incoming ICMP requests and verify both VMs are connected to the same Virtual Network.
</p>
<br />




<h3>Step 10: Verify the Client VM's IP configurations</h3>

<p>
<img src="https://i.imgur.com/mwX9oHi.png" height="100%" width="100%" alt="Configuration step"/>
</p>
<p>
-From within PowerShell, use the "ipconfig /all" command to ensure that the client VM's DNS Server is configured with the Domain Controller VM's private IP address.
</p>
<br />




<h2>Active Directory Infrastructure Setup Completed!</h2>

<b>We've successfully deployed a domain environment, establishing a functional domain controller and a client machine configured to communicate within the same Azure virtual network. This foundational setup provides the necessary groundwork for exploring more advanced domain-related tasks in subsequent labs. Remember to stop the VMs in the Azure Portal when not in use to manage costs effectively.</b>
<br />
<br />
</p>

