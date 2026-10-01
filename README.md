# Elkhaleq Construction & Engineering
# Enterprise Campus Network & Data Center Infrastructure

> **Cisco Packet Tracer — Enterprise Network Design, Implementation & Security**

A comprehensive enterprise campus network simulation designed for a **Construction & Engineering corporate environment**, focusing on hierarchical architecture, redundancy, Layer 3 switching, centralized services, and network security.

---

## 🏢 Project Overview

The project simulates an enterprise network connecting:

* **40 PCs**
* **4 Main Departments**
* **IP Phones**
* **4 Access Switches**
* **2 Layer 3 Core/Distribution Switches**
* **Dedicated Data Center**
* Centralized Network Services

The four departments are:

1. **Engineering**
2. **Construction**
3. **Project Management**
4. **Finance & HR**

The network was designed and implemented using **Cisco Packet Tracer**.

---

# 🏗️ Network Architecture

The network follows a **Hierarchical Enterprise Architecture** using a:

## Collapsed Core/Distribution + Access Model

The Core and Distribution functions are combined into a single logical Layer 3 tier.

Two Layer 3 switches provide the Core/Distribution functions:

* **L3-SW1 — Primary**
* **L3-SW2 — Secondary**

The two switches are interconnected using **LACP EtherChannel**.

The Access layer consists of four switches, each serving a specific department.

---

# 🔹 Core/Distribution Layer

### L3-SW1 — PRIMARY

Main responsibilities:

* Layer 3 Switching
* SVI Gateways
* Inter-VLAN Routing
* Core connectivity
* Network redundancy

### L3-SW2 — SECONDARY

Main responsibilities:

* Layer 3 Switching
* SVI Gateways
* Redundant connectivity
* Backup Core path
* Network resilience

### Core Interconnection

```text
L3-SW1 ================= L3-SW2
          LACP EtherChannel
```

The EtherChannel provides an aggregated logical connection between the two Layer 3 switches.

---

# 🔹 Access Layer

The Access layer provides endpoint connectivity for the four corporate departments.

| Switch  | Department             |   VLAN |
| ------- | ---------------------- | -----: |
| **SW1** | **Engineering**        | **20** |
| **SW2** | **Construction**       | **30** |
| **SW3** | **Project Management** | **40** |
| **SW4** | **Finance & HR**       | **50** |

Each Access switch provides connectivity for:

* Department PCs
* IP Phones
* Data VLAN
* Voice VLAN
* End-user access ports

### Access Layer Hierarchy

```text
                    CORE / DISTRIBUTION
                     COLLAPSED LAYER
                    ┌───────────────┐
                    │    L3-SW1     │
                    │    PRIMARY    │
                    └───────┬───────┘
                            │
                    ┌───────┴───────┐
                    │    L3-SW2     │
                    │   SECONDARY   │
                    └───────┬───────┘
                            │
                       ACCESS LAYER
                            │
       ┌──────────┬─────────┼─────────┬──────────┐
       │          │         │         │
      SW1        SW2       SW3       SW4
       │          │         │         │
 Engineering Construction   PM      Finance & HR
       │          │         │         │
      PCs        PCs       PCs       PCs
    + Phones    + Phones  + Phones  + Phones
```

---

# 🌐 IP Addressing & VLSM

Main Network:

```text
192.168.10.0/24
```

| Department / Service | VLAN | Network            | Mask              | Usable Range | Gateway |
| -------------------- | ---: | ------------------ | ----------------- | ------------ | ------- |
| Data Center          |   10 | `192.168.10.64/29` | `255.255.255.248` | `.65 - .70`  | `.65`   |
| Engineering          |   20 | `192.168.10.32/28` | `255.255.255.240` | `.33 - .46`  | `.33`   |
| Construction         |   30 | `192.168.10.48/28` | `255.255.255.240` | `.49 - .62`  | `.49`   |
| Project Management   |   40 | `192.168.10.0/28`  | `255.255.255.240` | `.1 - .14`   | `.1`    |
| Finance & HR         |   50 | `192.168.10.16/28` | `255.255.255.240` | `.17 - .30`  | `.17`   |
| Voice                |  100 | `192.168.10.72/29` | `255.255.255.248` | `.73 - .78`  | `.73`   |

---

# 🔄 Redundancy & Single Point of Failure

The network uses two Layer 3 Core/Distribution switches instead of relying on a single Core switch.

```text
                  ┌─────────────┐
                  │   L3-SW1    │
                  │   PRIMARY   │
                  └──────┬──────┘
                         │
                         │ LACP
                         │
                  ┌──────┴──────┐
                  │   L3-SW2    │
                  │  SECONDARY  │
                  └─────────────┘
```

The Access layer can use redundant uplinks toward the Core/Distribution layer where implemented.

This design reduces dependency on:

* A single Core switch
* A single physical uplink
* A single logical path

### Design Principle

> **Redundancy reduces Single Points of Failure; it does not mean that every component is completely failure-proof.**

The actual level of redundancy depends on the physical and logical topology implemented in the Packet Tracer project.

---

# ⚙️ Technologies Implemented

## Layer 2

* VLANs
* VTP
* 802.1Q Trunking
* LACP EtherChannel
* Rapid PVST+
* Root Primary / Secondary
* Voice VLAN

## Layer 3

* SVI
* Inter-VLAN Routing
* Layer 3 Switching
* DHCP Relay
* `ip helper-address`

## Network Services

* DHCP
* DNS
* Web
* File
* Network Services

---

# 🔐 Network Security

The Access layer is protected using:

### Port Security

* Sticky MAC
* Maximum MAC addresses
* Restrict violation mode

### DHCP Snooping

* DHCP Snooping
* Trusted uplinks
* Protection against unauthorized DHCP servers

### BPDU Guard

Enabled on appropriate edge/access ports to protect the Spanning Tree topology.

### Device Hardening

* Enable Secret
* Console password
* Service password encryption
* MOTD banner
* Console timeout
* Disabled IP domain lookup

---

# ☎️ Voice Network

Voice traffic is separated using:

**Voice VLAN 100**

The Voice VLAN provides a dedicated logical network for IP Phones.

> **Design Note:** VLAN 100 uses a `/29` subnet with 6 usable addresses. If the final implementation requires more than 6 Voice endpoints, the subnet should be expanded.

---

# 🏢 Data Center

The Data Center provides centralized services:

```text
DHCP
DNS
WEB
FILE
NETWORK SERVICES
```

Data Center VLAN:

**VLAN 10**

Network:

```text
192.168.10.64/29
```

Gateway:

```text
192.168.10.65
```

DHCP Server:

```text
192.168.10.66
```

---

# 📡 DHCP Relay

DHCP requests from user VLANs are forwarded to the centralized DHCP Server using Layer 3 DHCP Relay.

Example:

```text
interface vlan 20
 ip helper-address 192.168.10.66
```

The same configuration principle is applied to the required user VLANs.

---

# 🧪 Testing & Verification

Important verification commands include:

```text
show vlan brief
show interfaces trunk
show etherchannel summary
show spanning-tree
show spanning-tree root
show ip interface brief
show ip route
show interfaces status
show port-security
show ip dhcp snooping
```

Connectivity testing:

```text
ping
tracert
```

Testing should verify:

* Engineering connectivity
* Construction connectivity
* Project Management connectivity
* Finance & HR connectivity
* Inter-VLAN communication
* DHCP assignment
* Data Center connectivity
* Voice VLAN operation
* EtherChannel operation
* STP operation
* Security features
* Link failure and recovery

---

# 🛠️ Failure & Recovery Testing

The project can be tested using controlled failure scenarios:

### 1. EtherChannel Link Failure

Disconnect one physical member of the EtherChannel and verify that the logical EtherChannel remains operational.

### 2. Access Uplink Failure

Disconnect one Access uplink and verify connectivity through the remaining redundant path where configured.

### 3. Core Failure

Test the effect of losing one Core/Distribution switch and verify the remaining network path where the required redundancy mechanisms are implemented.

### 4. Unauthorized Device

Connect an unauthorized device to a protected access port and verify Port Security behavior.

### 5. Unauthorized Switch

Connect a switch to an edge port and verify BPDU Guard behavior.

---

# 🎯 Project Objectives

The project demonstrates practical implementation of:

* Hierarchical Network Architecture
* VLSM
* VLAN Segmentation
* Layer 3 Switching
* Inter-VLAN Routing
* EtherChannel
* STP
* DHCP Relay
* Centralized Network Services
* VoIP
* Layer 2 Security
* Redundancy
* Troubleshooting

---

# 📁 Repository Structure

```text
Enterprise-Campus-Network/
│
├── Packet-Tracer/
│   └── Enterprise-Campus-Network.pkt
│
├── Configurations/
│   ├── L3-SW1.txt
│   ├── L3-SW2.txt
│   ├── SW1-Engineering.txt
│   ├── SW2-Construction.txt
│   ├── SW3-Project-Management.txt
│   └── SW4-Finance-HR.txt
│
├── Documentation/
│   ├── Network-Topology.png
│   ├── IP-Addressing.md
│   └── Network-Documentation.pdf
│
└── README.md
```

---

# 👨‍💻 Author

**Eng. Amr Khaleq**

Network Engineering | Cisco Networking | Windows Server | IT Infrastructure

---

# 🏷️ Technologies

`Cisco Packet Tracer`
`CCNA`
`VLAN`
`VTP`
`STP`
`Rapid PVST+`
`EtherChannel`
`LACP`
`Inter-VLAN Routing`
`DHCP`
`VoIP`
`Port Security`
`DHCP Snooping`
`BPDU Guard`
`Network Security`
