# Day 10 - IPv4 Header

## 📌 Topics Covered
- Structure of the IPv4 Packet Header
- All IPv4 header fields and their purpose
- TTL (Time to Live) and why it matters
- Protocol field values
- Fragmentation fields (Identification, Flags, Fragment Offset)
- Difference between IPv4 Header and Ethernet Frame Header

---

## 🖥️ Key Concepts

### IPv4 Packet Header — Full Structure

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |   DSCP    |ECN|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|    Fragment Offset      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |        Header Checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         Source IP Address                     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      Destination IP Address                   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options (if IHL > 5)                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

---

### IPv4 Header Fields — Explained

| Field | Size | Purpose |
|---|---|---|
| **Version** | 4 bits | IP version — always **4** for IPv4 (0100 in binary) |
| **IHL** (Internet Header Length) | 4 bits | Header length in 4-byte increments — minimum value is **5** (= 20 bytes) |
| **DSCP** (Differentiated Services Code Point) | 6 bits | Used for **QoS (Quality of Service)** — prioritizes traffic like voice/video |
| **ECN** (Explicit Congestion Notification) | 2 bits | Signals network congestion without dropping packets |
| **Total Length** | 16 bits | Total size of packet (header + data) in bytes — max **65,535 bytes** |
| **Identification** | 16 bits | Identifies which fragments belong to the same original packet |
| **Flags** | 3 bits | Controls and identifies fragmentation (see below) |
| **Fragment Offset** | 13 bits | Indicates the position of this fragment in the original packet |
| **TTL** (Time to Live) | 8 bits | Decremented by 1 at each router hop — packet dropped when TTL = 0 |
| **Protocol** | 8 bits | Identifies the Layer 4 protocol inside the packet (see table below) |
| **Header Checksum** | 16 bits | Error-checking for the header only (not the data/payload) |
| **Source IP Address** | 32 bits | IP address of the sender — does **NOT** change during transit |
| **Destination IP Address** | 32 bits | IP address of the receiver — does **NOT** change during transit |
| **Options** | 0–40 bytes | Rarely used — only present if IHL > 5 |

---

### Flags Field (3 bits)

| Bit | Name | Meaning |
|---|---|---|
| Bit 0 | Reserved | Always 0 |
| Bit 1 | DF (Don't Fragment) | 1 = do NOT fragment this packet |
| Bit 2 | MF (More Fragments) | 1 = more fragments follow; 0 = last fragment |

---

### Protocol Field — Common Values

| Protocol Number | Protocol Name | Layer 4 Protocol |
|---|---|---|
| **1** | ICMP | Used by ping, traceroute |
| **6** | TCP | Reliable, connection-oriented |
| **17** | UDP | Unreliable, connectionless, fast |
| **89** | OSPF | Routing protocol (runs directly over IP) |

---

### TTL — Time to Live

- Every router that forwards a packet **decrements TTL by 1**
- When TTL reaches **0**, the router **drops** the packet and sends an ICMP "Time Exceeded" message back to the source
- Purpose: **Prevents packets from looping forever** in the network
- Default TTL values by OS:
  - Windows: **128**
  - Linux/Mac: **64**
  - Cisco IOS: **255**

```
PC (TTL=64) → Router1 (TTL=63) → Router2 (TTL=62) → Router3 (TTL=61) → Destination
```

---

### Fragmentation

- Maximum packet size is determined by the **MTU (Maximum Transmission Unit)** of the link
- Default Ethernet MTU = **1500 bytes**
- If a packet is larger than the MTU, routers **fragment** it into smaller pieces
- Fragments are reassembled at the **destination host** (not intermediate routers)
- Fragmentation fields work together:
  - **Identification** — same ID for all fragments of one packet
  - **Flags (MF bit)** — set to 1 on all fragments except the last
  - **Fragment Offset** — position of fragment in the original data

---

### IPv4 Header vs Ethernet Frame Header

| Feature | IPv4 Header | Ethernet Frame Header |
|---|---|---|
| Layer | Layer 3 | Layer 2 |
| Addresses | Source/Destination IP | Source/Destination MAC |
| Changes at each router hop? | ❌ No — stays the same | ✅ Yes — updated at every hop |
| Size | 20–60 bytes | 14 bytes (+ 4 FCS) |

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `ping [ip]` | Privileged EXEC | Sends ICMP — checks TTL and reachability |
| `traceroute [ip]` | Privileged EXEC | Shows each hop a packet takes (uses TTL) |
| `show interfaces [int]` | Privileged EXEC | Shows MTU of the interface |
| `debug ip packet` | Privileged EXEC | Shows live IP packet info (use carefully!) |

---

## 🔧 Lab Configuration

```
! Verify interface MTU
R1# show interfaces gigabitEthernet 0/0

! Test TTL using ping (extended ping)
R1# ping 10.0.0.2
R1# ping 10.0.0.2 repeat 5

! Traceroute to see each hop
R1# traceroute 192.168.3.1
```

---

## 🔧 Lab Summary
- Examined IPv4 packet headers in Packet Tracer simulation mode
- Clicked on PDUs and read each header field value
- Observed TTL decrementing at each router hop using traceroute
- Identified Protocol field values for ICMP (1) and TCP (6)
- Observed how Source and Destination IPs remain unchanged across hops

---

## 💡 Important Notes
- The IPv4 header is a **minimum of 20 bytes** (when no options are present)
- IHL minimum value of **5** means 5 × 4 = 20 bytes header length
- **Source and Destination IP never change** end-to-end — only MAC addresses change
- TTL of 0 = packet dropped + ICMP Time Exceeded sent to source — this is how `traceroute` works
- Header Checksum covers **header only** — data integrity is handled by Layer 4 (TCP)
- DSCP is important in real networks for **VoIP and video prioritization**

---

## ❓ Things I Was Confused About
- Why does TTL exist? (Prevents routing loops from circulating packets forever — without it, a misrouted packet could loop indefinitely)
- Why are IPs unchanged but MACs change at each hop? (IPs identify end-to-end source/destination; MACs identify the next hop only — new MAC frame is built at every router)
- Why is Header Checksum only for the header? (The payload is protected by upper-layer protocols like TCP which have their own checksum)
- What is DSCP used for in real life? (QoS — voice calls get higher priority than file downloads on the same link)
