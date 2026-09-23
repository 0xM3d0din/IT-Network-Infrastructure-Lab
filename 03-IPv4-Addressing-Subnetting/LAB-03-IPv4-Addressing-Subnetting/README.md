# LAB 03 — IPv4 Addressing & Subnetting



## 🎯 Objective



Understand IPv4 addressing and subnetting and learn how to determine:



- Network Address

- Broadcast Address

- First Usable Host

- Last Usable Host

- Number of Usable Hosts

- Subnet / Address Range

- Block Size

- Subnet Boundaries



The lab also applies IPv4 and subnetting concepts to practical network testing and basic IP troubleshooting using Cisco Packet Tracer.



---



## 🧩 Topology



```text

PC0 ─┐

PC1 ─┼── SW1 ───────── SW2 ─┬── PC3

PC2 ─┘                       ├── PC4

&#x20;                           └── PC5

```



### Connections



```text

PC0 → SW1 Fa0/1

PC1 → SW1 Fa0/2

PC2 → SW1 Fa0/3



PC3 → SW2 Fa0/1

PC4 → SW2 Fa0/2

PC5 → SW2 Fa0/3



SW1 Fa0/24 ↔ SW2 Fa0/24

```



No router was used in this lab.



No default gateway was configured.



---



## 🌐 Network Configuration



The lab starts from:



```text

192.168.10.0/24

```



and uses `/26` subnets.



### Subnet 1



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



Devices:



| Device | IP Address | Subnet Mask |

|---|---|---|

| PC0 | 192.168.10.10 | 255.255.255.192 |

| PC1 | 192.168.10.20 | 255.255.255.192 |

| PC2 | 192.168.10.30 | 255.255.255.192 |



### Subnet 2



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



Devices:



| Device | IP Address | Subnet Mask |

|---|---|---|

| PC3 | 192.168.10.70 | 255.255.255.192 |

| PC4 | 192.168.10.80 | 255.255.255.192 |

| PC5 | 192.168.10.90 | 255.255.255.192 |



---



# 📚 Core Concepts



## 1. IPv4 Addressing



An IPv4 address contains:



```text

32 bits

```



divided into:



```text

4 Octets

```



Each octet contains:



```text

8 bits

```



Example:



```text

192.168.10.70

```



Each octet can contain values from:



```text

0 → 255

```



because an 8-bit octet provides:



```text

2^8 = 256

```



possible values.



---



## 2. Binary Representation



The bit weights of an 8-bit octet are:



```text

128 64 32 16 8 4 2 1

```



Examples:



```text

10  = 00001010

25  = 00011001

64  = 01000000

192 = 11000000

255 = 11111111

```



Binary representation is important because subnetting is based on individual bits.



---



## 3. Network Portion and Host Portion



A CIDR prefix determines how many bits belong to the network portion.



Example:



```text

192.168.1.10/24

```



Means:



```text

24 Network Bits

8 Host Bits

```



Binary subnet mask:



```text

11111111.11111111.11111111.00000000

^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^

&#x20;      Network               Host

```



Therefore:



```text

Network Address:

192.168.1.0

```



---



## 4. CIDR and Subnet Masks



Common prefixes practiced in this lab:



| CIDR | Subnet Mask | Host Bits | Total Addresses | Usable Hosts |

|---|---|---:|---:|---:|

| /24 | 255.255.255.0 | 8 | 256 | 254 |

| /25 | 255.255.255.128 | 7 | 128 | 126 |

| /26 | 255.255.255.192 | 6 | 64 | 62 |

| /27 | 255.255.255.224 | 5 | 32 | 30 |

| /28 | 255.255.255.240 | 4 | 16 | 14 |



The important relationship is:



```text

CIDR

↓

Network Bits + Host Bits

↓

Subnet Mask

↓

Subnet Size

```



---



## 5. Network Address



The Network Address identifies the subnet itself.



Example:



```text

192.168.1.10/24

```



Results in:



```text

Network:

192.168.1.0

```



The Network Address is not assigned to a normal host.



---



## 6. Broadcast Address



The Broadcast Address is the last address in the subnet.



For:



```text

192.168.1.0/24

```



the Broadcast Address is:



```text

192.168.1.255

```



The Broadcast Address is not assigned to a normal host.



---



## 7. Usable Host Range



For:



```text

192.168.1.0/24

```



the range is:



```text

Network:

192.168.1.0



Usable:

192.168.1.1 → 192.168.1.254



Broadcast:

192.168.1.255

```



Usable hosts:



```text

256 - 2 = 254

```



The two reserved addresses are:



```text

Network Address

Broadcast Address

```



---



# 🧮 Subnetting



## 8. /26 Subnetting



For:



```text

/26

```



we have:



```text

26 Network Bits

6 Host Bits

```



Therefore:



```text

2^6 = 64 total addresses

```



and:



```text

64 - 2 = 62 usable hosts

```



The subnet mask is:



```text

255.255.255.192

```



---



## 9. Block Size



For `/26`:



```text

Subnet Mask:

255.255.255.192

```



Block size:



```text

256 - 192 = 64

```



Therefore the subnet boundaries are:



```text

0

64

128

192

```



The corresponding ranges are:



```text

0 → 63

64 → 127

128 → 191

192 → 255

```



---



## 10. Subnet Boundaries



A subnet boundary is the value where a new subnet begins.



For `/26`:



```text

0

64

128

192

```



These values represent the beginning of the four `/26` subnets inside a `/24` network.



The corresponding ranges are:



```text

0   → 63

64  → 127

128 → 191

192 → 255

```



The value `256` is not a valid IPv4 octet. It represents the point after the last valid value, `255`.



---



## 11. Example — 192.168.10.70/26



```text

IP:

192.168.10.70/26

```



Subnet mask:



```text

255.255.255.192

```



Block size:



```text

256 - 192 = 64

```



Subnet ranges:



```text

0–63

64–127

128–191

192–255

```



The value `70` belongs to:



```text

64–127

```



Therefore:



```text

Network:

192.168.10.64



Broadcast:

192.168.10.127



First Host:

192.168.10.65



Last Host:

192.168.10.126



Usable Hosts:

62

```



---



## 12. Example — 192.168.10.145/28



```text

IP:

192.168.10.145/28

```



Subnet mask:



```text

255.255.255.240

```



Block size:



```text

256 - 240 = 16

```



The subnet ranges include:



```text

0–15

16–31

32–47

48–63

64–79

80–95

96–111

112–127

128–143

144–159

160–175

...

```



The value `145` belongs to:



```text

144–159

```



Therefore:



```text

Network:

192.168.10.144



Broadcast:

192.168.10.159



First Host:

192.168.10.145



Last Host:

192.168.10.158



Usable Hosts:

14

```



---



# 🧪 Experiment 1 — Same Subnet Communication



PC0:



```text

192.168.10.10/26

```



PC1:



```text

192.168.10.20/26

```



PC2:



```text

192.168.10.30/26

```



All three belong to:



```text

192.168.10.0/26

```



Tests were performed from PC0:



```cmd

ping 192.168.10.20

ping 192.168.10.30

```



Both tests succeeded.



This demonstrated direct communication between devices in the same subnet.



---



# 🧪 Experiment 2 — Different Subnet Communication



PC0:



```text

192.168.10.10/26

```



PC3:



```text

192.168.10.70/26

```



Subnet calculation:



```text

PC0

192.168.10.10/26

→ 192.168.10.0/26

```



```text

PC3

192.168.10.70/26

→ 192.168.10.64/26

```



The devices are therefore in different subnets.



Test:



```cmd

ping 192.168.10.70

```



The communication failed because there was no Layer 3 device or default gateway available to provide communication between the two subnets.



---



# 🧪 Experiment 3 — ARP and MAC Learning



After successful local communication, PC0 was checked using:



```cmd

arp -a

```



PC0 learned IP-to-MAC mappings for reachable local hosts.



The switches were also checked using:



```cisco

show mac address-table

```



This demonstrated the relationship between:



```text

ARP Table:

IP → MAC

```



and:



```text

Switch MAC Address Table:

MAC → Port

```



Traffic between devices on the second subnet also demonstrated dynamic MAC learning across the inter-switch link.



---



# 🧪 Experiment 4 — Simulation: Different Subnet Without Gateway



The ARP cache on PC0 was cleared using:



```cmd

arp -d

```



Packet Tracer Simulation Mode was then used with ARP and ICMP enabled.



PC0 attempted:



```cmd

ping 192.168.10.70

```



Packet Tracer identified that:



```text

192.168.10.70

```



was not in the same subnet as PC0.



Because no default gateway was configured, the device dropped the packet.



The decision process was:



```text

Destination IP

&#x20;     ↓

Determine Local or Remote

&#x20;     ↓

Different Subnet

&#x20;     ↓

Need Layer 3 / Gateway

&#x20;     ↓

No Gateway Configured

&#x20;     ↓

Packet Dropped

```



This demonstrated that PC0 does not directly ARP for PC3 when the destination is identified as remote.



---



# 🧪 Experiment 5 — Intentional Failure: Wrong Subnet Mask



A baseline configuration was established first.



PC0:



```text

192.168.10.10/26

```



PC1:



```text

192.168.10.20/26

```



Baseline connectivity:



```text

4 Sent

4 Received

0% Loss

```



The failure was then introduced by changing the subnet mask on PC1 only.



PC1 became:



```text

IP Address:

192.168.10.20



Subnet Mask:

255.255.255.240

```



Therefore:



```text

PC0 = 192.168.10.10/26

PC1 = 192.168.10.20/28

```



---



## Failure Analysis



PC0 using `/26` has:



```text

192.168.10.0/26

```



and considers:



```text

192.168.10.20

```



to be local.



PC1 using `/28` has:



```text

192.168.10.16/28

```



and does not consider:



```text

192.168.10.10

```



to be part of its local subnet.



This created inconsistent subnet interpretations between the two devices.



---



## Failure Testing



From PC0:



```cmd

ping 192.168.10.20

```



Result:



```text

100% Loss

```



From PC1:



```cmd

ping 192.168.10.10

```



Result:



```text

100% Loss

```



---



## Evidence Collection



On PC0:



```cmd

ipconfig

arp -a

```



On PC1:



```cmd

ipconfig

arp -a

```



Observed behavior showed that:



```text

PC0:

192.168.10.10/26

```



considered PC1 local and learned PC1's MAC address.



PC1:



```text

192.168.10.20/28

```



did not have an ARP entry for PC0.



This provided evidence of inconsistent subnet configuration.



---



## 🔍 Root Cause



```text

Incorrect / Inconsistent Subnet Mask on PC1

```



The physical connectivity and Layer 2 switching were operational.



The problem was caused by inconsistent IP subnet configuration.



---



# 🔧 Fix



PC1 was restored to:



```text

IP Address:

192.168.10.20



Subnet Mask:

255.255.255.192

```



which restored:



```text

192.168.10.20/26

```



---



# ✅ Final Verification



Connectivity was tested again in both directions.



From PC0:



```cmd

ping 192.168.10.20

```



From PC1:



```cmd

ping 192.168.10.10

```



Both directions returned successful communication with:



```text

4 Sent

4 Received

0% Loss

```



The troubleshooting workflow was:



```text

Baseline

&#x20;  ↓

Intentional Failure

&#x20;  ↓

Test

&#x20;  ↓

Collect Evidence

&#x20;  ↓

Identify Root Cause

&#x20;  ↓

Fix

&#x20;  ↓

Verify

```



---



# 🧠 Key Takeaways



1. IPv4 addresses contain 32 bits divided into four 8-bit octets.

2. Each IPv4 octet ranges from 0 to 255.

3. CIDR determines how many bits belong to the network portion.

4. The subnet mask separates network bits from host bits.

5. The Network Address identifies the subnet.

6. The Broadcast Address identifies the end of the subnet.

7. Usable hosts are the addresses between the Network and Broadcast addresses.

8. Block Size determines the spacing between subnet boundaries.

9. A subnet boundary is the beginning of a new subnet.

10. Devices can use the same subnet mask while still belonging to different subnets.

11. Same-subnet communication can occur directly at Layer 2.

12. Different subnets require Layer 3 connectivity to communicate.

13. ARP resolves local IPv4 addresses to MAC addresses.

14. Switches use MAC Address Tables to associate MAC addresses with ports.

15. An incorrect subnet mask can cause devices to make inconsistent local and remote decisions.

16. Troubleshooting should use evidence instead of guessing.



---



# 🛠️ Commands Used



## Packet Tracer PCs



```cmd

ipconfig

ipconfig /all

arp -a

arp -d

ping 192.168.10.20

ping 192.168.10.30

ping 192.168.10.70

ping 192.168.10.10

```



## Cisco Switches



```cisco

enable

show mac address-table

```



---



# 📁 Lab Files



```text

LAB-03-IPv4-Addressing-Subnetting/

│

├── LAB-03-IPv4-Addressing-Subnetting.pkt

├── README.md

└── screenshots/

```



---



## ✅ Status



**Completed**



**100-Day IT Infrastructure Journey — LAB 03**
