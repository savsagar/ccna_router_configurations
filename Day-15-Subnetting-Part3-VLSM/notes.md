# Day 15 - Subnetting (Part 3) — VLSM

## 📌 Topics Covered
- What is VLSM (Variable Length Subnet Masking)
- Why VLSM is needed (IP address conservation)
- Fixed-Length Subnet Masking (FLSM) vs VLSM
- VLSM Step-by-Step Process
- Assigning subnets efficiently based on host requirements
- Point-to-point links using /30

---

## 🖥️ Key Concepts

### What is VLSM?
- **FLSM (Fixed Length)** = All subnets use the SAME prefix length → wastes IPs
- **VLSM (Variable Length)** = Each subnet uses a DIFFERENT prefix length based on how many hosts are needed → saves IPs
- VLSM = using subnets of **different sizes** within the same network

---

### Why VLSM Matters
**Scenario without VLSM (wasteful):**
- Network: 192.168.1.0/24
- You need: 3 LANs of 100 hosts, 1 LAN of 50 hosts, 3 point-to-point links
- With FLSM /25 → each subnet gives 126 hosts — point-to-point wastes 124 addresses!

**With VLSM:**
- Assign large subnets only where needed
- Assign /30 (2 usable hosts) for point-to-point links → waste only 2 addresses

---

### VLSM Golden Rule
> **Always assign the LARGEST subnet first, then work your way down to the smallest.**

This ensures you don't run out of address space.

---

### 📝 VLSM — Full Example

**Given:** `192.168.1.0 /24`

**Requirements:**
| Network | Hosts Needed |
|---|---|
| LAN A | 100 hosts |
| LAN B | 60 hosts |
| LAN C | 30 hosts |
| LAN D | 25 hosts |
| Point-to-Point Link 1 | 2 hosts |
| Point-to-Point Link 2 | 2 hosts |
| Point-to-Point Link 3 | 2 hosts |

---

**Step 1 — Sort by largest requirement first:**
100 → 60 → 30 → 25 → 2 → 2 → 2

---

**Step 2 — Assign subnets one by one:**

### LAN A — 100 hosts needed
- Need at least 102 addresses (100 hosts + network + broadcast)
- /25 gives 126 hosts ✅ (smallest subnet that fits)
- **192.168.1.0 /25**
- Range: 192.168.1.0 – 192.168.1.127
- Next available: **192.168.1.128**

---

### LAN B — 60 hosts needed
- /26 gives 62 hosts ✅
- **192.168.1.128 /26**
- Range: 192.168.1.128 – 192.168.1.191
- Next available: **192.168.1.192**

---

### LAN C — 30 hosts needed
- /27 gives 30 hosts ✅
- **192.168.1.192 /27**
- Range: 192.168.1.192 – 192.168.1.223
- Next available: **192.168.1.224**

---

### LAN D — 25 hosts needed
- /27 gives 30 hosts ✅ (next fit — /28 only gives 14, not enough)
- **192.168.1.224 /27**
- Range: 192.168.1.224 – 192.168.1.255

> ⚠️ Address space is full! In real networks this means you'd need more address space OR restructure. This shows why always assigning largest first matters — plan carefully.

---

### Better Planning Example (with room for P2P links)

**Given:** `192.168.5.0 /24`

| Subnet | Purpose | Prefix | Network Address | Range | Broadcast |
|---|---|---|---|---|---|
| 1st | LAN A (100 hosts) | /25 | 192.168.5.0 | .1 – .126 | .127 |
| 2nd | LAN B (50 hosts) | /26 | 192.168.5.128 | .129 – .190 | .191 |
| 3rd | LAN C (25 hosts) | /27 | 192.168.5.192 | .193 – .222 | .223 |
| 4th | LAN D (10 hosts) | /28 | 192.168.5.224 | .225 – .238 | .239 |
| 5th | P2P Link 1 | /30 | 192.168.5.240 | .241 – .242 | .243 |
| 6th | P2P Link 2 | /30 | 192.168.5.244 | .245 – .246 | .247 |
| 7th | P2P Link 3 | /30 | 192.168.5.248 | .249 – .250 | .251 |

**Remaining:** 192.168.5.252 – .255 (4 addresses left unused — very efficient!)

---

### Quick Host Requirement → Prefix Reference
| Hosts Needed | Minimum Prefix | Usable Hosts |
|---|---|---|
| 1–2 | /30 | 2 |
| 3–6 | /29 | 6 |
| 7–14 | /28 | 14 |
| 15–30 | /27 | 30 |
| 31–62 | /26 | 62 |
| 63–126 | /25 | 126 |
| 127–254 | /24 | 254 |

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show ip route` | Privileged EXEC | Shows routing table with all subnets |
| `show ip interface brief` | Privileged EXEC | Confirms IP and prefix on all interfaces |
| `show interfaces [int]` | Privileged EXEC | Detailed info including subnet mask |

---

## 🔧 Lab Configuration — VLSM Topology

```
! R1 — LAN A interface (192.168.5.0/25)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.5.1 255.255.255.128
R1(config-if)# description ## LAN-A - 100 Hosts ##
R1(config-if)# no shutdown
R1(config-if)# exit

! R1 — Point-to-Point link to R2 (192.168.5.240/30)
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 192.168.5.241 255.255.255.252
R1(config-if)# description ## P2P Link to R2 ##
R1(config-if)# no shutdown
R1(config-if)# exit

! R2 — LAN B interface (192.168.5.128/26)
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip address 192.168.5.129 255.255.255.192
R2(config-if)# description ## LAN-B - 50 Hosts ##
R2(config-if)# no shutdown
R2(config-if)# exit

! R2 — Point-to-Point link to R1 (192.168.5.240/30)
R2(config)# interface gigabitEthernet 0/1
R2(config-if)# ip address 192.168.5.242 255.255.255.252
R2(config-if)# description ## P2P Link to R1 ##
R2(config-if)# no shutdown
R2(config-if)# end

R1# show ip interface brief
R2# show ip interface brief
R1# show ip route
```

---

## 🔧 Lab Summary
- Built a multi-router topology in Packet Tracer with 4 LANs and 3 point-to-point links
- Assigned VLSM subnets from a single /24 address block
- Largest subnet assigned first, point-to-point links assigned last with /30
- Configured IP addresses with correct variable-length subnet masks on each interface
- Verified subnets using `show ip route` and confirmed no overlap

---

## 💡 Important Notes
- ALWAYS assign **largest subnet first** — prevents running out of space
- **Point-to-point links** always use **/30** (only 2 usable hosts needed)
- Two subnets must **never overlap** — double-check your ranges
- VLSM is the standard in real networks — FLSM is rarely used
- Each interface on a router = a different subnet
- The same address block **cannot be assigned to two different subnets**

---

## ❓ Things I Was Confused About
- Why assign largest subnet first? (If you assign small subnets first, you may not have contiguous space left for a large subnet later)
- Can two routers share the same subnet on a point-to-point link? (Yes — that's exactly what /30 is for, one IP per router end)
- What if my requirement doesn't fit any clean subnet? (Always round up to the next subnet size that fits — never down)
- How to check there's no overlap? (Write out all ranges — ensure no subnet's broadcast equals another's network address)
