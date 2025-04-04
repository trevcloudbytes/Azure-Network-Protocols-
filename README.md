<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDP, DNS, DHCP, ICMP)
- Wireshark (Protocol Analyzer)
- Powershell

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Step 1: Setting Up
- Step 2: Observe DHCP Traffic
- Step 3: Configure Firewall 
- Step 4: Observe SSH Traffic
- Step 5: Observe DNS Traffic
- Step 6: Observe RDP Traffic 


<h2>Actions and Observations</h2>

<p>
Step 1: Setting Up <br />
(Creating our Virtual Machines)
  
https://portal.azure.com/

- 1. Create a Resource Group.
- 2. Create a Windows 10 Virtual Machine (VM).
- a. While creating the VM, select the previously created Resource Group.
- b. While creating the VM, allow it to create a new Virtual Network (Vnet) and Subnet.
- 3. Create a Linux (Ubuntu) VM
- a. While creating the VM, select the previously created Resource Group and Virtual Network—the Virtual Network MUST BE THE SAME.
- b. Authentication type: Username/Password
- 4. Ensure both VMs are in the same Virtual Network / Subnet

</p>

<p>
This is the Linux VM:
<br />
<br />

<img src="https://i.imgur.com/hpFtGLV.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>



</p>
<p>

</p>
<br />

<p>
This is the Windows VM:
<p>
<img src="https://i.imgur.com/KhXYMkL.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>


</p>
<br />

<p>
<img src="https://i.imgur.com/zSvufXL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now that you have your VM's created. Take the public IP of the Windows VM and remote into the machine using RDC (remote desktop connection.) 
  - Ensure you use the username and password that you made as you created the VM.
</p>
<br />

<p>
Once logged into the Windows VM, download x64 Wireshark Installer from your web browser. Go through the processes of 'next' and 'finish' button clicks. 
</p>
<br />

<p>
<img src="https://i.imgur.com/RWQ4Xk9.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Once Wirshark is booted up, go ahead and boot up Powershell as well. <br /> 
Wireshark will be used to trace the traffic between the two VM's while Powershell is used to run the networking commands.
<br />
On Wireshark, click on the capture that has data going through it. (You should see a stream of triangles, like an EKG heart reading moniter)
It'll most likely say "Ethernet".

</p>
<br /> 

<p>
  Step 2: ICMP Traffic
  <p>
    <img src="https://i.imgur.com/R4tFQuT.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  </p>
</p>
<p>
In this example, I am showing ICMP traffic using the ping command in powershell to show an echo and request communication between the two VMs.  
<br /> 
Back on Azure Portal, retreive the private IP of the Linux VM. This is the IP you will ping. 

- On Wireshark, filter for 'icmp' traffic. Start a new capture on wireshark.
- On Powershell, type ping(private ip) and enter.
  <br />

  What you will notice is ping will send out 4 echos and should recieve 4 replies. This shows there is a stable connection between the two VMs and no loss of data packets.

  
</p>
<br /> 
Step 2.1 Configuring Firewall 

 <p>
<img src="https://i.imgur.com/BQYT1Er.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>
   
<p>
  <br />
</p>


<img src="https://i.imgur.com/1qe00Ok.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- 1. Initiate a perpetual/non-stop ping from your Windows 10 VM to your Ubuntu VM. command ping(private ip -t)
     
- a. Open the Network Security Group your Ubuntu VM is using and disable incoming (inbound) ICMP traffic
 
- b. Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity

- c. Re-enable ICMP traffic for the Network Security Group your Ubuntu VM is

- d. Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity (should start working)
 
- e. Stop the ping activity by entgering 'exit' on your terminal (powershell)


What you will notice is once you enable the inbound rule, the ping will start to time out. When you delete the inbound rule, the connection is restored.

</p>
<p>

<br /> 



<p>
  Step 3. Observe SSH Traffic 
  
<img src="https://i.imgur.com/00QsD2E.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
  <img src="https://i.imgur.com/2ZgnnUZ.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
</p>
<p>
  
- 1.Filter for SSH traffic only on wireshark
  
- 2.From your Windows 10 VM, “SSH into” your Ubuntu Virtual Machine (via its private IP address)

- 3.Open PowerShell, and type: ssh labuser@(privat IP address)

- a.Type commands (username, pwd, etc) into the linux SSH connection and observe SSH traffic spam in WireShark
  
- b.Exit the SSH connection by typing ‘exit’ and pressing [Enter]

</p>

<p>
Step 4. Observe DHCP Traffic 

<img src="https://i.imgur.com/7KsAj9T.png)" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- 1.From your Windows 10 VM, attempt to issue your VM a new IP address from the command line
  
- 2.Create a script to run in powershell. The script will be used to release the current IP address of the Windows VM then renew with another IP address.

- a.Open notepad and type

"ipconfig /release" <br />
"ipconfig /renew"

- b. Click 'File' and save to ' c: /programdata ', save as "DHCP.bat" and file type 'all'
  
- 3.In Wireshark, filter for udp.port 67 & 68

- 4.In powershell change directory. 'cd c: \programdata'
- a. type 'ls' to show the list of programdata
- b. You should see 'DHCP.bat' if you saved the script correctly.
- c. Next, type '.\DHCP.bat'
- d. Observe the DHCP traffic on Wireshark 

</p>

<p>
  Step 5. Observe DNS Traffic
  
<img src="https://i.imgur.com/rubEj0o.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- 1. Open Wireshark and filter for dns traffic
- 2. Open Powershell
- a. type the command 'nslookup' which is used to resolve domain names to IP addresses and visa versa.
- b. 'nslookup www.google.com'
- 3. Observe the Traffic on Wireshark.
 

Step 6. Observe RDP Traffic

- 1.On Wireshark, from the windows VM, filter for 'tcp.port ==6689'. This port is used for Remote Desktop.

What you will notice is, there will be a constant stream of data being sent. Comparatively to SSH, as it only traces and encriptes every keystroke.

<p>
