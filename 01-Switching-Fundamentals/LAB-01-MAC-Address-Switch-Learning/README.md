````markdown

\# LAB 01 — MAC Address \& Switch Learning



\## 🎯 Objective



Understand how a Layer 2 switch learns MAC addresses, stores them in its MAC Address Table, and uses them for Ethernet frame forwarding.



By completing this lab, I learned and practiced:



\- MAC Address

\- Ethernet Frame

\- Source MAC

\- Destination MAC

\- MAC Address Table

\- MAC Address Learning

\- Layer 2 Forwarding

\- Unknown Unicast Flooding

\- Broadcast

\- ARP Table vs MAC Address Table

\- Dynamic MAC Learning

\- Layer 2 Troubleshooting

\- Interface Shutdown and Recovery



\---



\## 🧩 Topology



```text

PC0 -------- SW1 -------- PC1

````



Initial connections:



```text

PC0 Fa0 → SW1 Fa0/1

PC1 Fa0 → SW1 Fa0/2

```



Later, PC1 was moved to:



```text

PC1 Fa0 → SW1 Fa0/3

```



\---



\## 🌐 Network Configuration



| Device | IP Address   | Subnet Mask   | Default Gateway |

| ------ | ------------ | ------------- | --------------- |

| PC0    | 192.168.1.10 | 255.255.255.0 | None            |

| PC1    | 192.168.1.20 | 255.255.255.0 | None            |



Both hosts are on the same subnet, so a default gateway is not required for communication between them.



\---



\## 🔑 MAC Addresses



| Device | MAC Address    | Initial Port |

| ------ | -------------- | ------------ |

| PC0    | 000B.BE7D.BC94 | Fa0/1        |

| PC1    | 0010.114E.72CC | Fa0/2        |



After moving PC1:



```text

PC1 MAC → Fa0/3

```



\---



\## 📚 Theory



\### MAC Address



A MAC Address is a Layer 2 address associated with a network interface and is used by Ethernet for local network communication.



Example:



```text

000B.BE7D.BC94

```



\### IP vs MAC



```text

IP  → Logical Address → Layer 3

MAC → Layer 2 Address → Ethernet

```



\---



\## 📦 Ethernet Frame



An Ethernet Frame contains important Layer 2 information such as:



```text

Source MAC

Destination MAC

Data

```



Example:



```text

Source MAC      = 000B.BE7D.BC94

Destination MAC = 0010.114E.72CC

```



\---



\## 🔀 MAC Address Table



A Layer 2 switch maintains a MAC Address Table that maps:



```text

MAC Address → Switch Port

```



Example:



```text

000B.BE7D.BC94 → Fa0/1

0010.114E.72CC → Fa0/2

```



The switch uses this table to make Layer 2 forwarding decisions.



\---



\## 🧠 MAC Address Learning



The switch learns MAC addresses from the \*\*Source MAC Address\*\* of incoming Ethernet frames.



Example:



```text

Source MAC      = 000B.BE7D.BC94

Incoming Port   = Fa0/1

```



The switch learns:



```text

000B.BE7D.BC94 → Fa0/1

```



The key rule is:



> A switch learns the Source MAC address from an incoming frame and associates it with the incoming port.



\---



\## 🚚 Forwarding



After learning the Source MAC, the switch checks the Destination MAC.



If the destination is known:



```text

Destination MAC

&#x20;       ↓

MAC Address Table

&#x20;       ↓

Correct Port

&#x20;       ↓

Forward

```



Example:



```text

0010.114E.72CC → Fa0/2

```



The switch forwards the frame through Fa0/2.



\---



\## 🌊 Unknown Unicast Flooding



If the Destination MAC is not present in the MAC Address Table, the switch does not know which port contains the destination device.



It therefore floods the frame through the relevant ports in the same Layer 2 domain, except the port where the frame was received.



This is called:



\*\*Unknown Unicast Flooding\*\*



\---



\## 📢 Broadcast



The Ethernet broadcast MAC address is:



```text

FF:FF:FF:FF:FF:FF

```



Broadcast frames are flooded within the same broadcast domain.



ARP Requests are an example of traffic that uses broadcast.



\---



\## 🔄 ARP Table vs MAC Address Table



\### ARP Table



Maintained by the host.



```text

IP Address → MAC Address

```



Example:



```text

192.168.1.20 → 0010.114E.72CC

```



Command:



```cmd

arp -a

```



\### MAC Address Table



Maintained by the switch.



```text

MAC Address → Switch Port

```



Example:



```text

0010.114E.72CC → Fa0/2

```



Command:



```cisco

show mac address-table

```



\---



\## 🧪 Experiment 1 — MAC Learning



The initial MAC Address Table did not contain the MAC addresses of the PCs.



After generating traffic using:



```cmd

ping 192.168.1.20

```



the switch learned:



```text

000B.BE7D.BC94 → Fa0/1

0010.114E.72CC → Fa0/2

```



The entries appeared as dynamic MAC entries.



This demonstrated:



\*\*Dynamic MAC Learning\*\*



\---



\## 🧪 Experiment 2 — MAC Table Reset and Relearning



The dynamic MAC entries were cleared using:



```cisco

clear mac-address-table dynamic

```



After generating traffic again, the switch learned the MAC addresses automatically.



This demonstrated that MAC learning is dynamic and can be rebuilt from network traffic.



\---



\## 🧪 Experiment 3 — MAC Relocation



PC1 was moved from:



```text

Fa0/2

```



to:



```text

Fa0/3

```



PC1 then generated traffic.



The switch learned the new location:



```text

0010.114E.72CC → Fa0/3

```



This demonstrated that the switch associates a MAC address with the port from which it receives traffic.



\---



\## 🚨 Intentional Failure



The following configuration was applied to Fa0/3:



```cisco

enable

configure terminal

interface fa0/3

shutdown

end

```



The interface became:



```text

Administratively Down

```



The connectivity test failed:



```cmd

ping 192.168.1.20

```



\---



\## 🔍 Troubleshooting



The problem was investigated using:



```cisco

show interfaces status

```



and:



```cisco

show interfaces fa0/3

```



The interface was identified as:



```text

Fa0/3

Administratively Down

```



The issue was therefore an administrative shutdown of the switch port, not an IP or subnet configuration problem.



\---



\## 🔧 Fix



The interface was restored using:



```cisco

enable

configure terminal

interface fa0/3

no shutdown

end

```



After recovery, Fa0/3 returned to an operational state.



\---



\## ✅ Verification



Connectivity was restored successfully:



```cmd

ping 192.168.1.20

```



The final interface state showed:



```text

Fa0/1 → connected

Fa0/3 → connected

```



The final MAC Address Table showed:



```text

000B.BE7D.BC94 → Fa0/1

0010.114E.72CC → Fa0/3

```



\---



\## 🛠️ Commands Used



\### PC



```cmd

ipconfig /all

ping 192.168.1.20

arp -a

```



\### Switch



```cisco

enable

show mac address-table

show mac address-table dynamic

show interfaces status

show interfaces fa0/3

configure terminal

interface fa0/3

shutdown

no shutdown

end

clear mac-address-table dynamic

```



\---



\## 🧠 Key Takeaways



1\. A switch learns MAC addresses from the Source MAC of incoming Ethernet frames.

2\. The MAC Address Table maps MAC addresses to switch ports.

3\. The Destination MAC is used for Layer 2 forwarding decisions.

4\. Known destinations are forwarded to the appropriate port.

5\. Unknown unicast destinations are flooded within the relevant Layer 2 domain.

6\. Broadcast frames are flooded within the broadcast domain.

7\. ARP maps IP addresses to MAC addresses.

8\. A switch MAC Address Table maps MAC addresses to switch ports.

9\. MAC learning is dynamic.

10\. A device can be relearned on a different switch port after it moves.

11\. An administratively shut down interface can cause connectivity failure even when IP configuration is correct.



\---



\## 📸 Screenshots



\### Topology



!\[Topology](screenshots/topology.png)



\### PC0 MAC Address



!\[PC0 MAC](screenshots/pc0-mac.png)



\### PC1 MAC Address



!\[PC1 MAC](screenshots/pc1-mac.png)



\### MAC Table Before Traffic



!\[MAC Table Before](screenshots/mac-table-before.png)



\### MAC Table After Traffic



!\[MAC Table After](screenshots/mac-table-after.png)



\### Intentional Failure



!\[Intentional Failure](screenshots/intentional-failure.png)



\### Final Interface Status



!\[Final Interface Status](screenshots/final-interface-status.png)



\---



\## 📁 Lab Files



\* `LAB-01-MAC-Address-Switch-Learning.pkt`

\* `README.md`

\* `screenshots/`



\---



\## ✅ Status



\*\*Completed\*\*



\*\*100-Day IT Infrastructure Journey — Day 1\*\*



```

```



