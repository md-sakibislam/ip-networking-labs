<div align="center">

# 🌐 IP Networking Labs

### MD. Sakib Islam

*Cybersecurity Analyst — Self-Taught | BSc in CSE, AIUB*

[![Email](https://img.shields.io/badge/Email-mdsakibislam.infosec%40gmail.com-D14836?style=flat&logo=gmail&logoColor=white)](mailto:mdsakibislam.infosec@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-mdsakibislam-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://linkedin.com/in/mdsakibislam)

</div>

---

## 👋 About

This repository documents my **hands-on IP networking labs** built while developing practical networking and infrastructure fundamentals.

The labs focus on **Layer 2 switching, Layer 3 routing, VLANs, trunking, inter-VLAN communication, static routing, and dynamic routing fundamentals** using Cisco Packet Tracer.

Each lab includes the topology, device configuration, verification commands, and connectivity tests where applicable.

The goal is to build practical understanding of how enterprise networks are designed, configured, verified, and troubleshot.

---

## 🧰 Skills Demonstrated


**Networking Fundamentals**

* OSI and TCP/IP concepts
* Layer 2 and Layer 3 networking
* IPv4 addressing and subnetting
* Default gateways
* Routing tables
* Network troubleshooting

**Layer 2**

* VLANs
* Access ports
* 802.1Q trunking
* VLAN segmentation

**Layer 3**

* Inter-VLAN routing
* Router-on-a-Stick
* Layer 3 switching
* Switched Virtual Interfaces (SVIs)
* `ip routing`
* Static routing

**Dynamic Routing**

* OSPF — currently in progress

**Tools**

* Cisco Packet Tracer
* Cisco IOS CLI
* Git & GitHub

---

## 📂 Projects

| **#** | **Project**                                   | **Focus Area**                                            | **Status**     |
| ----- | --------------------------------------------- | --------------------------------------------------------- | -------------- |
| 01    | [VLAN + Access Ports](./01-vlan)              | VLAN segmentation, access ports, Layer 2 connectivity     | ✅ Completed    |
| 02    | [Trunking / 802.1Q](./02-trunking)            | 802.1Q trunking, VLAN propagation between switches        | ✅ Completed    |
| 03    | [Inter-VLAN Routing](./03-inter-vlan-routing) | Router-on-a-Stick, 802.1Q subinterfaces, default gateways | ✅ Completed    |
| 04    | [Layer 3 Switching](./04-layer-3-switching)   | SVIs, `ip routing`, Layer 3 switching                     | ✅ Completed    |
| 05    | [Static Routing](./05-static-routing)         | Next-hop static routes, routing tables, connectivity      | ✅ Completed    |
| 06    | OSPF                                          | Dynamic routing, Area 0, OSPF neighbors                   | 🔄 In Progress  |
| 07    | eBGP                                          | Basic external BGP peering and routing                    | ⏳ Planned      |
| 08    | iBGP                                          | Basic internal BGP peering                                | ⏳ Planned      |

---

## 🔬 Lab Coverage

### Layer 2 Networking

The initial labs demonstrate how switches separate network traffic using VLANs and how trunk links carry multiple VLANs between network devices.

**Topics practiced:**

* VLAN creation
* VLAN naming
* Access-port configuration
* 802.1Q trunking
* VLAN-to-VLAN isolation
* VLAN propagation across switches
* Verification using Cisco IOS commands

---

### Layer 3 Networking

The Layer 3 labs demonstrate how devices communicate between different IP networks.

**Topics practiced:**

* Router-on-a-Stick
* 802.1Q subinterfaces
* Default gateways
* SVIs
* Layer 3 switching
* Static routes
* Routing table verification

---

### Dynamic Routing

Dynamic routing labs are being added progressively.

Current focus:

* OSPF Area 0
* OSPF neighbor relationships
* OSPF-learned routes
* Dynamic path learning

Future labs:

* eBGP
* iBGP

---

## 🧪 Verification & Troubleshooting

Cisco IOS verification commands used throughout the labs include:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show ip route ospf
show ip ospf neighbor
ping
```

These commands are used to verify:

* VLAN membership
* Trunk status
* Interface state
* IP addressing
* Routing-table entries
* OSPF neighbors
* End-to-end connectivity

---

## 📁 Repository Structure

```text
ip-networking-labs/
│
├── README.md
│
├── 01-vlan/
│   ├── README.md
│   ├── 01-vlan.pkt
│   ├── topology.png
│   ├── configs/
│   └── verification/
│
├── 02-trunking/
│   ├── README.md
│   ├── 02-trunking.pkt
│   ├── topology.png
│   ├── configs/
│   └── verification/
│
├── 03-inter-vlan-routing/
│   ├── README.md
│   ├── 03-inter-vlan-routing.pkt
│   ├── topology.png
│   ├── configs/
│   └── verification/
│
├── 04-layer-3-switching/
│   ├── README.md
│   ├── 04-layer-3-switching.pkt
│   ├── topology.png
│   ├── configs/
│   └── verification/
│
└── 05-static-routing/
    ├── README.md
    ├── 05-static-routing.pkt
    ├── topology.png
    ├── configs/
    └── verification/
```

---

## 🎯 Learning Objectives

Through these labs, I am developing practical understanding of:

1. How VLANs provide Layer 2 segmentation.
2. How access ports connect end devices to VLANs.
3. How 802.1Q trunks transport multiple VLANs.
4. How routers enable communication between different VLANs.
5. How Layer 3 switches perform routing using SVIs.
6. How static routes are added to routing tables.
7. How dynamic routing protocols such as OSPF learn routes.
8. How Cisco IOS commands can be used to verify and troubleshoot network configurations.

---

## 🛠️ Lab Platform

**Primary platform:**

* Cisco Packet Tracer
* Cisco IOS CLI

The labs are intentionally kept small and focused so that each networking concept can be configured, verified, and understood independently.

---

## 📌 Hands-On Progress

| Technology         | Practical Status  |
| ------------------ | ----------------- |
| VLAN               | ✅ Hands-on        |
| Access Ports       | ✅ Hands-on        |
| 802.1Q Trunking    | ✅ Hands-on        |
| Inter-VLAN Routing | ✅ Hands-on        |
| Router-on-a-Stick  | ✅ Hands-on        |
| Layer 2 / Layer 3  | ✅ Hands-on        |
| Layer 3 Switching  | ✅ Hands-on        |
| SVI                | ✅ Hands-on        |
| Static Routing     | ✅ Hands-on        |
| OSPF               | 🔄 In Progress     |
| eBGP               | ⏳ Not Started     |
| iBGP               | ⏳ Not Started     |
| QoS                | ⏳ Planned         |
| Nagios / NMS       | ⏳ Planned         |
| Huawei VRP         | ⏳ Planned         |

---

## 📈 Future Labs

The repository will gradually expand to cover additional service-provider and enterprise networking technologies:

* OSPF
* eBGP / iBGP
* IS-IS
* MPLS
* LDP
* MPLS L3VPN
* VRF
* QoS
* Network Monitoring / NMS
* GPON fundamentals
* Huawei VRP

Technologies that are not yet implemented will be clearly marked as **planned or theory-only** rather than presented as completed hands-on experience.

---

## 📄 Disclaimer

These labs are personal learning projects created to develop practical networking knowledge and troubleshooting skills.

Configurations and topologies are designed for educational and lab purposes and are not intended to represent production network configurations.

---

*Thanks for visiting my networking lab portfolio.*
