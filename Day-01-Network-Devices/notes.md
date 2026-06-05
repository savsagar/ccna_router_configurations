# Day 01 - Network Devices

## 📌 Topics Covered
- What is a Network and what is the Internet
- Types of network devices: Router, Switch, Firewall, IDS/IPS, Access Point, Server
- End Hosts vs Network Devices
- Client vs Server model
- How devices communicate in a network

## 🖥️ Key Concepts

### Network Devices
| Device | Role |
|---|---|
| **Router** | Connects different networks together, routes traffic between them |
| **Switch** | Connects devices within the same LAN, forwards frames using MAC addresses |
| **Firewall** | Controls traffic in/out of network based on rules (security) |
| **IDS/IPS** | Detects and/or prevents intrusions and attacks |
| **Wireless Access Point (WAP)** | Allows Wi-Fi devices to connect to the wired network |

### End Hosts vs Network Devices
- **End Hosts** = devices that send/receive data (PC, laptop, phone, printer, server)
- **Network Devices** = devices that forward/filter traffic (routers, switches, firewalls)

### Client vs Server
- **Client** = device requesting a service (e.g., your PC browsing a website)
- **Server** = device providing a service (e.g., web server, email server)
- Any device CAN be both client and server depending on context

## ⌨️ Commands Learned
| Command | Mode | What it does |
|---|---|---|
| `enable` | User EXEC | Enters Privileged EXEC mode |
| `show ip interface brief` | Privileged EXEC | Shows all interfaces, IPs, and status |

## 🔧 Lab Summary
- Opened Packet Tracer and explored the device palette
- Identified routers, switches, PCs, and servers in a sample topology
- Connected 2 PCs to a switch and observed how the network looks

## 💡 Important Notes
- Switches operate at **Layer 2** (Data Link) using MAC addresses
- Routers operate at **Layer 3** (Network) using IP addresses
- Firewalls can be hardware or software-based
- Servers don't need special hardware — any PC can be a server

## ❓ Things I Was Confused About
- Difference between a **Router** and a **Layer 3 Switch** (both can route — L3 switch is faster within a LAN)
- When to use a switch vs a router (switch = same network, router = different networks)
