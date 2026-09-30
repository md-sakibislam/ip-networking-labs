\# Lab 4 — Layer-3 Switching



\## Objective



The objective of this lab is to configure and verify inter-VLAN routing using a Cisco multilayer Layer-3 switch.



This lab demonstrates:



\* VLAN configuration

\* Access ports

\* Switched Virtual Interfaces (SVIs)

\* Layer-3 switching

\* `ip routing`

\* Inter-VLAN communication

\* Verification using Cisco IOS commands and ICMP ping



The main difference from the previous Router-on-a-Stick lab is that the multilayer switch itself performs the Layer-3 routing.



---



\## Platform



\*\*Cisco Packet Tracer\*\*



\### Devices



\* 1 × Cisco 3560 Multilayer Switch

\* 2 × PCs



---



\## Topology



```text

&nbsp;                 MLS1

&nbsp;         Cisco 3560 L3 Switch

&nbsp;            /            \\

&nbsp;           /              \\

&nbsp;       Fa0/1             Fa0/2

&nbsp;         |                 |

&nbsp;      VLAN 10           VLAN 20

&nbsp;         |                 |

&nbsp;        PC1               PC2

```



---



\## Connections



| Device | Interface | Connected To |

| ------ | --------- | ------------ |

| PC1    | NIC       | MLS1 Fa0/1   |

| PC2    | NIC       | MLS1 Fa0/2   |



---



\## VLAN Configuration



| VLAN | Name      | Network         | Gateway      |

| ---- | --------- | --------------- | ------------ |

| 10   | sales     | 192.168.10.0/24 | 192.168.10.1 |

| 20   | marketing | 192.168.20.0/24 | 192.168.20.1 |



---



\## IP Addressing



| Device | Interface   | IP Address    | Subnet Mask   | VLAN |

| ------ | ----------- | ------------- | ------------- | ---- |

| PC1    | NIC         | 192.168.10.10 | 255.255.255.0 | 10   |

| PC2    | NIC         | 192.168.20.10 | 255.255.255.0 | 20   |

| MLS1   | VLAN 10 SVI | 192.168.10.1  | 255.255.255.0 | 10   |

| MLS1   | VLAN 20 SVI | 192.168.20.1  | 255.255.255.0 | 20   |



\### Default Gateways



PC1:



```text

192.168.10.1

```



PC2:



```text

192.168.20.1

```



---



\## Switch Configuration



\### Create VLANs



```text

vlan 10

&nbsp;name sales



vlan 20

&nbsp;name marketing

```



\### Configure PC1 access port



```text

interface fa0/1

&nbsp;switchport mode access

&nbsp;switchport access vlan 10

```



\### Configure PC2 access port



```text

interface fa0/2

&nbsp;switchport mode access

&nbsp;switchport access vlan 20

```



---



\## Configure SVIs



A \*\*Switched Virtual Interface (SVI)\*\* is a virtual Layer-3 interface associated with a VLAN.



\### VLAN 10 SVI



```text

interface vlan 10

&nbsp;ip address 192.168.10.1 255.255.255.0

&nbsp;no shutdown

```



\### VLAN 20 SVI



```text

interface vlan 20

&nbsp;ip address 192.168.20.1 255.255.255.0

&nbsp;no shutdown

```



The SVIs act as the default gateways for their respective VLANs.



```text

VLAN 10 → 192.168.10.1

VLAN 20 → 192.168.20.1

```



---



\## Enable Layer-3 Routing



The multilayer switch must be instructed to perform IP routing:



```text

ip routing

```



Without `ip routing`, the switch will not perform routing between the VLANs.



---



\## How Layer-3 Switching Works



In the previous Router-on-a-Stick lab, routing was performed by an external router.



The architecture was:



```text

PC

&nbsp;|

L2 Switch

&nbsp;|

802.1Q Trunk

&nbsp;|

Router

&nbsp;|

Inter-VLAN Routing

```



In this lab, the multilayer switch performs the routing itself:



```text

PC1

&nbsp;|

VLAN 10

&nbsp;|

MLS1

&nbsp;|

Layer-3 Routing

&nbsp;|

VLAN 20

&nbsp;|

PC2

```



There is no external router in this topology.



---



\## Packet Flow: VLAN 10 to VLAN 20



PC1:



```text

192.168.10.10

```



needs to communicate with PC2:



```text

192.168.20.10

```



\### Step 1 — PC1 checks the destination



PC1 determines that `192.168.20.10` is outside its local `192.168.10.0/24` network.



Therefore, it sends the traffic to its default gateway:



```text

192.168.10.1

```



\### Step 2 — Traffic reaches MLS1



PC1 is connected to Fa0/1, which belongs to VLAN 10.



The traffic therefore reaches the VLAN 10 SVI:



```text

VLAN 10 SVI

192.168.10.1

```



\### Step 3 — MLS1 checks its routing table



MLS1 has the following connected networks:



```text

192.168.10.0/24 → VLAN 10

192.168.20.0/24 → VLAN 20

```



Because `192.168.20.0/24` is directly connected through VLAN 20, MLS1 routes the packet toward VLAN 20.



\### Step 4 — Traffic reaches PC2



MLS1 forwards the traffic through VLAN 20 toward PC2:



```text

PC1

192.168.10.10

&nbsp;    |

&nbsp;VLAN 10

&nbsp;    |

&nbsp;  MLS1

&nbsp;    |

&nbsp; Routing

&nbsp;    |

&nbsp;VLAN 20

&nbsp;    |

&nbsp;  PC2

192.168.20.10

```



---



\## Verification



\### 1. Verify VLANs



Command:



```text

show vlan brief

```



Verify that:



```text

VLAN 10 → Fa0/1

VLAN 20 → Fa0/2

```



---



\### 2. Verify SVIs



Command:



```text

show ip interface brief

```



The important interfaces should show:



```text

Vlan10   192.168.10.1   up   up

Vlan20   192.168.20.1   up   up

```



---



\### 3. Verify the routing table



Command:



```text

show ip route

```



The routing table should contain connected routes similar to:



```text

C    192.168.10.0/24 is directly connected, Vlan10

C    192.168.20.0/24 is directly connected, Vlan20

```



`C` means the network is directly connected.



---



\### 4. Verify Layer-3 routing



The command:



```text

show running-config

```



should show:



```text

ip routing

```



This confirms that Layer-3 routing has been enabled on the multilayer switch.



---



\## Connectivity Tests



\### PC1 → PC2



From PC1:



```text

ping 192.168.20.10

```



\*\*Result: Successful\*\*



\### PC2 → PC1



From PC2:



```text

ping 192.168.10.10

```



\*\*Result: Successful\*\*



Successful cross-VLAN pings confirm that MLS1 is routing traffic between VLAN 10 and VLAN 20.



---



\## Lab 3 vs Lab 4



| Feature          | Lab 3: Router-on-a-Stick | Lab 4: Layer-3 Switching      |

| ---------------- | ------------------------ | ----------------------------- |

| Routing device   | External router          | Multilayer switch             |

| Gateway          | Router subinterface      | SVI                           |

| VLAN 10 gateway  | G0/0.10                  | VLAN 10 SVI                   |

| VLAN 20 gateway  | G0/0.20                  | VLAN 20 SVI                   |

| Router trunk     | Required                 | Not required in this topology |

| Routing command  | Router performs routing  | `ip routing`                  |

| Routing location | External router          | Inside MLS1                   |



\### Router-on-a-Stick



```text

PC

&nbsp;|

L2 Switch

&nbsp;|

802.1Q Trunk

&nbsp;|

Router

&nbsp;|

Routing

```



\### Layer-3 Switching



```text

PC

&nbsp;|

Multilayer Switch

&nbsp;|

Routing

```



---



\## What I Learned



\* A multilayer switch can perform both Layer-2 switching and Layer-3 routing.

\* An SVI provides a Layer-3 interface for a VLAN.

\* An SVI can serve as the default gateway for hosts in that VLAN.

\* `ip routing` enables Layer-3 routing on a multilayer switch.

\* Inter-VLAN routing does not require an external router when a Layer-3 switch is used.

\* The routing table shows which networks are directly connected.

\* Layer-3 switching is useful when routing between VLANs needs to be performed directly by the switch.



---



\## Troubleshooting Checklist



If inter-VLAN communication fails, check:



1\. PC IP addresses

2\. PC subnet masks

3\. PC default gateways

4\. VLAN assignment of the access ports

5\. SVI IP addresses

6\. SVI status is `up/up`

7\. `ip routing` is enabled

8\. Both VLANs exist

9\. The routing table contains both networks



Useful commands:



```text

show vlan brief

show ip interface brief

show ip route

show running-config

```



---



\## Evidence



The following files contain the lab evidence:



```text

verification/

├── show-vlan-brief.txt

├── show-ip-interface-brief.txt

├── show-ip-route.txt

└── ping-results.txt

```



The Packet Tracer project is saved as:



```text

04-layer-3-switching.pkt

```



---



\## Lab Status



\*\*Completed\*\*



\* \[x] VLAN 10 configured

\* \[x] VLAN 20 configured

\* \[x] Access ports configured

\* \[x] VLAN 10 SVI configured

\* \[x] VLAN 20 SVI configured

\* \[x] `ip routing` enabled

\* \[x] Routing table verified

\* \[x] Inter-VLAN routing verified

\* \[x] Cross-VLAN ping successful



