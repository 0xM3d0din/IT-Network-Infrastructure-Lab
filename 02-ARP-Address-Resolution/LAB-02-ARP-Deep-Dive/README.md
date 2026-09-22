# LAB 02 — ARP Deep Dive



## 🎯 Objective



Understand how ARP (Address Resolution Protocol) resolves an IPv4 address to a MAC address and how ARP works together with Ethernet and Layer 2 switching.



### Skills Practiced



- ARP

- ARP Request

- ARP Reply

- ARP Cache

- IPv4 to MAC Resolution

- Ethernet Broadcast

- Unicast Communication

- Broadcast Domain

- Switch Flooding

- MAC Address Table

- ARP Table vs MAC Address Table

- ARP Cache Clearing

- MAC Table Clearing

- ARP and ICMP Interaction

- Basic ARP Troubleshooting Concepts



---



## 🧩 Topology



```text

&#x20;                   PC1

&#x20;                    |

&#x20;                    |

PC0 ---------------- SW1 ---------------- PC2

&#x20;                    |

&#x20;                    |

&#x20;                   PC3

```



### Switch Connections



```text

PC0 → SW1 Fa0/1

PC1 → SW1 Fa0/2

PC2 → SW1 Fa0/3

PC3 → SW1 Fa0/4

```



All hosts were placed in the same Layer 2 network and broadcast domain.



---



## 🌐 Network Configuration

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC0 | 192.168.1.10 | 255.255.255.0 | None |
| PC1 | 192.168.1.20 | 255.255.255.0 | None |
| PC2 | 192.168.1.30 | 255.255.255.0 | None |
| PC3 | 192.168.1.40 | 255.255.255.0 | None |



All hosts are in:



```text

192.168.1.0/24

```



No default gateway was required because communication remained within the same subnet.



---



# 📚 Core Concepts



## 1. What is ARP?



ARP stands for:



\*\*Address Resolution Protocol\*\*



Its purpose is to resolve:



```text

IPv4 Address → MAC Address

```



Example:



```text

192.168.1.20

&#x20;     ↓

&#x20;    ARP

&#x20;     ↓

0010.114E.72CC

```



ARP is used when a host knows the destination IPv4 address but does not yet know the destination MAC address required for local Ethernet communication.



---



## 2. ARP Table



The host maintains an ARP cache/table containing mappings such as:



```text

IP Address → MAC Address

```



Example:



```text

192.168.1.20 → 0010.114E.72CC

```



The ARP cache can be viewed using:



```cmd

arp -a

```



---



## 3. ARP Request



When PC0 wants to communicate with:



```text

192.168.1.20

```



but does not know the corresponding MAC address, it sends an:



\*\*ARP Request\*\*



The request asks:



```text

Who has 192.168.1.20?

```



The ARP Request is sent as an Ethernet broadcast.



Destination MAC:



```text

FF:FF:FF:FF:FF:FF

```



---



## 4. Broadcast



Because the requesting host does not know which device owns the destination IP address, the ARP Request is broadcast within the local broadcast domain.



Conceptually:



```text

PC0

&#x20;↓

ARP Request

&#x20;↓

Broadcast

&#x20;↓

Switch

&#x20;↓

Flooding within the broadcast domain

&#x20;↓

PC1 / PC2 / PC3

```



Only the host configured with the requested IP should respond to the ARP Request.



---



## 5. ARP Reply



PC1 owns:



```text

192.168.1.20

```



Therefore, it sends an ARP Reply containing its MAC address.



Conceptually:



```text

192.168.1.20 → 0010.114E.72CC

```



The reply allows PC0 to learn the destination MAC address.



The ARP Reply is normally sent directly back to the requesting host rather than being broadcast to every host.



---



# 🔄 ARP Resolution Flow



The complete local resolution process is:



```text

Destination IP

&#x20;     ↓

Is the MAC already known?

&#x20;     ↓

&#x20;    No

&#x20;     ↓

ARP Request

&#x20;     ↓

Broadcast

&#x20;     ↓

Switch Flooding

&#x20;     ↓

Target Host

&#x20;     ↓

ARP Reply

&#x20;     ↓

IP → MAC stored in ARP Cache

&#x20;     ↓

Ethernet Frame

&#x20;     ↓

Switch

&#x20;     ↓

Destination Port

&#x20;     ↓

Target Host

```



---



# 🔗 ARP and Layer 2 Switching



ARP provides the MAC address required for local Ethernet delivery.



After PC0 learns:



```text

192.168.1.20 → 0010.114E.72CC

```



it can build an Ethernet frame such as:



```text

Source MAC:

PC0 MAC



Destination MAC:

0010.114E.72CC

```



The frame is then processed by the Layer 2 switch.



The switch uses its MAC Address Table to determine the correct outgoing port.



---



# 🆚 ARP Table vs MAC Address Table



These are different tables maintained by different devices.



## ARP Table



Maintained by the host:



```text

IP Address → MAC Address

```



Command:



```cmd

arp -a

```



## MAC Address Table



Maintained by the switch:



```text

MAC Address → Switch Port

```



Command:



```cisco

show mac address-table

```



### Relationship



```text

PC:

IP → MAC



Switch:

MAC → Port

```



---



# 🧪 Experiment 1 — ARP Cache Before Traffic



Before generating traffic, the ARP cache on PC0 was inspected using:



```cmd

arp -a

```



The purpose was to determine whether the destination MAC was already known.



---



# 🧪 Experiment 2 — MAC Table Before Traffic



The switch MAC Address Table was checked using:



```cisco

show mac address-table

```



This established the initial Layer 2 state before the main communication test.



---



# 🧪 Experiment 3 — Observe ARP Request



Packet Tracer Simulation Mode was used to observe the ARP process.



PC0 generated traffic toward:



```text

192.168.1.20

```



The ARP Request was observed as a broadcast:



```text

Destination MAC:

FF:FF:FF:FF:FF:FF

```



The switch flooded the broadcast within the local broadcast domain.



---



# 🧪 Experiment 4 — Broadcast Reaches Local Hosts



The ARP Request was observed reaching hosts in the same broadcast domain.



The requested IP belonged to PC1:



```text

192.168.1.20

```



Other hosts received the broadcast but were not the owner of the requested IP.



This demonstrated the behavior of ARP broadcasts inside a local Layer 2 domain.



---



# 🧪 Experiment 5 — ARP Reply



PC1 responded to the ARP Request with its MAC address.



Conceptually:



```text

192.168.1.20

&#x20;       ↓

0010.114E.72CC

```



PC0 could then store the mapping in its ARP cache.



---



# 🧪 Experiment 6 — ARP Cache After Resolution



After the ARP exchange, PC0's ARP cache was checked again:



```cmd

arp -a

```



The expected relationship was:



```text

192.168.1.20 → PC1 MAC

```



This demonstrated successful IPv4-to-MAC resolution.



---



# 🧪 Experiment 7 — MAC Table After Traffic



The switch MAC Address Table was checked again:



```cisco

show mac address-table

```



Traffic generated by the ARP exchange and subsequent communication allowed the switch to learn source MAC addresses and associate them with the appropriate switch ports.



This connected the ARP process from this lab with the MAC learning behavior studied in LAB 01.



---



# 🧪 Experiment 8 — Ping With Existing ARP Cache



After the ARP entry already existed, communication was tested again using:



```cmd

ping 192.168.1.20

```



Because the destination MAC was already available in the ARP cache, a new ARP Request was not required for every packet.



The host could proceed directly toward Ethernet delivery using the cached mapping.



---



# 🧪 Experiment 9 — Clear ARP Cache



The ARP cache was cleared on the host:



```cmd

arp -d \*

```



The cache was then checked again:



```cmd

arp -a

```



The purpose was to remove the previously learned IP-to-MAC mapping.



Afterward, traffic was generated again:



```cmd

ping 192.168.1.20

```



This caused ARP resolution to occur again because the destination MAC address was no longer available in the ARP cache.



---



# 🧪 Experiment 10 — Clear MAC Address Table



The switch dynamic MAC entries were cleared using:



```cisco

clear mac-address-table dynamic

```



The MAC table was then inspected:



```cisco

show mac address-table

```



Traffic was generated again to observe dynamic MAC relearning.



This demonstrated that:



```text

ARP Cache

```



and:



```text

Switch MAC Address Table

```



are independent mechanisms.



The host is responsible for maintaining:



```text

IP → MAC

```



while the switch maintains:



```text

MAC → Port

```



---



# 🔍 Key Comparison



## ARP



```text

IP → MAC

```



Used by the host to resolve an IPv4 address to a MAC address for local Ethernet communication.



## Switching



```text

MAC → Port

```



Used by the switch to determine where to forward an Ethernet frame.



### Combined Flow



```text

Destination IP

&#x20;     ↓

ARP

&#x20;     ↓

Destination MAC

&#x20;     ↓

Ethernet Frame

&#x20;     ↓

Switch MAC Address Table

&#x20;     ↓

Destination Port

&#x20;     ↓

Destination Host

```



---



# 🧠 Important Rules



1\. ARP resolves IPv4 addresses to MAC addresses.

2\. ARP Requests are broadcast.

3\. The destination MAC address of an ARP Request is:



```text

FF:FF:FF:FF:FF:FF

```



4\. The device that owns the requested IP responds with an ARP Reply.

5\. The host stores the learned IP-to-MAC mapping in its ARP cache.

6\. The ARP Table maps:



```text

IP → MAC

```



7\. The switch MAC Address Table maps:



```text

MAC → Port

```



8\. ARP Requests are flooded within the local broadcast domain.

9\. Once the destination MAC is known, the host can build an Ethernet frame for local delivery.

10\. The ARP cache reduces the need to perform ARP resolution repeatedly.

11\. Clearing the ARP cache causes ARP resolution to be performed again when the destination MAC is needed.

12\. Clearing the dynamic MAC Address Table causes the switch to relearn MAC addresses from subsequent traffic.



---



# 🛠️ Commands Used



## PC



```cmd

ipconfig /all

arp -a

arp -d \*

ping 192.168.1.20

```



## Switch



```cisco

enable

show mac address-table

show mac address-table dynamic

clear mac-address-table dynamic

```



---



# 📁 Lab Files



```text

LAB-02-ARP-Deep-Dive/

│

├── LAB-02-ARP-Deep-Dive.pkt

├── README.md

└── screenshots/

```



---



## ✅ Status



\*\*Completed\*\*



\*\*100-Day IT Infrastructure Journey — LAB 02\*\*


