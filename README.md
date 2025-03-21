<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create 2 virtual machines(windows & linux) on the same sub network
- install wireshark on windows vm and observe ICMP traffic after pinging linux vm private IP
- Configure a firewall for the linux vm and observe ICMP traffic
- Observe SSH traffic after you SSH into Linux vm from powershell
- observe DHCP, DNS, & RDP traffic

<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/Wb48Fce.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Creating a Windows virtual machine and a Ubuntu virtual machine using Microsoft Azure. Before creating the virtual machines, make sure both of them are in the same virtual subnetwork.
</p>
<br />

<p>
<img src="https://i.imgur.com/ngC1l88.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Install Wireshark in the Windows virtual machine and start capturing network traffic
</p>
<br />

<p>
<img src="https://i.imgur.com/8sNMfk5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Filter for ICMP traffic only. It should be empty but after pinging the Ubuntu vm using the "ping" command in powershell ICMP packets should start to appear. Clicking a packet shows the source MAC address, the IPv4 address, and what the packet contains.
</p>
<br />

<p>
<img src="https://i.imgur.com/tROXHVz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Initiate a non-stop ping using the "ping [private ip] -t" command in powershell. Go into Microsoft Azure and configuring the firewall for the Ubuntu virtual machine so that it denies any ICMP traffic from entering and the firewall will drop the traffic.
</p>
<br />

<p>
<img src="https://i.imgur.com/4u8oCn6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
When the rule takes effect the ping should start timing out because the Ubuntu vm is not replying to the ping since the firewall is dropping the traffic. On Wireshark the network packets will go from "reply" "request" to "no response found" since the replies are getting blocked by the firewall.
</p>
<br />

<p>
<img src="https://i.imgur.com/J8cW4sY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
If we SSH into the Ubuntu vm via its private IP address, network packets for SSH start to show up. Everysingle keystroke gets sent to the Linux computer and you can see it as a network packet on wireshell. With SSH all the traffic is encrypted so when you try to inspect a packet to read its content, it will show up as scrambled on the network and we won't be able to read it. Telnet is the exact opposite of ths where you would be able to read the caputured packets over the network. SSH uses TCP port 22 to communicate. 
</p>
<br />

<p>
<img src="https://i.imgur.com/xZopHnZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
DHCP protocol is used to assign an IP address to devices when they are first connected to the network. Uses UDP port 67 & 68. in the Windows vm using the command "ipconfig /release" & "ipconfig "renew" will capture the DHCP packets in Wireshark. From the packets you can see that the ip was released with the first command. Then when trying to get a new IP the source IP was 0.0.0.0 since it got released. The server sent an offer request packet, my computer sent a request packet back to the DHCP server, the DHCP server acknowleged the request sent, and a IP was assigned to my computer
</p>
<br />

<p>
<img src="https://i.imgur.com/TGtz3EK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Use the "nslookup" command to figure out what the IP address of a website is. The computer will reach out to the DNS server and the DNS server will findout and tell us the IP address. This should cause us to see a bunch of traffic in Wireshark. 
</p>
<br />

<p>
<img src="https://i.imgur.com/hxnxNuq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
TCP port 3389 is used for the Remote Desktop Protocol(RDP). Since connecting to the Windows vm uses RDP there is a constant spam of traffic in Wireshark. This is because RDP is constantly streaming a picture from the server to our local machine so Wireshark is constantly is getting flooded with RDP network packets.
</p>
<br />
