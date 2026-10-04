Subnet Mismatch and ARP Troubleshooting

Overview

This lab demonstrates how an incorrect IPv4 subnet configuration affects network communication. Cisco Packet Tracer Simulation Mode is used to observe ARP and ICMP traffic and understand why communication fails when hosts are incorrectly addressed.

The lab also demonstrates a genuine ARP resolution failure by attempting to reach an unused IPv4 address, followed by correcting the network configuration and verifying successful connectivity.

Objectives

- Understand IPv4 subnet masks and CIDR prefixes.
- Identify a subnet mismatch between two hosts.
- Understand the role of a Layer 2 switch.
- Observe ARP requests and replies in Simulation Mode.
- Understand how ARP resolution affects ICMP communication.
- Troubleshoot failed ping communication.
- Correct IPv4 addressing configuration.
- Verify connectivity after troubleshooting.

Network Topology

       PC0                         PC1
        |                           |
        |                           |
     Fa0/1                       Fa0/2
        |                           |
        +------- Switch0 -----------+
                 Cisco 2960

Devices Used

Device| Model| Quantity
PC| End Device| 2
Switch| Cisco 2960| 1
Cable| Copper Straight-Through| 2

Initial IP Configuration

Parameter| PC0| PC1
IPv4 Address| 192.168.10.10| 192.168.20.20
Subnet Mask| 255.255.255.0| 255.255.255.0
CIDR Prefix| /24| /24
Default Gateway| Not configured| Not configured

The two PCs belong to different IPv4 networks:

PC0: 192.168.10.0/24
PC1: 192.168.20.0/24

Because PC0 considers PC1 to be on a different network, communication normally requires a Layer 3 device such as a router. No router or default gateway is configured in the initial topology.

Understanding /24

A "/24" CIDR prefix means that 24 of the 32 IPv4 bits are used for the network portion.

/24
=
255.255.255.0

For example:

192.168.10.10/24

belongs to:

Network: 192.168.10.0/24

Initial Connectivity Test

From PC0, open:

Desktop → Command Prompt

Run:

ping 192.168.20.20

The ping fails because PC0 and PC1 are configured in different subnets and no default gateway is available.

Simulation Mode

Cisco Packet Tracer Simulation Mode is used to observe network events.

Protocol Filters

Enable:

- ARP
- ICMP

Then generate traffic using the "ping" command and use the Capture/Forward button to observe packet movement.

ARP Demonstration

PC0 is temporarily configured with:

IP Address: 192.168.10.10
Subnet Mask: 255.255.0.0
CIDR: /16

PC1 remains:

IP Address: 192.168.20.20
Subnet Mask: 255.255.255.0
CIDR: /24

With the "/16" mask, PC0 considers "192.168.20.20" to be part of its local network and can therefore attempt ARP resolution for PC1.

The switch forwards the ARP broadcast to the connected devices.

Important Observation

A difference between "/16" and "/24" does not automatically cause PC1 to reject an ARP request for its own IP address.

Therefore, the different subnet masks alone should not be interpreted as an ARP failure.

Genuine ARP Resolution Failure

To demonstrate an unanswered ARP request, PC0 is configured with:

IP Address: 192.168.10.10
Subnet Mask: 255.255.0.0

Then PC0 attempts to ping an unused address:

ping 192.168.30.30

PC0 considers "192.168.30.30" to be on its local "/16" network and sends an ARP request to discover its MAC address.

Because no device owns "192.168.30.30", no ARP reply is returned.

The destination MAC address therefore cannot be resolved and the ICMP communication cannot proceed normally.

Corrected Configuration

After troubleshooting, both PCs are configured in the same subnet.

Parameter| PC0| PC1
IPv4 Address| 192.168.10.10| 192.168.10.11
Subnet Mask| 255.255.255.0| 255.255.255.0
CIDR Prefix| /24| /24
Default Gateway| Not required| Not required

Both hosts now belong to:

192.168.10.0/24

Connectivity Verification

From PC0:

ping 192.168.10.11

Expected result:

Reply from 192.168.10.11: bytes=32 time<1ms TTL=128

The exact response time may vary.

Additional Verification Commands

Display IP Configuration

ipconfig

Display ARP Table

arp -a

After successful communication, PC0 should have an ARP entry for PC1 if the Packet Tracer simulated PC supports the command.

Test Local TCP/IP Stack

ping 127.0.0.1

The loopback address tests the local TCP/IP protocol stack.

Troubleshooting Summary

Problem| Cause| Solution
PC0 cannot ping PC1| Different subnets| Configure compatible subnet addressing or use a router
No default gateway| No Layer 3 next hop| Configure a router/default gateway when communicating between subnets
ARP request receives no reply| Requested IP is unused| Verify the destination IP address
Ping fails after ARP| Return path or IP configuration problem| Check both hosts' IP addresses and subnet masks
Successful same-subnet ping| Correct addressing| No router is required for direct local communication

OSI Layer Analysis

Layer| Technology| Role
Layer 1| Ethernet cable/interfaces| Physical connectivity
Layer 2| Ethernet, MAC, ARP, Switch| Frame forwarding and MAC address resolution
Layer 3| IPv4, ICMP| IP addressing and connectivity testing

Key Learning

A switch provides Layer 2 connectivity between devices on a LAN. It does not normally route traffic between different IPv4 subnets.

The subnet mask determines whether a destination is considered local or remote.

ARP is used to resolve a local IPv4 address to a MAC address before an Ethernet frame can be sent directly to that host.

A successful ping requires correct IP addressing, appropriate subnet masks, functioning Layer 2 connectivity, and a valid return path.

Screenshots

Recommended screenshots for this lab:

1. Network topology.
2. PC0 initial IP configuration.
3. PC1 initial IP configuration.
4. Failed ping caused by the subnet mismatch.
5. ARP traffic in Simulation Mode.
6. Unanswered ARP request for the unused destination.
7. Corrected IP configuration.
8. Successful ping after correction.

Project Files

Day-04-Subnet-Mismatch-ARP-Failure/
│
├── README.md
├── Day-04-Subnet-Mismatch-ARP-Failure.pkt
│
└── screenshots/
    ├── 01-network-topology.png
    ├── 02-pc0-ip-configuration.png
    ├── 03-pc1-ip-configuration.png
    ├── 04-failed-ping.png
    ├── 05-arp-simulation.png
    ├── 06-arp-resolution-failure.png
    ├── 07-corrected-ip-configuration.png
    └── 08-successful-ping.png

Conclusion

This lab demonstrates how subnet configuration affects IPv4 communication and how ARP operates within a switched Ethernet network.

The troubleshooting process shows that a physical connection alone does not guarantee IP connectivity. Correct IPv4 addresses, subnet masks, ARP resolution, and appropriate routing information are required for successful communication.
