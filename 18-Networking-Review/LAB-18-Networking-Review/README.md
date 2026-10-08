# 🧠 LAB 18 — Networking Review & Knowledge Gaps

## 📌 Overview

LAB 18 is a dedicated **Networking Review & Knowledge Consolidation** day.

The goal is to review the networking concepts and technologies covered throughout LAB 00 → LAB 17, connect them together as one complete network flow, and close important knowledge gaps before moving into the next phase of the **100-Day IT Infrastructure Journey**.

This lab was intentionally completed **without Cisco Packet Tracer**.  
The focus was on understanding, connecting concepts, reviewing terminology, and building a stronger troubleshooting mindset.

---

## 🎯 Objectives

- Review all major networking concepts covered so far.
- Connect Layer 1, Layer 2, Layer 3, and network services together.
- Reinforce the relationship between IP, MAC, ARP, Switching, and Routing.
- Review VLANs, Trunks, Inter-VLAN Routing, STP, and EtherChannel.
- Review Static Routing and OSPF.
- Review DHCP and DNS.
- Review NAT/PAT, ACLs, and Port Security.
- Review IPv6, NDP, ICMPv6, and SLAAC.
- Understand the OSI and TCP/IP models.
- Review TCP vs UDP and common network ports.
- Understand Encapsulation and Decapsulation.
- Strengthen the network troubleshooting mindset.
- Identify important networking topics that require deeper review later.

---

# 🌐 Networking Concepts Reviewed

## 1. Network Fundamentals

Reviewed:

- IP Address
- MAC Address
- Subnet Mask
- Prefix Length
- Default Gateway
- Ping
- ICMP
- Local vs Remote Networks

### Core Question

> Is the destination in the same network or in a different network?

---

## 2. Switching & MAC Learning

Reviewed:

- MAC Address Table
- MAC Learning
- Frame Forwarding
- Unknown Unicast Flooding
- Broadcast Traffic
- Switch Ports

### Core Flow

Source MAC → Switch Learning → MAC Table → Forwarding Decision

---

## 3. ARP

Reviewed the relationship between IPv4 addresses and MAC addresses.

### IPv4 Communication

IP Address → ARP → MAC Address → Ethernet Frame

ARP Request is commonly sent as a broadcast, followed by an ARP Reply from the destination device.

---

## 4. VLANs

Reviewed:

- VLAN segmentation
- VLAN IDs
- Access Ports
- Broadcast Domains
- Communication between devices in the same VLAN
- Communication between devices in different VLANs

### Core Concept

Different VLANs represent different logical Layer 2 networks.

---

## 5. Trunking

Reviewed:

- Trunk Links
- Multiple VLANs over one link
- VLAN traffic between switches
- Allowed VLANs

### Core Concept

Multiple VLANs → One Trunk Link

---

## 6. Inter-VLAN Routing

Reviewed how devices in different VLANs communicate through a Layer 3 device.

### Basic Flow

Host → Default Gateway → Router / Layer 3 Device → Destination VLAN

---

## 7. STP

Reviewed:

- Layer 2 loops
- Redundant links
- Spanning Tree Protocol
- Root Bridge
- Alternate paths
- Loop prevention

### Core Goal

Prevent Layer 2 loops while maintaining redundancy.

---

## 8. EtherChannel

Reviewed:

- Multiple physical links
- Logical Port-Channel
- Link aggregation
- Redundancy
- Aggregate capacity

### Core Concept

Multiple Physical Links → One Logical Link

---

# 🛣️ Routing

## 9. Routing Fundamentals

Reviewed:

- Routing Table
- Destination Network
- Next Hop
- Exit Interface
- Connected Routes
- Static Routes
- Dynamic Routing

### Core Question

> Where should this packet go next?

---

## 10. Static Routing

Reviewed manually configured routes.

### Basic Concept

Destination Network → Next Hop / Exit Interface

Static routing provides direct control but becomes harder to manage as networks grow.

---

## 11. OSPF

Reviewed:

- Dynamic Routing
- OSPF
- Areas
- Area 0
- Multi-Area OSPF
- Passive Interfaces
- OSPF Route Types

### Core Concept

Routers exchange routing information and dynamically build routing tables.

---

# 🧩 Network Services

## 12. DHCP

Reviewed automatic IP configuration.

### DORA

Discover → Offer → Request → ACK

DHCP can provide:

- IP Address
- Subnet Mask
- Default Gateway
- DNS Information

---

## 13. DNS

Reviewed Name Resolution.

### Basic Flow

Hostname → DNS → IP Address

### Troubleshooting Example

If the IP address works but the hostname does not resolve, DNS becomes a primary area of investigation.

---

# 🔐 Network Security

## 14. NAT / PAT

Reviewed:

### Static NAT
Fixed one-to-one address translation.

### Dynamic NAT
Translation using an address pool.

### PAT
Allows multiple internal connections to share translated addressing using transport-level information.

### Core Flow

Private Address → Translation → Outside Network

---

## 15. ACL

Reviewed:

### Standard ACL
Primarily evaluates the source IP address.

### Extended ACL
Can evaluate:

- Source
- Destination
- Protocol
- Port

ACLs can be used to permit or deny specific traffic flows.

---

## 16. Port Security

Reviewed:

- MAC Address Control
- Maximum Allowed MAC Addresses
- Static Secure MAC
- Sticky MAC
- Violation Detection
- Shutdown / Recovery
- Unauthorized Device Scenarios

### Core Goal

Control which devices are allowed to use a switch access port.

---

# 🌍 IPv6

## 17. IPv6 Fundamentals

Reviewed:

- IPv6 Addressing
- Global Unicast
- Link-Local
- Prefix Length
- IPv6 Default Gateway
- ICMPv6
- Neighbor Discovery
- SLAAC

### Important Comparison

IPv4 → ARP

IPv6 → NDP / ICMPv6

### SLAAC

IPv6 hosts can automatically configure addressing information using Router Advertisements.

---

# 🏗️ OSI Model

Reviewed the seven OSI layers:

1. Application
2. Presentation
3. Session
4. Transport
5. Network
6. Data Link
7. Physical

The goal is not only memorization, but using the model to identify where a problem may exist.

---

# 🌐 TCP/IP Model

Reviewed the TCP/IP model as a networking framework:

1. Application
2. Transport
3. Internet
4. Network Access

The TCP/IP model connects the protocols and technologies used throughout the previous networking labs.

---

# 🔄 TCP vs UDP

## TCP

- Connection-oriented
- Reliable delivery
- Ordered communication

## UDP

- Connectionless
- Lower overhead
- No TCP-style delivery guarantees

### Core Idea

TCP → Reliability

UDP → Simplicity / Lower Overhead

---

# 🔢 Common Network Ports

| Protocol | Port |
|---|---:|
| HTTP | 80 |
| HTTPS | 443 |
| DNS | 53 |
| SSH | 22 |
| RDP | 3389 |
| FTP | 21 |
| DHCP Server | 67 |
| DHCP Client | 68 |

The goal is familiarity with common services and understanding how ports are useful during troubleshooting and ACL configuration.

---

# 📦 Encapsulation & Decapsulation

Reviewed how application data moves through the networking stack.

### Encapsulation

Application Data  
↓  
Transport Segment / Datagram  
↓  
Network Packet  
↓  
Data Link Frame  
↓  
Physical Bits

### Decapsulation

Bits  
↓  
Frame  
↓  
Packet  
↓  
Segment / Datagram  
↓  
Application Data

---

# 📡 Traffic Types

## Unicast

One → One

## Broadcast

One → All devices within the broadcast domain

## Multicast

One → Group

---

# 🔊 Broadcast Domain

A Broadcast Domain is the set of devices that can receive a Layer 2 broadcast.

VLANs are used to create separate logical broadcast domains.

---

# ⚡ Collision Domain

Reviewed the concept of collision domains and how modern switched, full-duplex Ethernet environments differ from older shared-media networks.

---

# 🛠️ Troubleshooting Mindset

A major objective of this review was developing a structured troubleshooting methodology.

Instead of changing configurations randomly, troubleshooting should follow an evidence-based process.

### Workflow

Ticket  
→ Identify Symptoms  
→ Gather Evidence  
→ Isolate the Problem  
→ Identify the Layer / Component  
→ Diagnose  
→ Fix  
→ Verify  
→ Document

---

# 🔎 Troubleshooting Decision Flow

A basic troubleshooting sequence:

Physical Connectivity  
↓  
Link / Interface Status  
↓  
Correct VLAN  
↓  
Correct IP / Mask  
↓  
Correct Default Gateway  
↓  
Local Connectivity  
↓  
Remote Connectivity  
↓  
Routing  
↓  
DNS / DHCP  
↓  
ACL / Security  
↓  
Application / Service

This is a general framework and the actual troubleshooting path depends on the symptoms and available evidence.

---

# 🧠 Common Troubleshooting Scenarios

## No Link

Possible areas:

- Cable
- NIC
- Interface
- Switch Port

---

## Host Has an IP but Cannot Reach the Gateway

Possible areas:

- VLAN
- Subnet Mask
- Interface
- Local Connectivity

---

## Gateway Works but Remote Network Fails

Possible areas:

- Routing
- Missing Route
- Incorrect Route
- Routing Protocol

---

## IP Works but Hostname Fails

Possible area:

- DNS

---

## Some VLANs Work While Others Fail

Possible areas:

- Trunk Configuration
- Allowed VLANs
- VLAN Configuration

---

## One Device Is Specifically Blocked

Possible areas:

- ACL
- Port Security
- Host Configuration

---

# 💻 Useful Troubleshooting Commands

## Cisco IOS

    show ip interface brief
    show ipv6 interface brief
    show ip route
    show ipv6 route
    show arp
    show ipv6 neighbors
    show mac address-table
    show vlan brief
    show interfaces trunk
    show spanning-tree
    show etherchannel summary
    show ip ospf neighbor
    show ip ospf interface
    show access-lists
    show ip nat translations
    show ip nat statistics
    show port-security
    show port-security interface

## Windows

    ipconfig
    ipconfig /all
    ping
    tracert
    nslookup
    arp -a

The objective is to understand when and why each command should be used rather than memorizing commands without context.

---

# 🔗 End-to-End Network Mental Model

A typical communication flow can be understood as:

Application  
↓  
DNS Name Resolution (if required)  
↓  
Destination IP  
↓  
Local or Remote Network Decision  
↓  
ARP / NDP if needed  
↓  
Default Gateway for Remote Destinations  
↓  
Switching / VLAN / Trunking  
↓  
Routing  
↓  
ACL / NAT / Security where applicable  
↓  
Destination Host / Service  
↓  
Response

This connects the major concepts studied throughout the networking phase into one complete communication process.

---

# 📚 Knowledge Gaps Identified

The following topics were introduced or reinforced during the review and may require deeper study later:

- OSI Model
- TCP/IP Model
- OSI ↔ TCP/IP Mapping
- TCP vs UDP
- Common Ports
- Encapsulation / Decapsulation
- Packet Flow
- Troubleshooting Methodology

These topics were reviewed conceptually without creating separate Packet Tracer labs.

---

# 📝 Lab Method

This lab intentionally followed a different format from the previous networking labs.

### Today's Method

Theory  
→ Review  
→ Connect Concepts  
→ Identify Knowledge Gaps  
→ Troubleshooting Scenarios  
→ Consolidation

### Packet Tracer

Not Used

### Configuration

Not Required

### Screenshots

Not Required

---

# ✅ Final Checklist

- [x] Network Fundamentals
- [x] IP Addressing
- [x] MAC Address
- [x] Subnetting
- [x] ARP
- [x] Switching
- [x] VLANs
- [x] Trunking
- [x] Inter-VLAN Routing
- [x] STP
- [x] EtherChannel
- [x] Routing
- [x] Static Routing
- [x] OSPF
- [x] DHCP
- [x] DNS
- [x] NAT / PAT
- [x] ACL
- [x] Port Security
- [x] IPv6
- [x] NDP
- [x] SLAAC
- [x] OSI Model
- [x] TCP/IP Model
- [x] TCP vs UDP
- [x] Common Ports
- [x] Encapsulation
- [x] Unicast / Broadcast / Multicast
- [x] Broadcast Domain
- [x] Collision Domain
- [x] ICMP
- [x] Troubleshooting Mindset
- [x] Packet Flow

---

# 🏁 Result

**DAY 18 — Networking Review & Knowledge Gaps ✅**

The Networking Foundation was consolidated through a complete conceptual review of the major technologies covered from LAB 00 to LAB 17.

This review created a stronger foundation for the next stage of the journey:

**Windows & IT Support**

The larger Network Troubleshooting and Enterprise Network Integration labs are intentionally deferred to a later practical consolidation stage.

---

# 🚀 Next

**DAY 19 — Windows & IT Support**

Focus areas:

- Windows Basics
- Windows Administration
- Users & Groups
- Permissions
- Processes
- Services
- Task Manager
- Event Viewer
- Device Manager
- Windows Network Troubleshooting
- TCP/IP in Windows
- DNS Client
- DHCP Client
- RDP
- File Sharing
- Printer Basics
- User Support
- Ticket-Style Scenarios

---

🔥 **100-Day IT Infrastructure Journey**

**Learn → Build → Test → Troubleshoot → Verify → Document**