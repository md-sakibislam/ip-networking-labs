# Lab 5 — Static Routing

## Objective

The objective of this lab is to configure and verify static routing between two separate LAN networks using two Cisco routers.

This lab demonstrates:

* Router-to-router connectivity
* Directly connected networks
* Static routes
* Next-hop addresses
* Routing-table verification
* End-to-end connectivity between separate LANs

The main goal is to understand how a router reaches a remote network when that network is not directly connected.

---

## Platform

**Cisco Packet Tracer**

### Devices

* 2 × Cisco 1941 Routers
* 2 × Cisco 2960 Switches
* 2 × PCs

---

## Topology

```text
                         10.0.0.0/30
                    G0/1           G0/1
              +-----R1---------------R2-----+
              |                              |
            G0/0                           G0/0
              |                              |
             SW1                            SW2
              |                              |
             PC1                            PC2
```

### Logical topology

```text
PC1 ── SW1 ── R1 ───────── R2 ── SW2 ── PC2
       LAN 1       WAN/link       LAN 2
```

---

## Connections

| Device | Interface | Connected To |
| ------ | --------- | ------------ |
| PC1    | NIC       | SW1 Fa0/1    |
| SW1    | Fa0/24    | R1 G0/0      |
| R1     | G0/1      | R2 G0/1      |
| R2     | G0/0      | SW2 Fa0/24   |
| SW2    | Fa0/1     | PC2          |

---

## Network Addressing

The topology uses three IP networks.

### LAN 1

```text
192.168.10.0/24
```

### Router-to-router link

```text
10.0.0.0/30
```

### LAN 2

```text
192.168.20.0/24
```

---

## IP Addressing Table

| Device | Interface | IP Address    | Subnet Mask     |
| ------ | --------- | ------------- | --------------- |
| PC1    | NIC       | 192.168.10.10 | 255.255.255.0   |
| R1     | G0/0      | 192.168.10.1  | 255.255.255.0   |
| R1     | G0/1      | 10.0.0.1      | 255.255.255.252 |
| R2     | G0/1      | 10.0.0.2      | 255.255.255.252 |
| R2     | G0/0      | 192.168.20.1  | 255.255.255.0   |
| PC2    | NIC       | 192.168.20.10 | 255.255.255.0   |

### Default Gateways

PC1:

```text
192.168.10.1
```

PC2:

```text
192.168.20.1
```

---

# R1 Configuration

## LAN interface

```text
interface gigabitEthernet 0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
```

## R1-R2 link

```text
interface gigabitEthernet 0/1
 ip address 10.0.0.1 255.255.255.252
 no shutdown
```

At this point R1 knows about:

```text
192.168.10.0/24 → directly connected
10.0.0.0/30     → directly connected
```

However, R1 does not automatically know how to reach:

```text
192.168.20.0/24
```

---

# R2 Configuration

## LAN interface

```text
interface gigabitEthernet 0/0
 ip address 192.168.20.1 255.255.255.0
 no shutdown
```

## R1-R2 link

```text
interface gigabitEthernet 0/1
 ip address 10.0.0.2 255.255.255.252
 no shutdown
```

At this point R2 knows about:

```text
192.168.20.0/24 → directly connected
10.0.0.0/30     → directly connected
```

However, R2 does not automatically know how to reach:

```text
192.168.10.0/24
```

---

# Static Route Configuration

Static routes manually tell a router how to reach a remote network.

## R1 static route

R1 needs to reach the remote LAN:

```text
192.168.20.0/24
```

R2 is the next hop:

```text
10.0.0.2
```

Therefore:

```text
ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

This means:

> To reach 192.168.20.0/24, forward the traffic to 10.0.0.2.

---

## R2 static route

R2 needs to reach:

```text
192.168.10.0/24
```

R1 is the next hop:

```text
10.0.0.1
```

Therefore:

```text
ip route 192.168.10.0 255.255.255.0 10.0.0.1
```

This means:

> To reach 192.168.10.0/24, forward the traffic to 10.0.0.1.

---

# Routing Tables

## R1

After configuring the static route, R1 knows:

```text
192.168.10.0/24 → directly connected
10.0.0.0/30     → directly connected
192.168.20.0/24 → via 10.0.0.2
```

The static route appears with:

```text
S
```

where `S` means **Static**.

---

## R2

After configuring the static route, R2 knows:

```text
192.168.20.0/24 → directly connected
10.0.0.0/30     → directly connected
192.168.10.0/24 → via 10.0.0.1
```

The static route appears with:

```text
S
```

---

# Packet Flow

Suppose PC1 wants to communicate with PC2:

```text
192.168.10.10 → 192.168.20.10
```

### Step 1 — PC1 checks the destination

PC1 determines that `192.168.20.10` is outside its local:

```text
192.168.10.0/24
```

network.

PC1 therefore sends the traffic to its default gateway:

```text
192.168.10.1
```

---

### Step 2 — R1 receives the packet

R1 checks its routing table.

It finds:

```text
S 192.168.20.0/24 via 10.0.0.2
```

R1 therefore forwards the packet toward R2.

---

### Step 3 — R2 receives the packet

R2 receives the traffic through:

```text
10.0.0.2
```

R2 checks its routing table and knows:

```text
192.168.20.0/24 → directly connected
```

R2 forwards the packet toward:

```text
192.168.20.10
```

---

### Complete path

```text
PC1
192.168.10.10
     |
     ↓
   SW1
     |
     ↓
    R1
     |
10.0.0.0/30
     |
     ↓
    R2
     |
     ↓
   SW2
     |
     ↓
PC2
192.168.20.10
```

---

# Verification

## 1. Verify R1 routing table

Command:

```text
show ip route
```

Expected important entries:

```text
C 192.168.10.0/24 is directly connected
C 10.0.0.0/30 is directly connected
S 192.168.20.0/24 [1/0] via 10.0.0.2
```

---

## 2. Verify R2 routing table

Command:

```text
show ip route
```

Expected important entries:

```text
C 192.168.20.0/24 is directly connected
C 10.0.0.0/30 is directly connected
S 192.168.10.0/24 [1/0] via 10.0.0.1
```

---

## 3. Verify router-to-router connectivity

From R1:

```text
ping 10.0.0.2
```

From R2:

```text
ping 10.0.0.1
```

Both routers were able to reach each other.

---

## 4. Verify remote LAN gateway connectivity

From R1:

```text
ping 192.168.20.1
```

From R2:

```text
ping 192.168.10.1
```

Both remote router LAN interfaces were reachable.

---

## 5. End-to-end connectivity

From PC1:

```text
ping 192.168.20.10
```

From PC2:

```text
ping 192.168.10.10
```

These tests verify the complete path across both routers.

---

# Static Routing vs Directly Connected Routes

The routing table contains different types of routes.

### Connected route

```text
C
```

Means the network is directly connected to the router.

Example:

```text
C 192.168.10.0/24
```

### Local route

```text
L
```

Represents the router's own interface IP address.

### Static route

```text
S
```

Means the route was manually configured by an administrator.

Example:

```text
S 192.168.20.0/24 via 10.0.0.2
```

---

# What I Learned

* A router automatically knows its directly connected networks.
* A router does not automatically know about remote networks.
* Static routes manually define paths to remote networks.
* The `ip route` command is used to configure a static route.
* A next-hop IP identifies the neighboring router that should receive the traffic.
* `show ip route` can be used to verify static routes.
* The `S` code in the routing table represents a static route.
* Both directions must have appropriate routes for successful end-to-end communication.
* Static routing is useful for understanding the fundamentals of routing before learning dynamic routing protocols such as OSPF.

---

# Troubleshooting Checklist

If PC1 cannot reach PC2, check:

1. PC1 IP address
2. PC2 IP address
3. PC1 default gateway
4. PC2 default gateway
5. R1 G0/0 address
6. R1 G0/1 address
7. R2 G0/0 address
8. R2 G0/1 address
9. R1-R2 link status
10. Static route on R1
11. Static route on R2
12. Routing tables on both routers

Useful commands:

```text
show ip interface brief
show ip route
ping <destination>
```

---

# Evidence

The following files contain the lab evidence:

```text
verification/
├── R1-show-ip-route.txt
├── R2-show-ip-route.txt
├── router-ping-results.txt
└── end-to-end-ping-results.txt
```

The Packet Tracer project is saved as:

```text
05-static-routing.pkt
```

---

# Lab Status

**Completed**

* [x] Two-router topology created
* [x] LAN addressing configured
* [x] Router-to-router /30 link configured
* [x] R1 directly connected networks verified
* [x] R2 directly connected networks verified
* [x] Static route configured on R1
* [x] Static route configured on R2
* [x] Static routes verified with `show ip route`
* [x] Router-to-router connectivity verified
* [x] Remote LAN connectivity verified
* [x] End-to-end connectivity tested
