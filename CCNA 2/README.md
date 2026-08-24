# 🌐 CCNA 2 — Enterprise Switching, Routing & Wireless Essentials

> **Cisco Networking Academy — CCNA 2 (SRWE) Final Practical Skills Assessment**  
> **IPv4 • IPv6 • VLANs • EtherChannel • HSRPv2 • DHCP • Static Routing • Layer 2 Security**

---

## 📌 Overview

This project implements a **multi-site enterprise network** using Cisco Packet Tracer, focusing on switching, routing, redundancy, dynamic addressing, and Layer 2 security.

The lab includes:

- 5 Routers: `R1`, `R2`, `R3`, `SITE1`, `ISP`
- Multiple Layer 2 and Layer 3 switches
- Multi-VLAN enterprise segmentation
- IPv4 / IPv6 dual-stack connectivity
- HSRPv2 high availability
- LACP and PAgP EtherChannel
- DHCPv4 and DHCPv6
- Static and floating static routing
- Port Security, PortFast and BPDU Guard
- Router-on-a-Stick and SVI Inter-VLAN Routing

---

## 🗺️ Network Topology

![CCNA 2 Network Topology](./images/ccna2-topology.png)

---

## 🧩 Technologies Implemented

| Technology | Implementation |
|---|---|
| VLANs | VLAN 10, 20, 30, 40, 90/91 |
| Trunking | IEEE 802.1Q |
| EtherChannel | LACP / PAgP |
| STP | Rapid-PVST+ |
| Redundancy | HSRPv2 IPv4 / IPv6 |
| Routing | IPv4 / IPv6 Static Routes |
| Failover | Floating Static Routes |
| DHCP | DHCPv4 / Stateful & Stateless DHCPv6 |
| Inter-VLAN Routing | Router-on-a-Stick / Layer 3 SVI |
| Layer 2 Security | Port Security / Sticky MAC |
| STP Security | PortFast / BPDU Guard |
| DTP | Disabled with `nonegotiate` |

---

# 📐 Addressing Plan

## WAN & Infrastructure Networks

| Network | IPv4 | IPv6 |
|---|---|---|
| ISP – R1 | `201.201.201.200/30` | `2001:CEDA:CEDA:1::/64` |
| R1 – R2 | `10.0.1.0/30` | `2001:A:A:2::/64` |
| R1 – R3 | `10.0.1.4/30` | `2001:A:A:3::/64` |
| R1 – SITE1 | `10.0.1.8/30` | `2001:A:A:4::/64` |
| R1 – SITE2 | `10.0.1.12/30` | `2001:A:A:5::/64` |
| SITE1 – SITE2 | `10.0.1.16/30` | `2001:A:A:6::/64` |
| SERVERS | `10.0.0.0/24` | `2001:A:A:1::/64` |

## SITE1 VLANs

| VLAN | Purpose | IPv4 | IPv6 |
|---|---|---|---|
| 10 | Administration | `192.168.10.0/24` | `2001:A:A:10::/64` |
| 20 | Sales | `192.168.20.0/24` | `2001:A:A:20::/64` |
| 30 | Marketing | `192.168.30.0/24` | `2001:A:A:30::/64` |
| 40 | Voice | `192.168.40.0/24` | `2001:A:A:40::/64` |

## SITE2 VLANs

| VLAN | Purpose | IPv4 | IPv6 |
|---|---|---|---|
| 11 | Administration | `172.16.11.0/24` | `2001:A:A:11::/64` |
| 21 | Sales | `172.16.21.0/24` | `2001:A:A:21::/64` |
| 31 | Marketing | `172.16.31.0/24` | `2001:A:A:31::/64` |
| 41 | Voice | `172.16.41.0/24` | `2001:A:A:41::/64` |
| 91 | Native | — | — |

---

# ⚙️ Device Configuration

## 1. R1 — Core Router

```cisco
enable
configure terminal
hostname R1

ipv6 unicast-routing

! ISP / Internet Edge
interface GigabitEthernet0/0/0
 ip address 201.201.201.201 255.255.255.252
 ipv6 address fe80::1:1 link-local
 ipv6 address 2001:CEDA:CEDA:1::1/64
 no shutdown
exit

! R1 - R2
interface GigabitEthernet0/1/0
 ip address 10.0.1.1 255.255.255.252
 ipv6 address fe80::2:1 link-local
 ipv6 address 2001:A:A:2::1/64
 no shutdown
exit

! R1 - R3
interface GigabitEthernet0/2/0
 ip address 10.0.1.5 255.255.255.252
 ipv6 address fe80::3:1 link-local
 ipv6 address 2001:A:A:3::1/64
 no shutdown
exit

! R1 - SITE1
interface GigabitEthernet0/0
 ip address 10.0.1.9 255.255.255.252
 ipv6 address fe80::4:1 link-local
 ipv6 address 2001:A:A:4::1/64
 no shutdown
exit

! R1 - SITE2
interface GigabitEthernet0/1/1
 ip address 10.0.1.13 255.255.255.252
 ipv6 address fe80::5:1 link-local
 ipv6 address 2001:A:A:5::1/64
 no shutdown
exit
DHCPv4
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 172.16.11.1 172.16.11.11

ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 10.0.0.10
 domain-name miempresa.com
exit

ip dhcp pool VLAN11
 network 172.16.11.0 255.255.255.0
 default-router 172.16.11.1
 dns-server 10.0.0.10
 domain-name miempresa.com
exit
IPv4 Static & Floating Routes
ip route 192.168.0.0 255.255.192.0 GigabitEthernet0/0 10.0.1.10
ip route 192.168.0.0 255.255.192.0 GigabitEthernet0/1/0 5

ip route 172.16.0.0 255.255.0.0 GigabitEthernet0/1/1 10.0.1.14
ip route 172.16.0.0 255.255.0.0 GigabitEthernet0/0 5

ip route 10.0.0.0 255.255.255.0 GigabitEthernet0/1/0 10.0.1.2
ip route 10.0.0.0 255.255.255.0 GigabitEthernet0/2/0 10.0.1.6

ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0/0
IPv6 Static & Floating Routes
ipv6 route 2001:A:A:10::/64 GigabitEthernet0/0 fe80::4:2
ipv6 route 2001:A:A:10::/64 GigabitEthernet0/1/0 fe80::2:2 5

ipv6 route 2001:A:A:20::/64 GigabitEthernet0/0 fe80::4:2
ipv6 route 2001:A:A:20::/64 GigabitEthernet0/1/0 fe80::2:2 5

ipv6 route 2001:A:A:30::/64 GigabitEthernet0/0 fe80::4:2
ipv6 route 2001:A:A:30::/64 GigabitEthernet0/1/0 fe80::2:2 5

ipv6 route 2001:A:A:40::/64 GigabitEthernet0/0 fe80::4:2
ipv6 route 2001:A:A:40::/64 GigabitEthernet0/1/0 fe80::2:2 5

ipv6 route 2001:A:A:11::/64 GigabitEthernet0/1/1 fe80::5:2
ipv6 route 2001:A:A:11::/64 GigabitEthernet0/0 fe80::4:2 5

ipv6 route 2001:A:A:21::/64 GigabitEthernet0/1/1 fe80::5:2
ipv6 route 2001:A:A:21::/64 GigabitEthernet0/0 fe80::4:2 5

ipv6 route 2001:A:A:31::/64 GigabitEthernet0/1/1 fe80::5:2
ipv6 route 2001:A:A:31::/64 GigabitEthernet0/0 fe80::4:2 5

ipv6 route 2001:A:A:41::/64 GigabitEthernet0/1/1 fe80::5:2
ipv6 route 2001:A:A:41::/64 GigabitEthernet0/0 fe80::4:2 5

ipv6 route 2001:A:A:1::/64 GigabitEthernet0/1/0 fe80::2:2
ipv6 route 2001:A:A:1::/64 GigabitEthernet0/2/0 fe80::3:2

ipv6 route ::/0 GigabitEthernet0/0/0

end
write memory
🔄 2. R2 — HSRP Redundant Router
enable
configure terminal
hostname R2

ipv6 unicast-routing

interface GigabitEthernet0/1/0
 ip address 10.0.1.2 255.255.255.252
 ipv6 address fe80::2:2 link-local
 ipv6 address 2001:A:A:2::2/64
 no shutdown
exit

interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.0
 ipv6 address fe80::1:2 link-local
 ipv6 address 2001:A:A:1::2/64

 standby version 2
 standby 4 ip 10.0.0.1
 standby 6 ipv6 FE80::1
 standby 6 ipv6 autoconfig

 no shutdown
exit
IPv4 Routing
ip route 192.168.0.0 255.255.192.0 GigabitEthernet0/0 10.0.0.4
ip route 172.16.0.0 255.255.0.0 GigabitEthernet0/1/0 10.0.1.1
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/1/0 10.0.1.1
IPv6 Routing
ipv6 route 2001:A:A:10::/64 GigabitEthernet0/0 fe80::1:4
ipv6 route 2001:A:A:20::/64 GigabitEthernet0/0 fe80::1:4
ipv6 route 2001:A:A:30::/64 GigabitEthernet0/0 fe80::1:4
ipv6 route 2001:A:A:40::/64 GigabitEthernet0/0 fe80::1:4

ipv6 route 2001:A:A:11::/64 GigabitEthernet0/1/0 fe80::2:1
ipv6 route 2001:A:A:21::/64 GigabitEthernet0/1/0 fe80::2:1
ipv6 route 2001:A:A:31::/64 GigabitEthernet0/1/0 fe80::2:1
ipv6 route 2001:A:A:41::/64 GigabitEthernet0/1/0 fe80::2:1

ipv6 route ::/0 GigabitEthernet0/1/0 fe80::2:1

end
write memory
🖥️ 3. SITE2 — Multilayer Switch
enable
configure terminal
hostname SITE2

ip routing
ipv6 unicast-routing

vlan 11
 name Administrativos
vlan 21
 name Ventas
vlan 31
 name Mercadeo
vlan 41
 name Voz
vlan 91
 name Nativa
exit
Routed Interfaces
interface GigabitEthernet1/0/1
 no switchport
 ip address 10.0.1.14 255.255.255.252
 ipv6 address fe80::5:2 link-local
 ipv6 address 2001:A:A:5::2/64
 no shutdown
exit

interface GigabitEthernet1/0/2
 no switchport
 ip address 10.0.1.18 255.255.255.252
 ipv6 address fe80::6:2 link-local
 ipv6 address 2001:A:A:6::2/64
 no shutdown
exit
Trunk Configuration
interface GigabitEthernet1/0/3
 switchport mode trunk
 switchport trunk native vlan 91
 switchport trunk allowed vlan 11,21,31,41,91
 switchport nonegotiate
exit
Stateful DHCPv6
ipv6 dhcp pool VLAN11
 address prefix 2001:A:A:11::/64
 dns-server 2001:A:A:1::AAAA
 domain-name miempresa.com
exit

ipv6 dhcp pool VLAN21
 address prefix 2001:A:A:21::/64
 dns-server 2001:A:A:1::AAAA
 domain-name miempresa.com
exit
SVIs
interface vlan 11
 ip address 172.16.11.1 255.255.255.0
 ipv6 address fe80::11:1 link-local
 ipv6 address 2001:A:A:11::1/64
 ip helper-address 10.0.1.13
 ipv6 dhcp server VLAN11
 ipv6 nd managed-config-flag
 no shutdown
exit

interface vlan 21
 ip address 172.16.21.1 255.255.255.0
 ipv6 address fe80::21:1 link-local
 ipv6 address 2001:A:A:21::1/64
 ip helper-address 10.0.1.13
 ipv6 dhcp server VLAN21
 ipv6 nd managed-config-flag
 no shutdown
exit

interface vlan 31
 ip address 172.16.31.1 255.255.255.0
 ipv6 address fe80::31:1 link-local
 ipv6 address 2001:A:A:31::1/64
 ip helper-address 10.0.1.13
 no shutdown
exit

end
write memory
🔐 4. S3 — Layer 2 Security & EtherChannel
enable
configure terminal
hostname S3

spanning-tree portfast default

vlan 11
 name Administrativos
vlan 21
 name Ventas
vlan 31
 name Mercadeo
vlan 41
 name Voz
vlan 91
 name Nativa
exit
Port Security
interface range FastEthernet0/6-12
 switchport mode access
 switchport access vlan 11
 switchport voice vlan 41

 switchport port-security
 switchport port-security maximum 5
 switchport port-security mac-address sticky
 switchport port-security violation restrict

 spanning-tree bpduguard enable
exit
PAgP EtherChannel
interface range FastEthernet0/1-5
 channel-group 1 mode desirable
exit

interface Port-channel 1
 switchport mode trunk
 switchport trunk native vlan 91
 switchport trunk allowed vlan 11,21,31,41,91
 switchport nonegotiate
exit

end
write memory
🧪 Verification & Testing
Routing
show ip route
show ipv6 route
HSRP
show standby
show standby brief
DHCP
show ip dhcp binding
show ipv6 dhcp binding
VLANs
show vlan brief
Trunking
show interfaces trunk
EtherChannel
show etherchannel summary
Port Security
show port-security
show port-security interface FastEthernet0/6
IPv4 / IPv6 Interfaces
show ip interface brief
show ipv6 interface brief
Connectivity Tests
ping <IPv4-address>
ping <IPv6-address>
traceroute <IPv4-address>
traceroute <IPv6-address>
📊 Skills Demonstrated
Domain	Skills
🔀 Switching	VLANs, 802.1Q, Trunking, DTP
⚡ EtherChannel	LACP, PAgP
🌳 STP	Rapid-PVST+, PortFast, BPDU Guard
🚀 High Availability	HSRPv2 IPv4 / IPv6
🧭 Routing	Static & Floating Static Routes
🌐 IPv6	Dual-Stack, DHCPv6
📡 DHCP	DHCPv4, DHCP Relay, Stateful DHCPv6
🛡️ Security	Port Security, Sticky MAC
🔄 Redundancy	Multiple WAN Paths & Gateway Redundancy
🖥️ Cisco IOS	Configuration & Troubleshooting


🎯 Lab Objectives
- Design and implement a multi-site enterprise network.
- Configure IPv4 and IPv6 addressing.
- Segment the network using VLANs.
- Implement Inter-VLAN routing.
- Configure LACP and PAgP EtherChannels.
- Implement HSRPv2 for gateway redundancy.
- Configure DHCPv4 and DHCPv6.
- Implement static and floating static routing.
- Harden Layer 2 access ports.
- Verify end-to-end IPv4/IPv6 connectivity.
🧰 Environment
Platform: Cisco Packet Tracer 8.x+
Course: Cisco Networking Academy — CCNA 2
Track: Switching, Routing & Wireless Essentials (SRWE)
Certification: Cisco CCNA 200-301
Built as a practical CCNA 2 networking and infrastructure lab.
Segment. Route. Secure. Redundancy.
