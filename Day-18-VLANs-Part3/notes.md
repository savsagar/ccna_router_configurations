# Day 18 - VLANs (Part 3) — Router on a Stick (ROAS)

## 📌 Topics Covered
- Why inter-VLAN routing is needed
- Three methods of inter-VLAN routing
- Router on a Stick (ROAS) — what it is and how it works
- Sub-interfaces on a router
- encapsulation dot1q command
- Native VLAN on ROAS
- Full ROAS lab configuration

---

## 🖥️ Key Concepts

### Why Inter-VLAN Routing is Needed
- Devices in different VLANs are in separate broadcast domains
- They **cannot communicate without a Layer 3 device (router)**
- A router is needed to route packets between VLANs

---

### Three Methods of Inter-VLAN Routing

| Method | Description | Drawback |
|---|---|---|
| **Legacy** | Separate physical router interface per VLAN | Needs many router ports — doesn't scale |
| **Router on a Stick (ROAS)** | One physical link, multiple sub-interfaces | Single link = potential bottleneck |
| **Layer 3 Switch (SVI)** | Switch does routing internally with SVIs | More expensive switch needed |

---

### Router on a Stick (ROAS)

- Uses **one physical interface** on the router divided into **logical sub-interfaces**
- The switch port connected to the router is configured as a **trunk**
- Each sub-interface handles one VLAN's traffic
- The router receives tagged frames, routes between VLANs, sends back out tagged

```
   PC1 (VLAN10) ──┐                    ┌── [VLAN10 sub-int: 192.168.10.1]
                   ├── SW1 ══trunk══ R1 ┤
   PC2 (VLAN20) ──┘                    └── [VLAN20 sub-int: 192.168.20.1]
```

---

### Sub-Interfaces

- A **sub-interface** is a virtual interface created on a physical interface
- Named as: `[physical-interface].[sub-interface-number]`
  - Example: `GigabitEthernet0/0.10` → sub-interface 10 on Gi0/0
- Sub-interface number does **not** have to match the VLAN number — but it is **best practice** to match them
- Each sub-interface gets its own IP address (acts as default gateway for its VLAN)

---

### How ROAS Works — Step by Step

```
Step 1: PC1 (192.168.10.10, VLAN10) wants to reach PC2 (192.168.20.10, VLAN20)
Step 2: PC1 sends frame to its default gateway = R1's VLAN10 sub-interface (192.168.10.1)
Step 3: Switch tags the frame with VLAN10 tag and sends out trunk to R1
Step 4: R1 receives tagged frame on Gi0/0.10 (the VLAN10 sub-interface)
Step 5: R1 strips tag, reads destination IP 192.168.20.10
Step 6: R1 checks routing table → 192.168.20.0/24 is connected on Gi0/0.20
Step 7: R1 re-tags the frame with VLAN20 tag, sends back out trunk to switch
Step 8: Switch receives VLAN20-tagged frame, forwards to PC2's access port
Step 9: PC2 receives the packet ✅
```

---

### Native VLAN on ROAS
- If a sub-interface needs to handle the **Native VLAN** (untagged traffic):
  - Use `encapsulation dot1q [vlan-id] native` on that sub-interface
  - OR configure the native VLAN on the physical interface itself

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `interface [int].[subint-number]` | Global Config | Creates and enters a sub-interface |
| `encapsulation dot1q [vlan-id]` | Sub-Interface Config | Assigns VLAN tag this sub-interface handles |
| `encapsulation dot1q [vlan-id] native` | Sub-Interface Config | Marks this sub-interface as the native VLAN handler |
| `ip address [ip] [mask]` | Sub-Interface Config | Assigns IP — this becomes the default gateway for that VLAN |
| `no shutdown` | Physical Interface | Must be run on the physical interface (sub-interfaces come up with it) |
| `show ip route` | Privileged EXEC | Verify connected routes for each sub-interface |
| `show interfaces [int].[subint]` | Privileged EXEC | View sub-interface details |
| `show vlans` | Privileged EXEC (Router) | Shows VLAN encapsulation on sub-interfaces |

---

## 🔧 Lab Configuration — Full ROAS Setup

```
Topology:
PC1: 192.168.10.10/24  GW: 192.168.10.1  VLAN 10
PC2: 192.168.20.10/24  GW: 192.168.20.1  VLAN 20
PC3: 192.168.30.10/24  GW: 192.168.30.1  VLAN 30

PC1 ─ Fa0/1 ─┐
PC2 ─ Fa0/2 ─┼─ SW1 ─ Gi0/1 (trunk) ══════ Gi0/0 ─ R1
PC3 ─ Fa0/3 ─┘

! ════════════════════════════════
! SWITCH CONFIGURATION
! ════════════════════════════════

! Create VLANs
SW1(config)# vlan 10
SW1(config-vlan)# name ENGINEERING
SW1(config-vlan)# exit
SW1(config)# vlan 20
SW1(config-vlan)# name HR
SW1(config-vlan)# exit
SW1(config)# vlan 30
SW1(config-vlan)# name SALES
SW1(config-vlan)# exit

! Assign access ports
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/3
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 30
SW1(config-if)# exit

! Configure trunk port to R1
SW1(config)# interface gigabitEthernet 0/1
SW1(config-if)# switchport trunk encapsulation dot1q
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30
SW1(config-if)# switchport trunk native vlan 999
SW1(config-if)# end

! ════════════════════════════════
! ROUTER CONFIGURATION (ROAS)
! ════════════════════════════════

! Physical interface must be UP — no IP needed here
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# no shutdown
R1(config-if)# exit

! Sub-interface for VLAN 10
R1(config)# interface gigabitEthernet 0/0.10
R1(config-subif)# encapsulation dot1q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0
R1(config-subif)# exit

! Sub-interface for VLAN 20
R1(config)# interface gigabitEthernet 0/0.20
R1(config-subif)# encapsulation dot1q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0
R1(config-subif)# exit

! Sub-interface for VLAN 30
R1(config)# interface gigabitEthernet 0/0.30
R1(config-subif)# encapsulation dot1q 30
R1(config-subif)# ip address 192.168.30.1 255.255.255.0
R1(config-subif)# end

! ════════════════════════════════
! VERIFICATION
! ════════════════════════════════
R1# show ip route
R1# show interfaces gigabitEthernet 0/0.10
R1# show interfaces gigabitEthernet 0/0.20
R1# show interfaces gigabitEthernet 0/0.30
SW1# show interfaces trunk
SW1# show vlan brief
```

---

## 🖧 Network Topology

![Day 18 Topology](topology.png)

---

## 🔧 Lab Summary
- Built a ROAS topology: 3 PCs across 3 VLANs connected to one switch, one router
- Configured VLANs 10, 20, 30 on the switch with proper names
- Set access ports for each PC in the correct VLAN
- Configured trunk port on switch facing the router (allowed VLANs 10, 20, 30)
- Created sub-interfaces on router: Gi0/0.10, Gi0/0.20, Gi0/0.30
- Applied `encapsulation dot1q` and assigned gateway IPs to each sub-interface
- Set default gateways on PCs to the corresponding sub-interface IPs
- Verified `show ip route` shows 3 connected routes (one per sub-interface)
- Tested inter-VLAN ping: PC1 → PC2 → PC3 — all successful ✅

---

## 💡 Important Notes
- The **physical interface** (`Gi0/0`) must have `no shutdown` — sub-interfaces inherit this state
- The **physical interface** itself does NOT need an IP address in ROAS
- Sub-interface number and VLAN number don't have to match — but always match them for clarity
- `encapsulation dot1q` **must** be configured before assigning an IP to the sub-interface
- The switch port connecting to the router **must be a trunk port** — not an access port
- Each sub-interface IP = **default gateway** for PCs in that VLAN
- ROAS is great for small networks — for larger/enterprise use Layer 3 switch SVIs instead

---

## ❓ Things I Was Confused About
- Why does the physical interface not need an IP? (The sub-interfaces handle the traffic — the physical interface just needs to be up to pass frames)
- What if I forget `no shutdown` on the physical interface? (All sub-interfaces go down too — they depend on the physical interface being up)
- Does sub-interface number have to match VLAN number? (No — but it's strongly recommended for readability and to avoid configuration errors)
- Why is ROAS called "on a stick"? (One cable connects the router to the switch — it looks like a stick; all inter-VLAN traffic goes up and back down that single link)
