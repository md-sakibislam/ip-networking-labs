\# Lab 3 — Inter-VLAN Routing with Router-on-a-Stick



\## Objective



The objective of this lab is to configure and verify inter-VLAN routing using the Router-on-a-Stick method.



This lab demonstrates:



\* VLAN 10 and VLAN 20

\* Access ports

\* 802.1Q trunking

\* Router subinterfaces

\* VLAN default gateways

\* Inter-VLAN routing

\* Verification using Cisco IOS commands and ICMP ping



The main goal is to allow hosts in VLAN 10 to communicate with hosts in VLAN 20 through a Layer-3 router.



---



\## Platform



\*\*Cisco Packet Tracer\*\*



\### Devices



\* 1 × Cisco 1941 Router

\* 1 × Cisco 2960 Switch

\* 2 × PCs



---



\## Topology



```text

&nbsp;             R1

&nbsp;            G0/0

&nbsp;              |

&nbsp;              | 802.1Q Trunk

&nbsp;              |

&nbsp;            SW1

&nbsp;           /   \\

&nbsp;       Fa0/1   Fa0/2

&nbsp;         |       |

&nbsp;        PC1     PC2

&nbsp;      VLAN 10  VLAN 20

```



\### Connections



| Device | Interface | Connected To |

| ------ | --------- | ------------ |

| PC1    | NIC       | SW1 Fa0/1    |

| PC2    | NIC       | SW1 Fa0/2    |

| SW1    | Fa0/24    | R1 G0/0      |



---



\## VLAN Configuration



| VLAN | Name      | Network         | Gateway      |

| ---- | --------- | --------------- | ------------ |

| 10   | sales     | 192.168.10.0/24 | 192.168.10.1 |

| 20   | marketing | 192.168.20.0/24 | 192.168.20.1 |



---



\## IP Addressing



| Device | Interface | IP Address    | Subnet Mask   | VLAN |

| ------ | --------- | ------------- | ------------- | ---- |

| PC1    | NIC       | 192.168.10.10 | 255.255.255.0 | 10   |

| PC2    | NIC       | 192.168.20.10 | 255.255.255.0 | 20   |

| R1     | G0/0.10   | 192.168.10.1  | 255.255.255.0 | 10   |

| R1     | G0/0.20   | 192.168.20.1  | 255.255.255.0 | 20   |



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



\### Configure router-facing trunk



```text

interface fa0/24

&nbsp;switchport mode trunk

```



The Fa0/24 link carries VLAN 10 and VLAN 20 between SW1 and R1 using 802.1Q tagging.



---



\## Router Configuration



The physical G0/0 interface is used as the trunk connection to SW1.



```text

interface gigabitEthernet 0/0

&nbsp;no shutdown

```



\### VLAN 10 subinterface



```text

interface gigabitEthernet 0/0.10

&nbsp;encapsulation dot1Q 10

&nbsp;ip address 192.168.10.1 255.255.255.0

```



\### VLAN 20 subinterface



```text

interface gigabitEthernet 0/0.20

&nbsp;encapsulation dot1Q 20

&nbsp;ip address 192.168.20.1 255.255.255.0

```



---



\## How Router-on-a-Stick Works



Router-on-a-Stick uses one physical router interface with multiple logical subinterfaces.



In this lab:



```text

G0/0.10 → VLAN 10 → 192.168.10.1

G0/0.20 → VLAN 20 → 192.168.20.1

```



The switch-to-router link is configured as an 802.1Q trunk so that traffic from multiple VLANs can travel over the same physical link.



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



PC1 determines that `192.168.20.10` is outside its local `192.168.10.0/24` subnet.



Therefore, PC1 sends the traffic to its default gateway:



```text

192.168.10.1

```



\### Step 2 — SW1 receives the frame



PC1 is connected to an access port belonging to VLAN 10.



SW1 therefore handles the traffic as VLAN 10 traffic.



\### Step 3 — SW1 sends traffic through the trunk



The SW1 Fa0/24 interface is a trunk.



The VLAN information is carried using 802.1Q tagging toward R1.



\### Step 4 — R1 receives the traffic



R1 receives the VLAN 10 traffic through:



```text

G0/0.10

```



R1 has the following connected networks:



```text

192.168.10.0/24 → G0/0.10

192.168.20.0/24 → G0/0.20

```



The router therefore routes the packet toward G0/0.20.



\### Step 5 — Traffic enters VLAN 20



R1 sends the traffic toward VLAN 20 through:



```text

G0/0.20

```



The traffic travels back through the trunk to SW1.



\### Step 6 — SW1 forwards to PC2



SW1 identifies PC2 on its VLAN 20 access port and forwards the frame to PC2.



Therefore:



```text

PC1

192.168.10.10

&nbsp;    |

&nbsp;VLAN 10

&nbsp;    |

&nbsp;   SW1

&nbsp;    |

&nbsp; 802.1Q

&nbsp;  trunk

&nbsp;    |

&nbsp;   R1

&nbsp;    |

&nbsp; routing

&nbsp;    |

&nbsp; VLAN 20

&nbsp;    |

&nbsp;   SW1

&nbsp;    |

&nbsp;  PC2

192.168.20.10

```



---



\## Verification



\### 1. Verify the trunk



Command:



```text

show interfaces trunk

```



Expected result:



```text

Fa0/24

Status: trunking

Encapsulation: 802.1q

```



The verified output showed VLANs 1, 10, and 20 active on the trunk.



---



\### 2. Verify router interfaces



Command:



```text

show ip interface brief

```



Important interfaces:



```text

GigabitEthernet0/0.10  192.168.10.1  up  up

GigabitEthernet0/0.20  192.168.20.1  up  up

```



Both router subinterfaces were verified as operational.



---



\### 3. Verify the routing table



Command:



```text

show ip route

```



The routing table showed:



```text

C 192.168.10.0/24 is directly connected, GigabitEthernet0/0.10

C 192.168.20.0/24 is directly connected, GigabitEthernet0/0.20

```



`C` means the networks are directly connected.



---



\## Connectivity Test



\### PC1 → PC2



```text

ping 192.168.20.10

```



\*\*Result: Successful\*\*



\### PC2 → PC1



```text

ping 192.168.10.10

```



\*\*Result: Successful\*\*



This confirms that hosts in VLAN 10 and VLAN 20 can communicate through the router.



---



\## What I Learned



\* VLANs provide Layer-2 segmentation.

\* Different VLANs are separate broadcast domains.

\* A Layer-3 device is required for communication between VLANs.

\* A default gateway provides the next-hop address for traffic leaving a local subnet.

\* Router-on-a-Stick allows one physical router interface to route between multiple VLANs.

\* Router subinterfaces are associated with VLANs using `encapsulation dot1Q`.

\* A switch-router link must be configured as a trunk when carrying multiple VLANs.

\* The router's routing table determines which subinterface should be used to reach the destination network.

\* `show interfaces trunk`, `show ip interface brief`, and `show ip route` are useful verification commands.



---



\## Troubleshooting Checklist



If inter-VLAN communication fails, check:



1\. PC IP addresses

2\. PC subnet masks

3\. PC default gateways

4\. Correct VLAN assignment on switch access ports

5\. Switch-router link configured as a trunk

6\. Correct VLAN ID in `encapsulation dot1Q`

7\. Correct IP address on each router subinterface

8\. Physical router interface is not administratively down

9\. Router subinterfaces show `up/up`

10\. Router routing table contains both VLAN networks



Useful commands:



```text

show vlan brief

show interfaces trunk

show ip interface brief

show ip route

```



---



\## Evidence



The following files contain the actual lab evidence:



```text

verification/

├── show-interfaces-trunk.txt

├── show-ip-interface-brief.txt

├── show-ip-route.txt

└── ping-results.txt

```



The Packet Tracer project is saved as:



```text

03-inter-vlan-routing.pkt

```



---



\## Lab Status



\*\*Completed\*\*



\* \[x] VLAN 10 configured

\* \[x] VLAN 20 configured

\* \[x] Access ports configured

\* \[x] 802.1Q trunk configured

\* \[x] Router subinterfaces configured

\* \[x] Default gateways configured

\* \[x] Inter-VLAN routing configured

\* \[x] Routing table verified

\* \[x] Cross-VLAN connectivity verified

\* \[x] Ping successful



