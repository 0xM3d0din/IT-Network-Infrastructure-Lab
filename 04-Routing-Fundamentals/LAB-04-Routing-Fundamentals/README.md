# LAB 04 — Routing Fundamentals



## 🎯 Objective



Understand the fundamentals of Layer 3 routing and learn how a router enables communication between different IPv4 subnets.



The lab focuses on:



- Router vs Switch

- Layer 2 vs Layer 3

- Default Gateway

- Local vs Remote Destinations

- Router Interfaces

- Connected Routes

- Routing Table

- Packet Forwarding

- ARP for the Default Gateway

- Hop-by-Hop MAC Address Changes

- End-to-End IP Communication

- Routing Troubleshooting

- Intentional Default Gateway Failure

- Root Cause Analysis

- Final Verification

- Traceroute



The lab was implemented and tested using Cisco Packet Tracer.



---



## 🧩 Topology



```text

PC0 ─── SW1 ─── R1 ─── SW2 ─── PC1

```



### Devices



```text

PC0

PC1



SW1 — Cisco 2960

SW2 — Cisco 2960



R1 — Cisco 2911

```



### Connections



```text

PC0 FastEthernet0 → SW1 FastEthernet0/1



SW1 FastEthernet0/24 → R1 GigabitEthernet0/0



R1 GigabitEthernet0/1 → SW2 FastEthernet0/24



SW2 FastEthernet0/1 → PC1 FastEthernet0

```



---



## 🌐 Network Configuration



Two different `/26` subnets were used.



### Network 1



```text

Network:

192.168.10.0/26



Subnet Mask:

255.255.255.192



Usable Hosts:

192.168.10.1 → 192.168.10.62



Broadcast:

192.168.10.63

```



### Network 2



```text

Network:

192.168.10.64/26



Subnet Mask:

255.255.255.192



Usable Hosts:

192.168.10.65 → 192.168.10.126



Broadcast:

192.168.10.127

```



### Device Addressing



| Device | Interface | IP Address | Subnet Mask | Default Gateway |

|---|---|---|---|---|

| PC0 | FastEthernet0 | 192.168.10.10 | 255.255.255.192 | 192.168.10.62 |

| R1 | GigabitEthernet0/0 | 192.168.10.62 | 255.255.255.192 | — |

| R1 | GigabitEthernet0/1 | 192.168.10.126 | 255.255.255.192 | — |

| PC1 | FastEthernet0 | 192.168.10.70 | 255.255.255.192 | 192.168.10.126 |



---



# 📚 Core Concepts



## 1. Router vs Switch



A switch primarily performs Layer 2 forwarding using MAC addresses.



A router performs Layer 3 forwarding using IP addresses and connects different IP networks.



```text

Switch:

MAC Address → Port



Router:

Destination IP → Route / Outgoing Interface

```



The router is therefore responsible for forwarding traffic between different subnets.



---



## 2. Local vs Remote Destination



A host uses its IP address and subnet mask to determine whether a destination is local or remote.



Example:



```text

PC0:

192.168.10.10/26

```



belongs to:



```text

192.168.10.0/26

```



while:



```text

PC1:

192.168.10.70/26

```



belongs to:



```text

192.168.10.64/26

```



Therefore they are in different subnets.



```text

192.168.10.10/26

→ 192.168.10.0/26



192.168.10.70/26

→ 192.168.10.64/26

```



Because the destination is remote, PC0 uses its Default Gateway.



---



## 3. Default Gateway



PC0 was configured with:



```text

Default Gateway:

192.168.10.62

```



This address belongs to R1 GigabitEthernet0/0.



When PC0 identifies a destination as remote, it does not ARP directly for the remote host.



Instead, it resolves the MAC address of the Default Gateway.



The forwarding decision is:



```text

Destination IP

↓

Determine Local or Remote

↓

Remote

↓

Use Default Gateway

↓

ARP for Gateway MAC

↓

Send Ethernet Frame to Router

```



---



## 4. Router Interfaces



R1 used two Layer 3 interfaces:



```text

GigabitEthernet0/0

192.168.10.62/26



GigabitEthernet0/1

192.168.10.126/26

```



Both interfaces were enabled using:



```text

no shutdown

```



The interface state was verified using:



```text

show ip interface brief

```



The final operational state was:



```text

GigabitEthernet0/0 → up/up

GigabitEthernet0/1 → up/up

```



---



## 5. Routing Table



The routing table was inspected using:



```text

show ip route

```



R1 automatically learned the two directly connected networks.



The important routes were:



```text

C    192.168.10.0/26

&#x20;    directly connected, GigabitEthernet0/0



C    192.168.10.64/26

&#x20;    directly connected, GigabitEthernet0/1

```



`C` means:



```text

Connected

```



The router also displayed local `/32` routes for its own interface addresses:



```text

L    192.168.10.62/32

L    192.168.10.126/32

```



No default route was configured.



The routing table therefore contained the two required connected networks without manually adding static routes.



---



## 6. Connected Routes



When an interface has an IP address and is operational, the router can automatically identify the directly connected subnet.



Example:



```text

G0/0

192.168.10.62/26

```



results in:



```text

192.168.10.0/26

```



being known as a connected network.



Similarly:



```text

G0/1

192.168.10.126/26

```



results in:



```text

192.168.10.64/26

```



being known as a connected network.



---



# 🧪 Experiment 1 — Baseline Routing



The initial configuration was tested from PC0.



Test:



```text

ping 192.168.10.70

```



The first test produced:



```text

4 Sent

3 Received

1 Lost

25% Loss

```



The subsequent ping succeeded with:



```text

4 Sent

4 Received

0% Loss

```



The initial packet loss occurred while the required ARP resolution process was being completed.



The successful test confirmed communication between PC0 and PC1 through R1.



---



# 🧪 Experiment 2 — ARP for the Default Gateway



After successful communication, PC0 was checked using:



```text

arp -a

```



The ARP table contained:



```text

192.168.10.62

0050.0F38.E101

dynamic

```



This demonstrated that PC0 learned the MAC address associated with its Default Gateway.



The forwarding relationship was:



```text

Remote Destination

↓

Default Gateway

↓

ARP

↓

Gateway MAC Address

```



PC0 did not need to resolve the MAC address of PC1 directly in its own local Layer 2 segment.



---



# 🧪 Experiment 3 — Packet Forwarding Through R1



Packet Tracer Simulation Mode was used with ICMP traffic.



The packet started at:



```text

PC0

```



with:



```text

Source IP:

192.168.10.10



Destination IP:

192.168.10.70

```



At PC0, the Layer 2 destination was the MAC address of the Default Gateway.



The packet was then forwarded:



```text

PC0

↓

SW1

↓

R1 G0/0

↓

Routing Table

↓

R1 G0/1

↓

SW2

↓

PC1

```



At R1, the incoming frame was received on:



```text

GigabitEthernet0/0

```



and forwarded out of:



```text

GigabitEthernet0/1

```



The Layer 3 addresses remained:



```text

Source IP:

192.168.10.10



Destination IP:

192.168.10.70

```



while the Layer 2 MAC addresses changed for the next network segment.



This demonstrated:



```text

IP addresses:

End-to-End communication



MAC addresses:

Hop-by-Hop delivery

```



---



# 🧪 Experiment 4 — Frame Forwarding on SW2



After R1 forwarded the packet through GigabitEthernet0/1, SW2 received the frame on:



```text

FastEthernet0/24

```



SW2 then forwarded it toward PC1 through:



```text

FastEthernet0/1

```



The Layer 2 forwarding process was therefore:



```text

R1 G0/1

↓

SW2 Fa0/24

↓

SW2 MAC Address Table

↓

SW2 Fa0/1

↓

PC1

```



This demonstrates the division of responsibilities:



```text

Router:

Layer 3 routing



Switch:

Layer 2 forwarding

```



---



# 🧪 Experiment 5 — Packet Reception at PC1



PC1 received the ICMP Echo Request with:



```text

Source IP:

192.168.10.10



Destination IP:

192.168.10.70

```



The source and destination IP addresses remained associated with the two endpoint devices.



PC1 then generated the ICMP Echo Reply:



```text

Source IP:

192.168.10.70



Destination IP:

192.168.10.10

```



The reply traveled back through the router toward PC0.



This demonstrated bidirectional communication between the two different subnets.



---



# 💥 Experiment 6 — Intentional Failure: Wrong Default Gateway



A deliberate configuration error was introduced on PC0.



The correct configuration was:



```text

Default Gateway:

192.168.10.62

```



The failure configuration changed only the gateway to:



```text

Default Gateway:

192.168.10.61

```



The IP address and subnet mask remained unchanged:



```text

IP Address:

192.168.10.10



Subnet Mask:

255.255.255.192

```



---



## Failure Testing



From PC0:



```text

ping 192.168.10.70

```



Result:



```text

4 Sent

0 Received

4 Lost

100% Loss

```



The ARP table was then checked:



```text

arp -a

```



Result:



```text

No ARP Entries Found

```



This indicated that PC0 could not resolve the incorrect gateway address.



---



# 🔍 Failure Analysis



The IP configuration was inspected using:



```text

ipconfig

```



The configuration showed:



```text

IPv4 Address:

192.168.10.10



Subnet Mask:

255.255.255.192



Default Gateway:

192.168.10.61

```



The router's actual gateway address on PC0's subnet was:



```text

192.168.10.62

```



To verify local connectivity independently from the remote destination, PC0 tested:



```text

ping 192.168.10.62

```



Result:



```text

4 Sent

4 Received

0% Loss

```



This demonstrated that:



```text

PC0 IP configuration:

Correct IP and subnet mask



Physical connectivity:

Operational



Layer 2 connectivity:

Operational



R1 G0/0:

Reachable



Remote communication:

Failed

```



The evidence therefore pointed to the incorrect Default Gateway configuration.



---



# 🔍 Root Cause



```text

Incorrect Default Gateway configured on PC0

```



Configured gateway:



```text

192.168.10.61

```



Correct gateway:



```text

192.168.10.62

```



The router itself and its connected routes were operational.



The failure was caused by the incorrect gateway configured on the end device.



---



# 🔧 Fix



PC0 was restored to:



```text

IP Address:

192.168.10.10



Subnet Mask:

255.255.255.192



Default Gateway:

192.168.10.62

```



No other addressing parameters were changed.



---



# ✅ Final Verification



After correcting the Default Gateway, the following test was performed:



```text

ping 192.168.10.70

```



Result:



```text

4 Sent

4 Received

0% Loss

```



The final configuration was verified using:



```text

ipconfig

```



showing:



```text

IPv4 Address:

192.168.10.10



Subnet Mask:

255.255.255.192



Default Gateway:

192.168.10.62

```



The successful ping confirmed that PC0 could again reach the remote subnet through R1.



---



# 🔎 Traceroute Verification



The routing path was verified from PC0 using:



```text

tracert 192.168.10.70

```



The result showed:



```text

1    192.168.10.62

2    192.168.10.70



Trace complete.

```



This demonstrated that the Default Gateway / R1 was the first Layer 3 hop before the destination host.



The path was:



```text

PC0

↓

192.168.10.62

(R1 G0/0)

↓

192.168.10.70

(PC1)

```



---



# 🧠 Key Takeaways



1. A switch primarily performs Layer 2 forwarding using MAC addresses.

2. A router performs Layer 3 forwarding using IP addresses.

3. A host uses its IP address and subnet mask to determine whether a destination is local or remote.

4. Remote destinations require a Default Gateway.

5. The host ARPs for the Default Gateway MAC when sending traffic toward a remote network.

6. A router can automatically learn directly connected networks.

7. `show ip route` can be used to inspect the routing table.

8. `C` in the routing table represents a Connected route.

9. `L` represents a Local route for the router's own interface address.

10. Router interfaces must be operational for connected routes to function correctly.

11. `no shutdown` enables a router interface administratively.

12. IP addresses identify the end-to-end source and destination.

13. MAC addresses are used for hop-by-hop Layer 2 delivery.

14. A routing table determines the outgoing interface for the destination network.

15. An incorrect Default Gateway can prevent access to remote networks even when the local network and router are operational.

16. Troubleshooting should use evidence such as `ipconfig`, `arp`, `ping`, `show ip interface brief`, and `show ip route`.

17. `tracert` can be used to observe the Layer 3 path toward a destination.



---



# 🛠️ Commands Used



## Packet Tracer PCs



```text

ipconfig



arp -a



ping 192.168.10.62



ping 192.168.10.70



ping 192.168.10.10



tracert 192.168.10.70

```



## Cisco Router



```text

enable



configure terminal



hostname R1



interface gigabitEthernet 0/0



ip address 192.168.10.62 255.255.255.192



no shutdown



interface gigabitEthernet 0/1



ip address 192.168.10.126 255.255.255.192



no shutdown



show ip interface brief



show ip route

```



---



# 📸 Evidence



The lab includes screenshots covering:



```text

Topology



PC0 Configuration

PC1 Configuration



R1 Interfaces Up

R1 Connected Routes



Baseline Ping

ARP Gateway



PC0 Forwarding to Gateway

R1 Forwarding Between Subnets

SW2 Forwarding to PC1

PC1 Received Routing Packet



Wrong Gateway Configuration

Wrong Gateway Ping Failure

Wrong Gateway Diagnosis Evidence



Final Verification

Traceroute Routing Path

```



---



# 📁 Lab Files



```text

LAB-04-Routing-Fundamentals/

│

├── LAB-04-Routing-Fundamentals.pkt

├── README.md

└── screenshots/


```



---



## ✅ Status



**Completed**



**100-Day IT Infrastructure Journey — LAB 04**
