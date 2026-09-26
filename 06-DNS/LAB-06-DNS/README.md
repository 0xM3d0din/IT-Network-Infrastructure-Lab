\# LAB 06 — DNS



\## 🎯 Objective



Understand the fundamentals of Domain Name System (DNS) and learn how DNS provides name resolution between hostnames and IP addresses.



This lab focuses on:



\- DNS Server vs DNS Client

\- Name Resolution

\- DNS Queries and Responses

\- DNS A Records

\- Forward Lookup

\- DNS Cache Concepts

\- DNS Server Configuration

\- DNS Client Configuration

\- DNS Simulation in Packet Tracer

\- Intentional DNS Service Failure

\- Evidence-Based Troubleshooting

\- Root Cause Identification

\- Recovery and Final Verification



The lab was implemented and tested using Cisco Packet Tracer.



\---



\## 🧩 Topology



```text

DNS-SRV ─┐

&#x20;        │

WEB-SRV ─┼── SW1 ─── PC0

&#x20;        │

```



\### Devices



```text

PC0      → DNS Client



WEB-SRV  → Host to be resolved



DNS-SRV  → DNS Server



SW1      → Layer 2 Switch

```



\---



\## 🌐 Network Configuration



The lab uses a single IPv4 network:



```text

Network:

192.168.10.0/24



Subnet Mask:

255.255.255.0

```



\### Device Addressing



| Device | IP Address | Subnet Mask | DNS Server |

|---|---|---|---|

| DNS-SRV | 192.168.10.2 | 255.255.255.0 | 0.0.0.0 |

| WEB-SRV | 192.168.10.10 | 255.255.255.0 | 0.0.0.0 |

| PC0 | 192.168.10.20 | 255.255.255.0 | 192.168.10.2 |



No Default Gateway was configured because this lab focuses on DNS and all devices are located on the same IPv4 network.



\---



\# 📚 Core Concepts



\## 1. What is DNS?



DNS stands for:



```text

Domain Name System

```



DNS is responsible for \*\*Name Resolution\*\*.



It allows a hostname to be resolved to an IP address.



For example:



```text

webserver.company.local

&#x20;       ↓

DNS

&#x20;       ↓

192.168.10.10

```



This allows users and applications to work with names instead of having to remember IP addresses.



\---



\## 2. DNS Client



A DNS Client is the device that requests name resolution.



In this lab:



```text

PC0

↓

DNS Query

↓

DNS-SRV

```



PC0 was configured to use the DNS Server at:



```text

192.168.10.2

```



\---



\## 3. DNS Server



The DNS Server receives DNS queries and provides DNS responses based on its configured records.



In this lab:



```text

DNS-SRV

IP:

192.168.10.2

```



The DNS service was enabled on the server.



\---



\## 4. DNS Query



A DNS Query is sent by the client when it needs to resolve a hostname.



Example:



```text

PC0:

What is the IP address of

webserver.company.local?

```



The client sends the query to the configured DNS Server.



The DNS Server then processes the request and returns a response.



\---



\## 5. DNS Response



The DNS Response contains the result of the DNS query.



In this lab, the response resolved:



```text

webserver.company.local

&#x20;       ↓

192.168.10.10

```



The DNS traffic observed in Packet Tracer used UDP port `53`.



\---



\# 🧾 DNS A Record



The lab used an \*\*A Record\*\*.



The configured record was:



```text

Name:

webserver.company.local



Type:

A Record



Address:

192.168.10.10

```



An A Record maps a hostname to an IPv4 address.



```text

Hostname

&#x20;  ↓

A Record

&#x20;  ↓

IPv4 Address

```



\---



\# 🔄 Forward Lookup



A Forward Lookup resolves:



```text

Name → IP Address

```



Example:



```text

webserver.company.local

&#x20;       ↓

192.168.10.10

```



This is the main DNS resolution process tested in this lab.



\---



\# 🔁 Reverse Lookup



A Reverse Lookup works in the opposite direction:



```text

IP Address → Name

```



Reverse DNS commonly uses PTR records.



Reverse lookup was covered as a DNS concept but was not implemented as a separate experiment in this lab.



\---



\# 🧠 DNS Cache



DNS clients can temporarily store DNS resolution results in a DNS cache.



Conceptually:



```text

DNS Query

&#x20;  ↓

DNS Response

&#x20;  ↓

Cache

```



Caching can reduce repeated DNS queries and improve response time for previously resolved names.



\---



\# 🧪 Experiment 1 — DNS Server Configuration



The DNS Server was configured with:



```text

IP Address:

192.168.10.2



Subnet Mask:

255.255.255.0

```



The DNS service was then enabled.



The DNS Server was used as the name resolution service for PC0.



\---



\# 🧪 Experiment 2 — DNS A Record Configuration



An A Record was created on the DNS Server:



```text

webserver.company.local

&#x20;       ↓

192.168.10.10

```



This record provides the mapping required for forward DNS resolution.



\---



\# 🧪 Experiment 3 — WEB-SRV Configuration



WEB-SRV was configured with:



```text

IP Address:

192.168.10.10



Subnet Mask:

255.255.255.0

```



The server represents the host that will be resolved through DNS.



\---



\# 🧪 Experiment 4 — PC0 DNS Client Configuration



PC0 was configured with:



```text

IP Address:

192.168.10.20



Subnet Mask:

255.255.255.0



DNS Server:

192.168.10.2

```



This allows PC0 to send DNS queries to DNS-SRV.



\---



\# 🧪 Experiment 5 — IP Connectivity Test



Before testing DNS, basic IP connectivity was verified.



PC0 was tested against WEB-SRV:



```text

ping 192.168.10.10

```



The result was:



```text

4 Sent

4 Received

0% Loss

```



This confirmed that the underlying IP connectivity was working before testing name resolution.



\---



\# 🧪 Experiment 6 — DNS Resolution Test



The hostname was then tested from PC0:



```text

ping webserver.company.local

```



The hostname was successfully resolved to:



```text

192.168.10.10

```



The ping test completed successfully with:



```text

4 Sent

4 Received

0% Loss

```



This demonstrated successful DNS name resolution followed by normal IP communication.



\---



\# 🧪 Experiment 7 — DNS Query Simulation



Packet Tracer Simulation Mode was used to observe the DNS Query.



The observed flow was:



```text

PC0

&#x20;↓

DNS Query

&#x20;↓

SW1

&#x20;↓

DNS-SRV

```



The DNS Query was sent from the DNS Client to the DNS Server.



The observed transport communication used:



```text

UDP

Client Port → 53

```



This provided packet-level evidence of the DNS request.



\---



\# 🧪 Experiment 8 — DNS Response Simulation



After processing the query, the DNS Server returned a DNS Response.



The observed flow was:



```text

DNS-SRV

&#x20;↓

DNS Response

&#x20;↓

SW1

&#x20;↓

PC0

```



The response contained the resolved IP address for:



```text

webserver.company.local

```



The observed communication used:



```text

UDP

53 → Client Port

```



This demonstrated the complete DNS query/response process in Packet Tracer.



\---



\# 💥 Experiment 9 — Intentional Failure: DNS Service Failure



A deliberate failure was introduced by disabling the DNS service on DNS-SRV.



The DNS Service was changed from:



```text

ON

```



to:



```text

OFF

```



The A Record remained configured.



The configured record was still:



```text

webserver.company.local

&#x20;       ↓

192.168.10.10

```



This allowed the failure to isolate the DNS service rather than the DNS record itself.



\---



\# 🔍 Failure Testing



With the DNS service disabled, PC0 attempted:



```text

ping webserver.company.local

```



The hostname could not be resolved.



At the same time, direct IP communication was still successful:



```text

ping 192.168.10.10

```



Result:



```text

4 Sent

4 Received

0% Loss

```



The DNS Server itself was also reachable:



```text

ping 192.168.10.2

```



Result:



```text

4 Sent

4 Received

0% Loss

```



The evidence therefore showed:



```text

PC0 → WEB-SRV by IP

✅ Working



PC0 → DNS-SRV by IP

✅ Working



Hostname Resolution

❌ Failing

```



\---



\# 🔍 Failure Analysis



The available evidence showed:



```text

IP Connectivity

✅ Working



DNS Server Reachability

✅ Working



DNS Service

❌ OFF



Name Resolution

❌ Failing

```



The A Record was still present.



Therefore, the failure was isolated to the DNS service itself.



\---



\# 🔍 Root Cause



```text

The DNS service on DNS-SRV was disabled.

```



The failure path was:



```text

PC0

&#x20;↓

DNS Query

&#x20;↓

DNS Service Unavailable

&#x20;↓

Name Resolution Failed

&#x20;↓

Hostname Ping Failed

```



Direct IP connectivity remained available because the underlying network connection was still operational.



\---



\# 🔧 Fix



The DNS service on DNS-SRV was restored:



```text

DNS Service:

ON

```



The existing A Record was left unchanged.



\---



\# ✅ Final Recovery



After restoring the DNS service, PC0 repeated the test:



```text

ping webserver.company.local

```



The hostname was successfully resolved and the ping completed with:



```text

4 Sent

4 Received

0% Loss

```



This confirmed:



```text

DNS Service

✅ Restored



Name Resolution

✅ Working



Connectivity

✅ Working

```



\---



\# 🧠 Key Takeaways



\- DNS stands for Domain Name System.

\- DNS provides Name Resolution.

\- A DNS Client sends DNS Queries.

\- A DNS Server processes DNS Queries and returns DNS Responses.

\- An A Record maps a hostname to an IPv4 address.

\- Forward Lookup resolves Name → IP.

\- Reverse Lookup resolves IP → Name.

\- DNS commonly uses UDP port 53.

\- DNS and DHCP have different roles.

\- DHCP provides network configuration.

\- DNS provides Name Resolution.

\- The Default Gateway provides a path toward other networks.

\- DNS does not replace the Default Gateway.

\- IP connectivity should be tested separately from DNS resolution during troubleshooting.

\- Successful IP connectivity does not necessarily mean DNS resolution is working.

\- A DNS failure can allow direct IP communication while hostname-based communication fails.

\- Evidence-based troubleshooting helps isolate the failure domain.

\- In this lab, the intentional DNS failure was caused by disabling the DNS service.

\- Restoring the DNS service successfully recovered name resolution and connectivity.



\---



\# 🛠️ Commands Used



\## Packet Tracer PC



```text

ipconfig



ping 192.168.10.10



ping 192.168.10.2



ping webserver.company.local

```



\---



\# 📸 Evidence



The lab contains screenshots documenting:



\- Lab topology

\- DNS Server configuration

\- DNS A Record

\- WEB-SRV configuration

\- PC0 DNS client configuration

\- IP connectivity

\- Successful DNS resolution

\- DNS Query simulation

\- DNS Response simulation

\- DNS service failure

\- DNS resolution failure

\- DNS failure diagnosis

\- Final DNS recovery



\---



\# 📁 Lab Structure



```text

LAB-06-DNS/

│

├── LAB-06-DNS.pkt

├── README.md

└── screenshots/

```



\### Screenshots



```text

DNS-A-Record.png

DNS-Failure-Diagnosis.png

DNS-Final-Recovery.png

DNS-Query-Simulation.png

DNS-Resolution-Failure.png

DNS-Resolution-Success.png

DNS-Response-Simulation.png

DNS-Service-Failure.png

DNS-SRV-Configuration.png

IP-Connectivity-Test.png

PC0-Configuration.png

Topology.png

WEB-SRV-Configuration.png

```



\---



\## ✅ Status



\*\*Completed\*\*



\*\*100-Day IT Infrastructure Journey — LAB 06\*\*



Next:



\*\*LAB 07 — VLANs\*\*
