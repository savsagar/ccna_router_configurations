# Day 07 - IPv4 Addressing (Part 1)

## 📌 Topics Covered
- What is an IPv4 Address
- Binary and Dotted Decimal Notation
- IPv4 Address Classes (A, B, C, D, E)
- Network portion vs Host portion
- Subnet Mask
- CIDR (Slash) Notation
- Loopback Address

## 🖥️ Key Concepts

### IPv4 Address Structure
- **32-bit** address divided into 4 octets (groups of 8 bits)
- Written in **dotted decimal** notation: `192.168.1.1`
- Each octet = 0 to 255

### Binary to Decimal — Quick Reference
| Binary | Decimal |
|---|---|
| 10000000 | 128 |
| 11000000 | 192 |
| 11100000 | 224 |
| 11110000 | 240 |
| 11111000 | 248 |
| 11111100 | 252 |
| 11111110 | 254 |
| 11111111 | 255 |

### IPv4 Address Classes
| Class | First Octet Range | Default Subnet Mask | Network / Host Bits | Usage |
|---|---|---|---|---|
| **A** | 1 – 126 | 255.0.0.0 (/8) | 8 net / 24 host | Large networks |
| **B** | 128 – 191 | 255.255.0.0 (/16) | 16 net / 16 host | Medium networks |
| **C** | 192 – 223 | 255.255.255.0 (/24) | 24 net / 8 host | Small networks |
| **D** | 224 – 239 | N/A | N/A | Multicast |
| **E** | 240 – 255 | N/A | N/A | Reserved/Research |

> 💡 **127.x.x.x** = Loopback range (reserved — not Class A usable)

### Special Addresses
| Address | Purpose |
|---|---|
| `127.0.0.1` | Loopback — tests local TCP/IP stack |
| `0.0.0.0` | Represents "this" network or default route |
| `255.255.255.255` | Limited broadcast (all hosts on local network) |

### Network vs Host Portion
- **Network bits** = identify the network (same for all hosts in the network)
- **Host bits** = identify the specific host within the network

Example: `192.168.1.10 /24`
```
Network portion: 192.168.1  (first 24 bits)
Host portion:    .10        (last 8 bits)
```

### Subnet Mask
- Written as dotted decimal: `255.255.255.0`
- Network bits = **1s**, Host bits = **0s**
- `255.255.255.0` in binary = `11111111.11111111.11111111.00000000`

### CIDR / Slash Notation
| Subnet Mask | CIDR | Network Bits |
|---|---|---|
| 255.0.0.0 | /8 | 8 |
| 255.255.0.0 | /16 | 16 |
| 255.255.255.0 | /24 | 24 |
| 255.255.255.128 | /25 | 25 |
| 255.255.255.192 | /26 | 26 |
| 255.255.255.224 | /27 | 27 |
| 255.255.255.240 | /28 | 28 |
| 255.255.255.248 | /29 | 29 |
| 255.255.255.252 | /30 | 30 |

## ⌨️ Commands Learned
| Command | Mode | What it does |
|---|---|---|
| `show ip interface brief` | Privileged EXEC | Lists all interfaces with IP and status |
| `show interfaces` | Privileged EXEC | Detailed interface information |
| `show ip interface [int]` | Privileged EXEC | IP-specific info for one interface |

## 🔧 Lab Configuration — Assigning IP to Router Interface

```
R1> enable
R1# configure terminal
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1# show ip interface brief
```

## 🔧 Lab Summary
- Practiced converting dotted decimal to binary and back
- Identified address class from the first octet of given IP addresses
- Identified network and host portions using subnet masks
- Assigned IP addresses to router interfaces in Packet Tracer
- Used `show ip interface brief` to verify IP assignment and interface status

## 💡 Important Notes
- Class A has the most hosts per network (2^24 - 2 = ~16 million hosts)
- Class C has the fewest hosts per network (2^8 - 2 = 254 hosts)
- Two addresses in every network are reserved:
  - **Network address** (all host bits = 0) → e.g., 192.168.1.**0**
  - **Broadcast address** (all host bits = 1) → e.g., 192.168.1.**255**
- These two cannot be assigned to any host
- `no shutdown` must be used — Cisco router interfaces are **shutdown by default**

## ❓ Things I Was Confused About
- Why is 127.x.x.x not usable Class A? (Reserved for loopback testing)
- Why are network and broadcast addresses unusable? (Network address identifies the network itself; broadcast is used to reach all hosts)
- Why does `show ip int brief` show "administratively down"? (Interface was not enabled with `no shutdown`)
