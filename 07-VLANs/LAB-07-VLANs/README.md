\# LAB 07 — VLANs



\## 🎯 Objective



Understand how Virtual LANs (VLANs) provide logical Layer 2 segmentation on a switch and create separate broadcast domains for different departments within a company.



This lab focuses on:



\- VLAN Fundamentals

\- Layer 2 Segmentation

\- Broadcast Domains

\- VLAN IDs

\- Access Ports

\- VLAN Membership

\- VLAN Creation

\- Port Assignment

\- VLAN Verification

\- Same VLAN Communication

\- Different VLAN Isolation

\- Evidence-Based Verification



The lab was implemented and tested using Cisco Packet Tracer.



\---



\## 🏢 Company Scenario



The network represents a company with multiple departments connected to a single Layer 2 switch.



The departments are logically separated using VLANs:



| Department | VLAN | Network |

|---|---:|---|

| Administration | 10 | 192.168.10.0/24 |

| HR | 20 | 192.168.10.0/24 |

| Finance | 30 | 192.168.10.0/24 |

| IT | 40 | 192.168.10.0/24 |

| Sales | 50 | 192.168.10.0/24 |



For this lab, all devices intentionally remain in the same IPv4 subnet.



The purpose is to isolate the departments using \*\*Layer 2 VLAN segmentation\*\* and observe the effect of VLAN membership independently from Layer 3 subnet separation.



\---



\## 🧩 Topology



```text

&#x20;                        SW1

&#x20;       ┌────────────────┼────────────────┐

&#x20;       │                │                │

&#x20;  Administration       HR            Finance

&#x20;  PC01 / PC02      PC01 / PC02      PC01 / PC02

&#x20;       │                │                │

&#x20;       └────────────────┼────────────────┘

&#x20;                        │

&#x20;                   IT / Sales

&#x20;                 PC01 / PC02

```



\### Devices



```text

SW1 — Cisco 2960



Administration:

Admin-PC01

Admin-PC02



HR:

HR-PC01

HR-PC02



Finance:

Finance-PC01

Finance-PC02



IT:

IT-PC01

IT-PC02



Sales:

Sales-PC01

Sales-PC02

```



All endpoint devices are connected directly to SW1 using Ethernet connections.



No router or Layer 3 switch was used in this lab.



\---



\# 🌐 IP Addressing



All devices use the same IPv4 network:



```text

Network:

192.168.10.0/24



Subnet Mask:

255.255.255.0

```



\### Endpoint Addressing



```text

Administration

Admin-PC01 → 192.168.10.10

Admin-PC02 → 192.168.10.11



HR

HR-PC01 → 192.168.10.12

HR-PC02 → 192.168.10.13



Finance

Finance-PC01 → 192.168.10.14

Finance-PC02 → 192.168.10.15



IT

IT-PC01 → 192.168.10.16

IT-PC02 → 192.168.10.17



Sales

Sales-PC01 → 192.168.10.18

Sales-PC02 → 192.168.10.19

```



The devices were not assigned a Default Gateway because the lab does not include inter-network routing.



\---



\# 📚 Core Concepts



\## 1. What is a VLAN?



VLAN stands for:



```text

Virtual Local Area Network

```



A VLAN allows a physical switch to be divided into multiple logical networks at Layer 2.



Instead of treating every connected device as part of one broadcast domain, the switch can separate devices into different VLANs.



Example:



```text

VLAN 10 → Administration

VLAN 20 → HR

VLAN 30 → Finance

VLAN 40 → IT

VLAN 50 → Sales

```



\---



\## 2. Why Use VLANs?



In a company environment, different departments may need logical separation even when they share the same physical switching infrastructure.



For example:



```text

Administration

HR

Finance

IT

Sales

```



can all use the same physical switch while remaining separated into different Layer 2 broadcast domains.



This improves logical organization and provides a foundation for later access-control and routing policies.



\---



\## 3. VLAN and Broadcast Domain



A VLAN represents a separate Layer 2 broadcast domain.



Conceptually:



```text

VLAN 10

&#x20;  ↓

Broadcast Domain 10

```



and:



```text

VLAN 20

&#x20;  ↓

Broadcast Domain 20

```



A broadcast generated inside VLAN 10 does not normally cross into VLAN 20 through a Layer 2 switch.



Therefore:



```text

VLAN 10 ≠ VLAN 20

```



\---



\## 4. VLAN ID



Each VLAN is identified by a VLAN ID.



This lab uses:



```text

VLAN 10 → Administration

VLAN 20 → HR

VLAN 30 → Finance

VLAN 40 → IT

VLAN 50 → Sales

```



The VLAN ID allows the switch to identify which logical broadcast domain a port belongs to.



\---



\## 5. Access Port



An Access Port is a switch port configured to belong to a single VLAN for endpoint connectivity.



Example:



```text

Fa0/1

&#x20;  ↓

Access Port

&#x20;  ↓

VLAN 10

```



The endpoint connected to that port becomes a member of the assigned VLAN.



\---



\## 6. VLAN vs Subnet



VLAN and subnet are related but they are not the same concept.



```text

VLAN

→ Layer 2 segmentation



Subnet

→ Layer 3 IP network

```



In this lab, all devices intentionally use:



```text

192.168.10.0/24

```



while VLANs are used to provide Layer 2 separation.



This allows the lab to demonstrate the effect of VLAN segmentation without introducing different IP networks.



\---



\# 🧪 Experiment 1 — Baseline Connectivity



Before VLAN configuration, the devices were connected to the switch without departmental VLAN separation.



A cross-department connectivity test was performed.



Example:



```text

Admin-PC02

&#x20;     ↓

HR-PC01

```



The ping was successful:



```text

4 Sent

4 Received

0% Loss

```



This demonstrated that before VLAN segmentation, devices from different departments could communicate while using the same network and the default switch VLAN configuration.



\---



\# 🧪 Experiment 2 — VLAN Creation



The required VLANs were created on SW1:



```text

VLAN 10 → ADMINISTRATION



VLAN 20 → HR



VLAN 30 → FINANCE



VLAN 40 → IT



VLAN 50 → SALES

```



The VLAN names were configured to reflect the corresponding company departments.



\---



\# 🧪 Experiment 3 — Access Port Assignment



The endpoint ports were assigned to their corresponding VLANs.



\### Administration



```text

Fa0/1 → VLAN 10

Fa0/2 → VLAN 10

```



\### HR



```text

Fa0/3 → VLAN 20

Fa0/4 → VLAN 20

```



\### Finance



```text

Fa0/5 → VLAN 30

Fa0/6 → VLAN 30

```



\### IT



```text

Fa0/7 → VLAN 40

Fa0/8 → VLAN 40

```



\### Sales



```text

Fa0/9 → VLAN 50

Fa0/10 → VLAN 50

```



Each endpoint port was configured as an Access Port.



\---



\# 🧪 Experiment 4 — VLAN Verification



The switch configuration was verified using:



```text

show vlan brief

```



The verification showed:



```text

VLAN 10 → ADMINISTRATION → Fa0/1, Fa0/2



VLAN 20 → HR → Fa0/3, Fa0/4



VLAN 30 → FINANCE → Fa0/5, Fa0/6



VLAN 40 → IT → Fa0/7, Fa0/8



VLAN 50 → SALES → Fa0/9, Fa0/10

```



This confirmed that the endpoint ports were assigned to the intended VLANs.



\---



\# 🧪 Experiment 5 — VLAN Isolation Test



After VLAN configuration, connectivity was tested again.



\### Same VLAN



Administration devices remained in:



```text

VLAN 10

```



The communication between the two Administration PCs remained successful:



```text

Same VLAN

&#x20;   ↓

Layer 2 Communication

&#x20;   ↓

Ping Successful

```



\### Different VLAN



A cross-department test was then performed:



```text

Administration

VLAN 10

&#x20;       ↓

&#x20;       ❌

&#x20;       ↓

HR

VLAN 20

```



The communication failed with:



```text

4 Sent

0 Received

100% Loss

```



This occurred even though both devices were using addresses from the same IPv4 subnet.



\---



\# 🔍 Analysis



The test demonstrated an important distinction:



```text

Same IPv4 Subnet

&#x20;       +

Different VLAN

&#x20;       ↓

Layer 2 Isolation

```



The switch did not provide direct Layer 2 communication between different VLANs.



The communication behavior therefore changed after VLAN segmentation:



```text

Before VLAN Assignment

→ Cross-department communication ✅



After VLAN Assignment

→ Cross-VLAN Layer 2 communication ❌

```



\---



\# 🧠 VLAN Communication Model



The basic behavior observed in this lab can be summarized as:



```text

Same VLAN

&#x20;    ↓

Same Layer 2 Broadcast Domain

&#x20;    ↓

Direct Layer 2 Communication

```



While:



```text

Different VLANs

&#x20;    ↓

Different Layer 2 Broadcast Domains

&#x20;    ↓

No Direct Layer 2 Communication

```



To enable communication between different VLANs, a Layer 3 device is required.



Examples include:



```text

Router

Layer 3 Switch

```



Inter-VLAN routing will be studied later as a separate lab.



\---



\# 🛠️ Commands Used



\## Create VLANs



```text

enable



configure terminal



vlan 10

name ADMINISTRATION



vlan 20

name HR



vlan 30

name FINANCE



vlan 40

name IT



vlan 50

name SALES



end

```



\## Configure Access Ports



Example for Administration:



```text

enable



configure terminal



interface range fastEthernet 0/1 - 2

switchport mode access

switchport access vlan 10



end

```



The same configuration method was used for the other departmental port ranges with their corresponding VLAN IDs.



\## Verify VLAN Configuration



```text

show vlan brief

```



\---



\# 📸 Evidence



The lab contains screenshots documenting:



\- Company topology and department segmentation

\- Baseline cross-department connectivity

\- VLAN creation and port assignment verification

\- VLAN isolation after configuration



\---



\# 📁 Lab Structure



```text

LAB-07-VLANs/

│

├── LAB-07-VLANs.pkt

├── README.md

└── screenshots/

```



\### Screenshots



```text

Baseline-Cross-Department.png

Topology.png

VLAN-Assignment-Verification.png

VLAN-Isolation-Test.png

```



\---



\## ✅ Status



\*\*Completed\*\*



\*\*100-Day IT Infrastructure Journey — LAB 07\*\*



Next:



\*\*LAB 08 — Trunking\*\*
