# Day 13 - Subnetting (Part 1) — Class C

## 📌 Topics Covered
- What is Subnetting and WHY we need it
- CIDR (Classless Inter-Domain Routing)
- Borrowing host bits to create subnets
- The Subnetting Magic Number method
- Calculating: Number of Subnets, Hosts per Subnet
- Finding: Network Address, Broadcast, First/Last Usable Host
- Subnetting Class C networks (/25 to /30)

---

## 🖥️ Key Concepts

### Why Subnetting?
- A company gets assigned one large network (e.g., 192.168.1.0/24)
- Without subnetting, ALL devices share one broadcast domain → slow, insecure
- Subnetting **splits** one large network into smaller sub-networks
- Benefits: Better security, less broadcast traffic, more organized

### How Subnetting Works
- We **borrow bits from the host portion** and add them to the network portion
- More network bits = more subnets BUT fewer hosts per subnet
- Fewer network bits = fewer subnets BUT more hosts per subnet

```
Original /24:   11111111.11111111.11111111.00000000
Borrow 1 bit → /25:  11111111.11111111.11111111.10000000
Borrow 2 bits → /26: 11111111.11111111.11111111.11000000
Borrow 3 bits → /27: 11111111.11111111.11111111.11100000
```

---

### 🔢 The Subnetting Cheat Sheet (Class C — /24 base)

| Prefix | Subnet Mask | Subnets | Hosts/Subnet | Block Size |
|--------|-------------|---------|--------------|------------|
| /25 | 255.255.255.128 | 2 | 126 | 128 |
| /26 | 255.255.255.192 | 4 | 62 | 64 |
| /27 | 255.255.255.224 | 8 | 30 | 32 |
| /28 | 255.255.255.240 | 16 | 14 | 16 |
| /29 | 255.255.255.248 | 32 | 6 | 8 |
| /30 | 255.255.255.252 | 64 | 2 | 4 |

> 💡 **Formulas:**
> - Number of Subnets = **2^(borrowed bits)**
> - Hosts per Subnet = **2^(remaining host bits) - 2**
> - Block Size = **256 - subnet mask last octet**

---

### 🧮 The Magic Number Method (Fastest Way)

**Magic Number = 256 - last octet of subnet mask**

Example: Subnet mask = 255.255.255.192
Magic Number = 256 - 192 = **64**

Subnets start at multiples of the magic number:
- Subnet 1: 0
- Subnet 2: 64
- Subnet 3: 128
- Subnet 4: 192

---

### 📝 Subnetting a Network — Step by Step

**Given:** `192.168.1.0 /26` — find all subnet details

**Step 1:** Find the block size (magic number)
- /26 = 255.255.255.192 → 256 - 192 = **64**

**Step 2:** List all subnets (multiples of 64)

| Subnet | Network Address | First Host | Last Host | Broadcast |
|--------|----------------|------------|-----------|-----------|
| 1st | 192.168.1.0 | 192.168.1.1 | 192.168.1.62 | 192.168.1.63 |
| 2nd | 192.168.1.64 | 192.168.1.65 | 192.168.1.126 | 192.168.1.127 |
| 3rd | 192.168.1.128 | 192.168.1.129 | 192.168.1.190 | 192.168.1.191 |
| 4th | 192.168.1.192 | 192.168.1.193 | 192.168.1.254 | 192.168.1.255 |

**Pattern for any subnet:**
- **Network Address** = first IP of the block
- **First Host** = Network Address + 1
- **Broadcast** = Next subnet's network address - 1
- **Last Host** = Broadcast - 1

---

### 🔍 Given an IP — Find Its Subnet

**Given:** `192.168.1.77 /26` — which subnet does this belong to?

1. Magic number = 64
2. Divide 77 ÷ 64 = 1 remainder 13 → it's in the **64 block**
3. Network Address = **192.168.1.64**
4. Broadcast = **192.168.1.127**
5. First usable = **192.168.1.65**
6. Last usable = **192.168.1.126**

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show ip interface brief` | Privileged EXEC | Verify IP assignments on interfaces |
| `show ip route` | Privileged EXEC | See the routing table with subnets |

---

## 🔧 Lab Configuration — Subnetting Setup

```
! Subnet: 192.168.1.0/26 assigned to R1 Gi0/0
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.192
R1(config-if)# no shutdown
R1(config-if)# exit

! Subnet: 192.168.1.64/26 assigned to R1 Gi0/1
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 192.168.1.65 255.255.255.192
R1(config-if)# no shutdown
R1(config-if)# end

R1# show ip route
R1# show ip interface brief
```

---

## 🔧 Lab Summary
- Given a /24 network, divided it into /26, /27, /28 subnets
- Identified Network Address, Broadcast, First and Last Host for each subnet
- Assigned IPs from correct subnets to router interfaces
- Verified correct subnetting with `show ip interface brief`
- Practiced the magic number method for fast subnetting

---

## 💡 Important Notes
- /31 has no usable hosts in standard subnetting (but used for point-to-point links per RFC 3021)
- /32 = a single host address — used for loopback interfaces and host routes
- The **network address** (all 0s in host bits) and **broadcast** (all 1s in host bits) are NEVER assignable
- Always double-check: does your IP fall within the correct subnet range?
- In exams, identify the subnet of a given IP quickly using the magic number

---

## ❓ Things I Was Confused About
- Why do we subtract 2 from hosts? (Network address + broadcast address are reserved)
- How to quickly find which subnet an IP belongs to? (Use magic number — divide host octet by block size)
- Can two hosts in different subnets communicate directly? (No — they need a router)
