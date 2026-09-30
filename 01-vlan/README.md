# Lab 1 — VLAN and Access Ports

## Objective

Build a basic Layer-2 network using Cisco Packet Tracer and demonstrate how VLANs divide a switch into separate broadcast domains.

This lab demonstrates:

* VLAN creation
* Access ports
* VLAN membership
* Layer-2 communication within the same VLAN
* Isolation between different VLANs

## Platform

* Cisco Packet Tracer
* Cisco 2960 switch
* 4 PCs

## Topology

```text
                    SW1
              +-----+-----+
              |           |
           VLAN 10      VLAN 20
           /     \       /     \
         PC1     PC2   PC3     PC4
```

### Port Assignment

```text
PC1 → SW1 Fa0/1 → VLAN 10
PC2 → SW1 Fa0/2 → VLAN 10

PC3 → SW1 Fa0/3 → VLAN 20
PC4 → SW1 Fa0/4 → VLAN 20
```

## IP Addressing

| Device | IP Address    | Subnet Mask   | VLAN |
| ------ | ------------- | ------------- | ---- |
| PC1    | 192.168.10.10 | 255.255.255.0 | 10   |
| PC2    | 192.168.10.20 | 255.255.255.0 | 10   |
| PC3    | 192.168.20.10 | 255.255.255.0 | 20   |
| PC4    | 192.168.20.20 | 255.255.255.0 | 20   |

No default gateway is configured because this lab does not contain a router.

## VLAN Configuration

Two VLANs were created:

| VLAN | Name      | Ports        |
| ---- | --------- | ------------ |
| 10   | sales     | Fa0/1, Fa0/2 |
| 20   | marketing | Fa0/3, Fa0/4 |

All four PC-facing ports were configured as access ports.

## Configuration

### Create VLAN 10

```text
enable
configure terminal
vlan 10
name sales
exit
```

### Create VLAN 20

```text
vlan 20
name marketing
exit
```

### Configure PC1 and PC2 as VLAN 10 access ports

```text
interface fa0/1
switchport mode access
switchport access vlan 10
exit

interface fa0/2
switchport mode access
switchport access vlan 10
exit
```

### Configure PC3 and PC4 as VLAN 20 access ports

```text
interface fa0/3
switchport mode access
switchport access vlan 20
exit

interface fa0/4
switchport mode access
switchport access vlan 20
exit
```

## Verification

The VLAN configuration was verified using:

```text
show vlan brief
```

Expected result:

```text
10   sales       active    Fa0/1, Fa0/2
20   marketing   active    Fa0/3, Fa0/4
```

Connectivity was tested using ICMP ping.

### Expected Results

```text
PC1 → PC2       SUCCESS
PC3 → PC4       SUCCESS

PC1 → PC3       FAIL
PC3 → PC1       FAIL
```

### Explanation

PC1 and PC2 are members of VLAN 10, so they can communicate at Layer 2.

PC3 and PC4 are members of VLAN 20, so they can communicate at Layer 2.

PC1 and PC3 belong to different VLANs. Since there is no Layer-3 device performing inter-VLAN routing, they cannot communicate.

## What I Learned

* A VLAN creates a separate Layer-2 broadcast domain.
* An access port belongs to a single VLAN.
* End devices such as PCs are commonly connected through access ports.
* Devices in the same VLAN can communicate through Layer 2.
* Different VLANs are isolated from each other unless Layer-3 routing is configured.
* A router or Layer-3 switch is required for inter-VLAN communication.

## Troubleshooting

The following command can be used to verify VLAN membership:

```text
show vlan brief
```

If a PC cannot communicate with another PC in the same VLAN, check:

1. IP addresses
2. Subnet masks
3. Physical connections
4. VLAN assignment of the switch port
5. Whether the switch port is configured as an access port

## Evidence

* `topology.png` — Packet Tracer topology screenshot
* `configs/SW1.txt` — Switch configuration
* `verification/show-vlan-brief.txt` — VLAN verification output
* `verification/ping-results.txt` — Connectivity test results
