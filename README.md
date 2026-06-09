# 📡 CCNA Router Configurations
My CCNA study journey following **Jeremy's IT Lab** free course on YouTube.

---

## 👤 About
- 📚 Course: Jeremy's IT Lab — Free CCNA Course (200-301)
- 🛠️ Tool: Cisco Packet Tracer
- 🎯 Goal: Pass the Cisco CCNA 200-301 Exam

---

## 📊 Progress Tracker

| Day | Topic | Status |
|---|---|---|
| Day 01 | Network Devices | ✅ Done |
| Day 02 | Interfaces and Cables | ✅ Done |
| Day 03 | OSI Model & TCP/IP Suite | ✅ Done |
| Day 04 | Intro to the CLI | ✅ Done |
| Day 05 | Ethernet LAN Switching Part 1 | ✅ Done |
| Day 06 | Ethernet LAN Switching Part 2 | ✅ Done |
| Day 07 | IPv4 Addressing Part 1 | ✅ Done |
| Day 08 | IPv4 Addressing Part 2 | ✅ Done |
| Day 09 | Switch Interfaces | ✅ Done |
| Day 10 | IPv4 Header | ✅ Done |
| Day 11 | Routing Fundamentals | ✅ Done |
| Day 12 | Life of a Packet | ✅ Done |
| Day 13 | Subnetting Part 1 — Class C | ✅ Done |
| Day 14 | Subnetting Part 2 — Class B & A | ✅ Done |
| Day 15 | Subnetting Part 3 — VLSM | ✅ Done |
| Day 16 | VLANs Part 1 | ✅ Done |
| Day 17 | VLANs Part 2 | ✅ Done |
| Day 18 | VLANs Part 3 | ✅ Done |
| Day 19 | DTP & VTP | 🔲 Pending |
| Day 20 | Spanning Tree Protocol Part 1 | 🔲 Pending |

---

## 📁 Folder Structure
Each day has its own folder containing:
- `notes.md` — Topics, commands, lab summary, confusion points
- `topology.png` — Screenshot of the Packet Tracer network diagram
- `router-config.txt` — Actual CLI config copied from `show running-config`

---

## ⌨️ Master Command Reference

### Navigation & Modes
```
enable                          → Enter Privileged EXEC mode
disable                         → Return to User EXEC mode
configure terminal              → Enter Global Config mode
exit                            → Go back one level
end / Ctrl+Z                    → Jump back to Privileged EXEC
```

### Device Configuration
```
hostname [name]                 → Set device name
enable secret [password]        → Set encrypted enable password
service password-encryption     → Encrypt all plain-text passwords
no [command]                    → Remove/undo any configuration
```

### Interface Configuration
```
interface [type] [number]       → Enter interface config mode
ip address [ip] [subnet-mask]   → Assign IP to interface
no shutdown                     → Enable an interface
shutdown                        → Disable an interface
description [text]              → Label/document an interface
```

### Routing Commands
```
ip route [net] [mask] [next-hop]          → Add a static route
ip route 0.0.0.0 0.0.0.0 [next-hop]      → Add a default route
no ip route [net] [mask] [next-hop]       → Remove a static route
show ip route                             → View full routing table
show ip route static                      → View only static routes
show ip route connected                   → View only connected routes
show ip route [ip-address]               → Best route for specific IP
```

### Verification & Troubleshooting
```
show ip interface brief         → All interfaces, IPs, and status
show interfaces [int]           → Detailed interface info (MTU, errors)
show ip interface [int]         → IP-specific info for one interface
show arp / show ip arp          → ARP table — IP to MAC mappings
show mac address-table          → Switch MAC address table
show running-config             → Current active config (RAM)
show startup-config             → Saved config (NVRAM)
ping [ip]                       → Test connectivity
ping [ip] source [ip]           → Ping from a specific interface
traceroute [ip]                 → Show each hop to destination
```

### Saving Configuration
```
write memory  (or wr)                     → Save config to NVRAM
copy running-config startup-config        → Same as write memory
```

---

## 📌 Quick Reference Tables

### OSI Model
| # | Layer | PDU | Key Device |
|---|---|---|---|
| 7 | Application | Data | PC, Firewall |
| 6 | Presentation | Data | — |
| 5 | Session | Data | — |
| 4 | Transport | Segment | — |
| 3 | Network | Packet | **Router** |
| 2 | Data Link | Frame | **Switch** |
| 1 | Physical | Bit | Hub, Cables |

### IPv4 Address Classes
| Class | First Octet | Default Mask | Max Hosts |
|---|---|---|---|
| A | 1 – 126 | /8 | 16,777,214 |
| B | 128 – 191 | /16 | 65,534 |
| C | 192 – 223 | /24 | 254 |
| D | 224 – 239 | — | Multicast |
| E | 240 – 255 | — | Reserved |

### IPv4 Header — Key Fields
| Field | Size | Purpose |
|---|---|---|
| Version | 4 bits | Always 4 for IPv4 |
| TTL | 8 bits | Decremented per hop — prevents loops |
| Protocol | 8 bits | 1=ICMP, 6=TCP, 17=UDP, 89=OSPF |
| Source IP | 32 bits | Never changes end-to-end |
| Destination IP | 32 bits | Never changes end-to-end |

### Protocol Field Values
| Value | Protocol |
|---|---|
| 1 | ICMP (ping, traceroute) |
| 6 | TCP |
| 17 | UDP |
| 89 | OSPF |

### Routing — Administrative Distance
| Route Type | Code | AD |
|---|---|---|
| Connected | C | 0 |
| Static | S | 1 |
| OSPF | O | 110 |
| RIP | R | 120 |

### Subnetting Cheat Sheet (Class C)
| Prefix | Subnet Mask | Subnets | Hosts/Subnet | Block Size |
|---|---|---|---|---|
| /25 | 255.255.255.128 | 2 | 126 | 128 |
| /26 | 255.255.255.192 | 4 | 62 | 64 |
| /27 | 255.255.255.224 | 8 | 30 | 32 |
| /28 | 255.255.255.240 | 16 | 14 | 16 |
| /29 | 255.255.255.248 | 32 | 6 | 8 |
| /30 | 255.255.255.252 | 64 | 2 | 4 |

### Host Requirement → Prefix Lookup
| Hosts Needed | Use Prefix | Usable Hosts |
|---|---|---|
| 1 – 2 | /30 | 2 |
| 3 – 6 | /29 | 6 |
| 7 – 14 | /28 | 14 |
| 15 – 30 | /27 | 30 |
| 31 – 62 | /26 | 62 |
| 63 – 126 | /25 | 126 |
| 127 – 254 | /24 | 254 |

### Interface Status Meanings
| Status | Protocol | Meaning |
|---|---|---|
| up | up | ✅ Fully working |
| up | down | ⚠️ Layer 2 issue |
| administratively down | down | ❌ Manually shut down |
| down | down | ❌ Physical/cable issue |

---

## 🧮 Key Formulas

```
Magic Number        = 256 - last non-255 octet of subnet mask
Number of Subnets   = 2^(borrowed bits)
Hosts per Subnet    = 2^(host bits) - 2
Network Address     = first IP of subnet block (all host bits = 0)
Broadcast Address   = last IP of subnet block  (all host bits = 1)
First Usable Host   = Network Address + 1
Last Usable Host    = Broadcast Address - 1
```

---

## 🔑 Life of a Packet — Key Rules

```
✅ Source IP / Destination IP  → NEVER change across all hops
✅ Source MAC / Destination MAC → CHANGE at every single hop
✅ TTL                         → Decremented by 1 at each router
✅ ARP                         → Used to find next-hop MAC at each router
✅ Default Gateway             → Where hosts send traffic for remote networks
```

---

*Updated as I progress through the course — Jeremy's IT Lab Free CCNA 200-301*
