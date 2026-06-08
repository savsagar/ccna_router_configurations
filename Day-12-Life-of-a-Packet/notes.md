# Day 12 - Life of a Packet

## 📌 Topics Covered
- Complete step-by-step journey of a packet from source to destination
- How Layer 2 (MAC) and Layer 3 (IP) work together at each hop
- Why MAC addresses change at every hop but IP addresses don't
- ARP at each router along the path
- Encapsulation and de-encapsulation at each device
- Full packet walk-through across a multi-router topology

---

## 🖥️ Key Concepts

### The Golden Rule of Packet Forwarding
> **IP addresses identify the END-TO-END source and destination.**
> **MAC addresses identify only the NEXT HOP.**

- Source IP / Destination IP → **never change** across the entire journey
- Source MAC / Destination MAC → **change at every single router hop**

---

### Topology Used for This Lesson

```
PC1 ──── R1 ──────── R2 ──────── R3 ──── PC2
LAN A        Link 1       Link 2       LAN B

PC1:  192.168.1.10   MAC: A1
R1:   Gi0/0: 192.168.1.1  MAC: B1  (LAN A side)
      Gi0/1: 10.0.0.1     MAC: C1  (Link 1 side)
R2:   Gi0/0: 10.0.0.2     MAC: D1  (Link 1 side)
      Gi0/1: 10.0.1.1     MAC: E1  (Link 2 side)
R3:   Gi0/0: 10.0.1.2     MAC: F1  (Link 2 side)
      Gi0/1: 192.168.3.1  MAC: G1  (LAN B side)
PC2:  192.168.3.10   MAC: H1
```

---

### Step-by-Step: PC1 Pings PC2

---

#### STEP 1 — PC1 Creates the Packet
- PC1 wants to reach `192.168.3.10` (PC2)
- PC1 checks: Is PC2 in my subnet? → No (different network)
- PC1 must send to its **Default Gateway** = R1's IP `192.168.1.1`
- PC1 creates IP packet:
  - **Source IP:** `192.168.1.10` (PC1)
  - **Destination IP:** `192.168.3.10` (PC2)

---

#### STEP 2 — PC1 Needs R1's MAC (ARP)
- PC1 knows R1's IP (`192.168.1.1`) but not its MAC
- PC1 sends **ARP Request** (broadcast) → "Who has 192.168.1.1?"
- R1 responds with **ARP Reply** → "I have 192.168.1.1, my MAC is B1"
- PC1 stores this in its ARP cache

---

#### STEP 3 — PC1 Sends Frame to R1
PC1 encapsulates the IP packet in an Ethernet frame:

| Field | Value |
|---|---|
| Source MAC | A1 (PC1's MAC) |
| Destination MAC | B1 (R1 Gi0/0 MAC) |
| Source IP | 192.168.1.10 (PC1) |
| Destination IP | 192.168.3.10 (PC2) |

---

#### STEP 4 — R1 Receives and Processes the Frame
1. R1 checks Destination MAC = B1 ✅ (that's me — accept it)
2. R1 **strips the Ethernet header** (de-encapsulates)
3. R1 reads Destination IP = `192.168.3.10`
4. R1 checks routing table → finds route: `192.168.3.0/24 via 10.0.0.2`
5. Next hop = R2 at `10.0.0.2` — needs R2's MAC

---

#### STEP 5 — R1 Needs R2's MAC (ARP)
- R1 sends ARP Request on Gi0/1 → "Who has 10.0.0.2?"
- R2 replies → "I have 10.0.0.2, my MAC is D1"

---

#### STEP 6 — R1 Forwards Frame to R2
R1 builds a **NEW Ethernet frame** — only MACs change, IPs stay same:

| Field | Value |
|---|---|
| Source MAC | C1 (R1 Gi0/1 MAC) ← **CHANGED** |
| Destination MAC | D1 (R2 Gi0/0 MAC) ← **CHANGED** |
| Source IP | 192.168.1.10 (PC1) ← same |
| Destination IP | 192.168.3.10 (PC2) ← same |

> 💡 TTL is decremented by 1 here (e.g., 64 → 63)

---

#### STEP 7 — R2 Receives and Processes the Frame
1. R2 checks Destination MAC = D1 ✅ (that's me)
2. R2 strips Ethernet header
3. R2 reads Destination IP = `192.168.3.10`
4. R2 checks routing table → route: `192.168.3.0/24 via 10.0.1.2`
5. Next hop = R3 at `10.0.1.2` — needs R3's MAC
6. R2 does ARP for `10.0.1.2` → gets MAC F1

---

#### STEP 8 — R2 Forwards Frame to R3
New Ethernet frame from R2:

| Field | Value |
|---|---|
| Source MAC | E1 (R2 Gi0/1 MAC) ← **CHANGED** |
| Destination MAC | F1 (R3 Gi0/0 MAC) ← **CHANGED** |
| Source IP | 192.168.1.10 ← same |
| Destination IP | 192.168.3.10 ← same |

> TTL decremented again (63 → 62)

---

#### STEP 9 — R3 Receives and Processes the Frame
1. R3 checks Destination MAC = F1 ✅ (that's me)
2. R3 strips Ethernet header
3. R3 reads Destination IP = `192.168.3.10`
4. R3 checks routing table → `192.168.3.0/24` is **directly connected** on Gi0/1
5. Destination is on R3's own LAN — needs PC2's MAC

---

#### STEP 10 — R3 Needs PC2's MAC (ARP)
- R3 sends ARP Request on Gi0/1 → "Who has 192.168.3.10?"
- PC2 responds → "I have 192.168.3.10, my MAC is H1"

---

#### STEP 11 — R3 Delivers Frame to PC2
Final Ethernet frame:

| Field | Value |
|---|---|
| Source MAC | G1 (R3 Gi0/1 MAC) ← **CHANGED** |
| Destination MAC | H1 (PC2's MAC) ← **CHANGED** |
| Source IP | 192.168.1.10 ← same |
| Destination IP | 192.168.3.10 ← same |

> TTL decremented (62 → 61)

---

#### STEP 12 — PC2 Receives the Packet
1. PC2 checks Destination MAC = H1 ✅ (that's me)
2. PC2 strips Ethernet header
3. PC2 reads Destination IP = `192.168.3.10` ✅ (that's me)
4. PC2 passes the data up to Layer 4 → Layer 7
5. **Packet delivered successfully!** ✅

---

### Summary Table — What Changes at Each Hop

| Hop | Src MAC | Dst MAC | Src IP | Dst IP |
|---|---|---|---|---|
| PC1 → R1 | A1 (PC1) | B1 (R1 Gi0/0) | 192.168.1.10 | 192.168.3.10 |
| R1 → R2 | C1 (R1 Gi0/1) | D1 (R2 Gi0/0) | 192.168.1.10 | 192.168.3.10 |
| R2 → R3 | E1 (R2 Gi0/1) | F1 (R3 Gi0/0) | 192.168.1.10 | 192.168.3.10 |
| R3 → PC2 | G1 (R3 Gi0/1) | H1 (PC2) | 192.168.1.10 | 192.168.3.10 |

**MACs = change every hop | IPs = stay the same end to end**

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show ip route` | Privileged EXEC | See routing decisions routers will make |
| `show arp` | Privileged EXEC | See ARP table — IP to MAC mappings on router |
| `show mac address-table` | Privileged EXEC (Switch) | See MAC learning on the switch |
| `traceroute [ip]` | Privileged EXEC | Shows each hop a packet takes to reach destination |
| `ping [ip] source [ip]` | Privileged EXEC | Pings from a specific source interface |
| `show ip arp` | Privileged EXEC | Alternative to show arp on routers |

---

## 🔧 Lab Configuration — Multi-Router Packet Walk

```
! ---- R1 ----
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# ip route 192.168.3.0 255.255.255.0 10.0.0.2
R1(config)# ip route 10.0.1.0 255.255.255.252 10.0.0.2

! ---- R2 ----
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip address 10.0.0.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface gigabitEthernet 0/1
R2(config-if)# ip address 10.0.1.1 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1
R2(config)# ip route 192.168.3.0 255.255.255.0 10.0.1.2

! ---- R3 ----
R3(config)# interface gigabitEthernet 0/0
R3(config-if)# ip address 10.0.1.2 255.255.255.252
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# interface gigabitEthernet 0/1
R3(config-if)# ip address 192.168.3.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# ip route 192.168.1.0 255.255.255.0 10.0.1.1

! ---- Verify ----
R1# show ip route
R1# show arp
R1# traceroute 192.168.3.10
```

---

## 🔧 Lab Summary
- Built a 3-router topology in Packet Tracer
- Used simulation mode to trace the packet hop by hop
- Observed ARP requests at each router before forwarding
- Confirmed MAC addresses change at each hop using PDU inspector
- Confirmed Source and Destination IPs never changed through all hops
- Used `traceroute` from PC1 to PC2 — observed each router's IP in output
- Used `show arp` on each router to see learned IP-to-MAC mappings

---

## 💡 Important Notes
- Each router only knows the **next hop** — it doesn't need to know the full path
- ARP only happens when the MAC of the next hop is **not already cached**
- Cached ARP entries expire — a new ARP may happen if the cache clears
- **Traceroute** works by sending packets with increasing TTL (1, 2, 3...) and reading the ICMP Time Exceeded replies from each hop
- PC's default gateway must be set correctly — without it, PC can't reach any remote network
- Switches are **transparent** in this process — they just forward frames based on MAC

---

## ❓ Things I Was Confused About
- Why does the MAC change but not the IP? (IP is the final destination identity; MAC is just the address for the immediate next device on the current segment)
- Does the router check both the MAC AND the IP? (Yes — it accepts the frame based on MAC matching its own interface, then makes forwarding decision based on Destination IP)
- What if ARP cache is already populated — does ARP still happen? (No — router uses cached MAC directly and skips ARP until entry expires)
- How does traceroute know each router's address? (Routers send back ICMP Time Exceeded when TTL hits 0 — that reply reveals the router's IP)
