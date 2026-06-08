# Day 14 - Subnetting (Part 2) — Class B & Class A

## 📌 Topics Covered
- Subnetting Class B networks (/17 to /30)
- Subnetting Class A networks (/9 to /30)
- Applying the magic number method across different octets
- Determining which octet the "interesting" octet is
- Practice problems for Class B and Class A

---

## 🖥️ Key Concepts

### Which Octet is the "Interesting" Octet?
The interesting octet is where subnetting happens — where the subnet mask is **not** 255 or 0.

| Prefix Range | Interesting Octet |
|---|---|
| /8 to /15 | 2nd octet |
| /16 to /23 | 3rd octet |
| /24 to /30 | 4th octet |

---

### 🔢 Class B Subnetting Cheat Sheet (/16 base)

| Prefix | Subnet Mask | Subnets | Hosts/Subnet | Block Size (3rd octet) |
|--------|-------------|---------|--------------|------------------------|
| /17 | 255.255.128.0 | 2 | 32,766 | 128 |
| /18 | 255.255.192.0 | 4 | 16,382 | 64 |
| /19 | 255.255.224.0 | 8 | 8,190 | 32 |
| /20 | 255.255.240.0 | 16 | 4,094 | 16 |
| /21 | 255.255.248.0 | 32 | 2,046 | 8 |
| /22 | 255.255.252.0 | 64 | 1,022 | 4 |
| /23 | 255.255.254.0 | 128 | 510 | 2 |
| /24 | 255.255.255.0 | 256 | 254 | 1 |

> For /25 to /30 on a Class B → same as Class C in the 4th octet

---

### 📝 Subnetting Class B — Step by Step

**Given:** `172.16.0.0 /18` — find all subnet details

**Step 1:** Identify interesting octet → /18 is in the **3rd octet**
**Step 2:** Magic Number = 256 - 192 = **64** (applied to 3rd octet)
**Step 3:** Subnets are multiples of 64 in the 3rd octet:

| Subnet | Network Address | First Host | Last Host | Broadcast |
|--------|----------------|------------|-----------|-----------|
| 1st | 172.16.0.0 | 172.16.0.1 | 172.16.63.254 | 172.16.63.255 |
| 2nd | 172.16.64.0 | 172.16.64.1 | 172.16.127.254 | 172.16.127.255 |
| 3rd | 172.16.128.0 | 172.16.128.1 | 172.16.191.254 | 172.16.191.255 |
| 4th | 172.16.192.0 | 172.16.192.1 | 172.16.255.254 | 172.16.255.255 |

> 💡 When the interesting octet is the 3rd, the 4th octet goes from **0 to 255** within each subnet block

---

### 🔍 Given an IP — Find Its Subnet (Class B)

**Given:** `172.16.85.100 /18` — which subnet?

1. Interesting octet = 3rd → look at **85**
2. Magic number = 64
3. 85 ÷ 64 = 1 remainder 21 → it's in the **64 block**
4. Network Address = **172.16.64.0**
5. Broadcast = **172.16.127.255**
6. First Host = **172.16.64.1**
7. Last Host = **172.16.127.254**

---

### 🔢 Class A Subnetting Cheat Sheet (/8 base)

| Prefix | Subnet Mask | Subnets | Hosts/Subnet | Block Size (2nd octet) |
|--------|-------------|---------|--------------|------------------------|
| /9 | 255.128.0.0 | 2 | 8,388,606 | 128 |
| /10 | 255.192.0.0 | 4 | 4,194,302 | 64 |
| /11 | 255.224.0.0 | 8 | 2,097,150 | 32 |
| /12 | 255.240.0.0 | 16 | 1,048,574 | 16 |
| /13 | 255.248.0.0 | 32 | 524,286 | 8 |
| /14 | 255.252.0.0 | 64 | 262,142 | 4 |
| /15 | 255.254.0.0 | 128 | 131,070 | 2 |
| /16 | 255.255.0.0 | 256 | 65,534 | 1 |

> For /17 to /30 on Class A → apply magic number to 3rd or 4th octet respectively

---

### 📝 Subnetting Class A — Step by Step

**Given:** `10.0.0.0 /11` — find all subnet details

**Step 1:** Interesting octet = **2nd**
**Step 2:** Magic Number = 256 - 224 = **32**
**Step 3:** Subnets are multiples of 32 in the 2nd octet:

| Subnet | Network Address | First Host | Last Host | Broadcast |
|--------|----------------|------------|-----------|-----------|
| 1st | 10.0.0.0 | 10.0.0.1 | 10.31.255.254 | 10.31.255.255 |
| 2nd | 10.32.0.0 | 10.32.0.1 | 10.63.255.254 | 10.63.255.255 |
| 3rd | 10.64.0.0 | 10.64.0.1 | 10.95.255.254 | 10.95.255.255 |
| 4th | 10.96.0.0 | 10.96.0.1 | 10.127.255.254 | 10.127.255.255 |

---

### 🔍 Given an IP — Find Its Subnet (Class A)

**Given:** `10.45.200.50 /11` — which subnet?

1. Interesting octet = 2nd → look at **45**
2. Magic number = 32
3. 45 ÷ 32 = 1 remainder 13 → it's in the **32 block**
4. Network Address = **10.32.0.0**
5. Broadcast = **10.63.255.255**
6. First Host = **10.32.0.1**
7. Last Host = **10.63.255.254**

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show ip route` | Privileged EXEC | Shows routing table — lists all known subnets |
| `show ip interface brief` | Privileged EXEC | Verify IP and subnet mask on interfaces |

---

## 🔧 Lab Configuration — Class B Subnetting

```
! Assigning IPs from 172.16.0.0/18 subnets

! Router 1 - First subnet
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 172.16.0.1 255.255.192.0
R1(config-if)# no shutdown
R1(config-if)# exit

! Router 1 - Second subnet
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 172.16.64.1 255.255.192.0
R1(config-if)# no shutdown
R1(config-if)# end

R1# show ip route
R1# show ip interface brief
```

---

## 🔧 Lab Summary
- Practiced subnetting a Class B /16 into /18, /20, /22, /24 subnets
- Practiced subnetting a Class A /8 into /11, /13, /16 subnets
- Identified which octet the magic number applies to for each prefix
- Calculated network address, broadcast, first/last host for each subnet
- Assigned subnets to router interfaces and verified with `show ip route`

---

## 💡 Important Notes
- The magic number method works the same regardless of class — just apply it to the **correct octet**
- For Class B subnets /25–/30, the interesting octet shifts to the **4th octet**
- For Class A subnets /17–/23, the interesting octet shifts to the **3rd octet**
- For Class A subnets /25–/30, the interesting octet shifts to the **4th octet**
- Class A provides far more hosts but much harder to subnet mentally — practice!

---

## ❓ Things I Was Confused About
- How to know which octet the magic number applies to? (Count prefix bits — which octet is partially masked?)
- What happens to the other octets? (Octets before the interesting octet are fixed; octets after go 0–255 within each subnet)
- Can a Class A network use a /24 mask? (Yes! Classless subnetting allows any mask on any network)
