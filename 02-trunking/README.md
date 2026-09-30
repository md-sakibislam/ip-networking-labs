\# Lab 2 — Trunking and 802.1Q



\## Objective



Extend the VLAN topology from Lab 1 by connecting two Cisco switches with an 802.1Q trunk.



This lab demonstrates:



\* Trunk ports

\* 802.1Q

\* VLAN traffic between switches

\* Access ports vs trunk ports

\* Carrying multiple VLANs over a single physical link

\* Same-VLAN communication across multiple switches

\* Isolation between different VLANs



\## Platform



\* Cisco Packet Tracer

\* 2 × Cisco 2960 switches

\* 4 × PCs



\## Topology



```text

PC1 ---------------- SW1 Fa0/1

&nbsp;                        |

PC2 ---------------- SW1 Fa0/2

&nbsp;                        |

&nbsp;                   Fa0/24

&nbsp;                        ||

&nbsp;                        || 802.1Q TRUNK

&nbsp;                        ||

&nbsp;                   Fa0/24

&nbsp;                        |

&nbsp;                        |

PC3 ---------------- SW2 Fa0/1

PC4 ---------------- SW2 Fa0/2

```



\### VLAN Layout



```text

VLAN 10:



PC1 -------- SW1 ======== SW2 -------- PC3

&nbsp;                TRUNK





VLAN 20:



PC2 -------- SW1 ======== SW2 -------- PC4

&nbsp;                TRUNK

```



The same physical trunk link carries traffic for both VLAN 10 and VLAN 20.



\## IP Addressing



| Device | IP Address    | Subnet Mask   | VLAN |

| ------ | ------------- | ------------- | ---- |

| PC1    | 192.168.10.10 | 255.255.255.0 | 10   |

| PC2    | 192.168.20.10 | 255.255.255.0 | 20   |

| PC3    | 192.168.10.30 | 255.255.255.0 | 10   |

| PC4    | 192.168.20.30 | 255.255.255.0 | 20   |



No default gateway is configured because this lab does not contain a router or Layer-3 switch.



\## VLAN Configuration



Both switches contain:



| VLAN | Name      |

| ---- | --------- |

| 10   | sales     |

| 20   | marketing |



\### SW1



| Port   | Type   | VLAN   |

| ------ | ------ | ------ |

| Fa0/1  | Access | 10     |

| Fa0/2  | Access | 20     |

| Fa0/24 | Trunk  | 10, 20 |



\### SW2



| Port   | Type   | VLAN   |

| ------ | ------ | ------ |

| Fa0/1  | Access | 10     |

| Fa0/2  | Access | 20     |

| Fa0/24 | Trunk  | 10, 20 |



\## Configuration



\### SW1 — VLANs



```text

enable

configure terminal



vlan 10

name sales

exit



vlan 20

name marketing

exit

```



\### SW1 — Access Ports



```text

interface fa0/1

switchport mode access

switchport access vlan 10

exit



interface fa0/2

switchport mode access

switchport access vlan 20

exit

```



\### SW1 — Trunk Port



```text

interface fa0/24

switchport mode trunk

exit

```



\### SW2 — VLANs



```text

enable

configure terminal



vlan 10

name sales

exit



vlan 20

name marketing

exit

```



\### SW2 — Access Ports



```text

interface fa0/1

switchport mode access

switchport access vlan 10

exit



interface fa0/2

switchport mode access

switchport access vlan 20

exit

```



\### SW2 — Trunk Port



```text

interface fa0/24

switchport mode trunk

exit

```



\## How the Trunk Works



An access port normally carries traffic for a single VLAN.



A trunk port can carry traffic for multiple VLANs between network devices.



In this lab, the link between SW1 and SW2 carries:



```text

VLAN 10 traffic

&nbsp;      +

VLAN 20 traffic

```



using 802.1Q VLAN tagging.



The trunk therefore allows VLAN 10 to extend from SW1 to SW2 and VLAN 20 to extend from SW1 to SW2.



\## Verification



The trunk was verified using:



```text

show interfaces trunk

```



The expected result is that Fa0/24 is shown as:



```text

trunking

802.1q

```



on both SW1 and SW2.



VLAN membership was verified using:



```text

show vlan brief

```



\## Connectivity Tests



\### Test 1 — VLAN 10 across the trunk



```text

PC1 → PC3

192.168.10.10 → 192.168.10.30

```



Result:



```text

SUCCESS

```



This proves that VLAN 10 traffic successfully crosses the trunk between SW1 and SW2.



\### Test 2 — VLAN 20 across the trunk



```text

PC2 → PC4

192.168.20.10 → 192.168.20.30

```



Result:



```text

SUCCESS

```



This proves that VLAN 20 traffic successfully crosses the trunk between SW1 and SW2.



\### Test 3 — Different VLANs



```text

PC1 → PC2

192.168.10.10 → 192.168.20.10

```



Result:



```text

FAIL / NO REPLY

```



This is expected because VLAN 10 and VLAN 20 are separate Layer-2 broadcast domains and no Layer-3 routing is configured.



\## Access Port vs Trunk Port



\### Access Port



An access port is assigned to a single VLAN and is normally used for end devices such as PCs.



Example:



```text

PC1 ─── Access Port ─── SW1

&nbsp;                        |

&nbsp;                     VLAN 10

```



\### Trunk Port



A trunk port carries traffic belonging to multiple VLANs between network devices.



Example:



```text

SW1 ═══════════ SW2

&nbsp;      Trunk

&nbsp;   VLAN 10 + 20

```



\## What I Learned



\* A trunk allows multiple VLANs to cross between switches.

\* 802.1Q provides VLAN identification on trunk links.

\* Access ports are normally used for end devices.

\* Trunk ports are normally used between network devices.

\* The same VLAN can span multiple switches when the VLAN is carried across the trunk.

\* A trunk does not perform routing between VLANs.

\* VLAN 10 and VLAN 20 remain isolated until a Layer-3 device provides inter-VLAN routing.



\## Troubleshooting



Useful commands:



```text

show interfaces trunk

show vlan brief

show interfaces status

```



If same-VLAN communication between switches fails, check:



1\. VLAN exists on both switches.

2\. End-device ports are assigned to the correct VLAN.

3\. The switch-to-switch link is configured as a trunk on both ends.

4\. The trunk is operational.

5\. The correct IP addresses and subnet masks are configured.

6\. The physical link is up.



\## Evidence



\* `topology.png` — Packet Tracer topology screenshot

\* `02-trunking.pkt` — Packet Tracer lab file

\* `configs/SW1.txt` — SW1 configuration

\* `configs/SW2.txt` — SW2 configuration

\* `verification/show-interfaces-trunk.txt` — trunk verification from both switches

\* `verification/show-vlan-brief.txt` — VLAN verification from both switches

\* `verification/ping-results.txt` — connectivity test results



