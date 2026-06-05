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
| Day 09 | Switch Interfaces | 🔲 Pending |
| Day 10 | IPv4 Header | 🔲 Pending |
| Day 11 | Routing Fundamentals | 🔲 Pending |
| Day 12 | Life of a Packet | 🔲 Pending |
| Day 13 | Subnetting Part 1 | 🔲 Pending |
| Day 14 | Subnetting Part 2 | 🔲 Pending |
| Day 15 | Subnetting Part 3 | 🔲 Pending |

---

## 📁 Folder Structure
Each day has its own folder containing:
- `notes.md` — Topics, commands, lab summary, and confusion points
- `router-config.txt` — Actual CLI configuration from Packet Tracer lab

---

## ⌨️ Key Commands Learned So Far

```
enable                          → Enter Privileged EXEC mode
configure terminal              → Enter Global Config mode
hostname [name]                 → Set device name
enable secret [password]        → Set encrypted enable password
service password-encryption     → Encrypt all plain-text passwords
interface [int-id]              → Enter interface config
ip address [ip] [subnet-mask]   → Assign IP to interface
no shutdown                     → Enable an interface
description [text]              → Label an interface
show ip interface brief         → View all interfaces + status
show interfaces                 → Detailed interface info
show arp                        → View ARP table
show mac address-table          → View MAC address table
show running-config             → View current config (RAM)
show startup-config             → View saved config (NVRAM)
write memory                    → Save config to NVRAM
ping [ip-address]               → Test connectivity
```

---

## 📌 Quick Reference

### OSI Model Layers
| # | Layer | PDU | Device |
|---|---|---|---|
| 7 | Application | Data | Firewall, PC |
| 6 | Presentation | Data | — |
| 5 | Session | Data | — |
| 4 | Transport | Segment | — |
| 3 | Network | Packet | Router |
| 2 | Data Link | Frame | Switch |
| 1 | Physical | Bit | Hub, Cable |

### IPv4 Address Classes
| Class | Range | Default Mask |
|---|---|---|
| A | 1–126 | /8 |
| B | 128–191 | /16 |
| C | 192–223 | /24 |
| D | 224–239 | Multicast |
| E | 240–255 | Reserved |

### Interface Status Meanings
| Status | Protocol | Meaning |
|---|---|---|
| up | up | ✅ Working fine |
| up | down | ⚠️ Layer 2 issue |
| admin down | down | ❌ Manually shut down |
| down | down | ❌ Physical issue |

---

*Updated as I progress through the course.*
