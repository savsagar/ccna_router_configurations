# Day 17 - VLANs (Part 2) — Trunk Ports

## 📌 Topics Covered
- What is a Trunk Port and why it is needed
- Access Port vs Trunk Port comparison
- 802.1Q VLAN Tagging
- Native VLAN concept and security implications
- Allowed VLANs on a trunk
- Configuring trunk ports on Cisco switches
- show interfaces trunk output explained

---

## 🖥️ Key Concepts

### The Problem That Trunking Solves
- Without trunking: to carry 3 VLANs between two switches, you'd need **3 separate physical links** (one per VLAN) — wasteful
- With trunking: **one physical link** carries traffic for ALL VLANs simultaneously
- A trunk port tags each frame with its VLAN ID so the receiving device knows which VLAN it belongs to

---

### Access Port vs Trunk Port

| Feature | Access Port | Trunk Port |
|---|---|---|
| VLANs carried | One VLAN only | Multiple VLANs |
| Connected to | End hosts (PC, printer) | Other switches, routers |
| VLAN tagging | No tag — device unaware | Tags frames with 802.1Q |
| show vlan brief | Port listed here | Port NOT listed here |

---

### 802.1Q — VLAN Tagging

- **IEEE 802.1Q** is the standard for VLAN tagging on trunk links
- Inserts a **4-byte tag** into the Ethernet frame between Source MAC and Type fields:

```
Original Frame:
| Dst MAC | Src MAC | Type | Data | FCS |

Tagged Frame (802.1Q):
| Dst MAC | Src MAC | 802.1Q Tag | Type | Data | FCS |
                     ↑
              4-byte tag inserted here
```

### 802.1Q Tag Structure (4 bytes)

| Field | Size | Value |
|---|---|---|
| TPID (Tag Protocol ID) | 16 bits | Always **0x8100** — identifies this as an 802.1Q tag |
| PCP (Priority Code Point) | 3 bits | Class of Service for QoS |
| DEI (Drop Eligible Indicator) | 1 bit | Whether frame can be dropped under congestion |
| VID (VLAN ID) | 12 bits | VLAN number — supports VLANs **0 to 4095** |

---

### Native VLAN

- The **Native VLAN** is the one VLAN whose frames are sent **untagged** across a trunk link
- Default Native VLAN on Cisco switches = **VLAN 1**
- When the receiving switch gets an untagged frame, it assigns it to the Native VLAN
- **Both sides of a trunk must have the same Native VLAN** — mismatch causes problems

#### Native VLAN Security Risk
- If an attacker is on the Native VLAN, their untagged frames can leak onto the trunk
- Best practice: **Change Native VLAN to an unused VLAN** (e.g., VLAN 999) that no users are on

---

### Allowed VLANs on a Trunk
- By default, a trunk port **allows all VLANs (1–4094)**
- You can restrict which VLANs are allowed for security and efficiency
- Only VLANs that are both **allowed** AND **exist** on the switch will be active on the trunk

---

### show interfaces trunk — Output Explained

```
SW1# show interfaces trunk

Port      Mode    Encapsulation  Status    Native VLAN
Gi0/1     on      802.1q         trunking  1

Port      Vlans allowed on trunk
Gi0/1     1-4094

Port      Vlans allowed and active in management domain
Gi0/1     1,10,20,30

Port      Vlans in spanning tree forwarding state and not pruned
Gi0/1     1,10,20,30
```

| Section | Meaning |
|---|---|
| Mode / Status | How trunk was formed and current state |
| Native VLAN | Which VLAN sends/receives untagged frames |
| Vlans allowed on trunk | Configured allowed list |
| Vlans allowed and active | Allowed AND exist on this switch |
| Vlans in spanning tree forwarding | Actually forwarding traffic |

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show interfaces trunk` | Privileged EXEC | Shows all trunk ports and their VLAN details |
| `show interfaces [int] switchport` | Privileged EXEC | Detailed switchport info for one interface |
| `switchport trunk encapsulation dot1q` | Interface Config | Sets 802.1Q encapsulation (required on older switches) |
| `switchport mode trunk` | Interface Config | Forces the port into trunk mode |
| `switchport trunk native vlan [id]` | Interface Config | Changes the Native VLAN |
| `switchport trunk allowed vlan [list]` | Interface Config | Sets the allowed VLAN list |
| `switchport trunk allowed vlan add [id]` | Interface Config | Adds a VLAN to allowed list |
| `switchport trunk allowed vlan remove [id]` | Interface Config | Removes a VLAN from allowed list |
| `switchport trunk allowed vlan all` | Interface Config | Resets allowed list to all VLANs |

---

## 🔧 Lab Configuration — Trunk Between Two Switches

```
Topology:
PC1 (VLAN10) ─ Fa0/1 ─ SW1 ─ Gi0/1 ══════ Gi0/1 ─ SW2 ─ Fa0/1 ─ PC3 (VLAN10)
PC2 (VLAN20) ─ Fa0/2 ─ SW1                        SW2 ─ Fa0/2 ─ PC4 (VLAN20)

! ---- SW1 Configuration ----

! Create VLANs
SW1(config)# vlan 10
SW1(config-vlan)# name ENGINEERING
SW1(config-vlan)# exit
SW1(config)# vlan 20
SW1(config-vlan)# name HR
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

! Configure trunk port to SW2
SW1(config)# interface gigabitEthernet 0/1
SW1(config-if)# switchport trunk encapsulation dot1q
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk native vlan 999
SW1(config-if)# switchport trunk allowed vlan 10,20
SW1(config-if)# exit

! ---- SW2 Configuration (mirror of SW1) ----
SW2(config)# vlan 10
SW2(config-vlan)# name ENGINEERING
SW2(config-vlan)# exit
SW2(config)# vlan 20
SW2(config-vlan)# name HR
SW2(config-vlan)# exit

SW2(config)# interface fastEthernet 0/1
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 10
SW2(config-if)# exit

SW2(config)# interface fastEthernet 0/2
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 20
SW2(config-if)# exit

SW2(config)# interface gigabitEthernet 0/1
SW2(config-if)# switchport trunk encapsulation dot1q
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk native vlan 999
SW2(config-if)# switchport trunk allowed vlan 10,20
SW2(config-if)# end

! ---- Verify ----
SW1# show interfaces trunk
SW1# show vlan brief
SW1# show interfaces gigabitEthernet 0/1 switchport
```

---

## 🖧 Network Topology

![Day 17 Topology](topology.png)

---

## 🔧 Lab Summary
- Built a 2-switch topology with PCs in VLAN 10 and VLAN 20
- Configured access ports on both switches for each VLAN
- Configured trunk port between the two switches using 802.1Q
- Changed Native VLAN to 999 (unused) for security
- Restricted allowed VLANs to only 10 and 20
- Verified trunk with `show interfaces trunk`
- Tested PC1 (VLAN10, SW1) can ping PC3 (VLAN10, SW2) — cross-switch same VLAN ✅
- Tested PC1 (VLAN10) cannot ping PC4 (VLAN20) without a router ❌

---

## 💡 Important Notes
- Trunk ports do NOT appear in `show vlan brief` — always use `show interfaces trunk`
- Native VLAN must match on both ends of a trunk — mismatch causes a CDP warning and traffic issues
- `switchport trunk encapsulation dot1q` is required on older Catalyst switches — newer ones only support 802.1Q so it's automatic
- Restricting allowed VLANs on trunk is good practice — only pass VLANs that actually exist on the other switch
- Removing a VLAN from the allowed list is different from deleting it — the VLAN still exists but won't pass over that trunk

---

## ❓ Things I Was Confused About
- Why doesn't the trunk port show in `show vlan brief`? (Trunk ports carry multiple VLANs — they don't belong to just one VLAN so they don't appear in that output)
- What happens if Native VLAN mismatches? (Untagged frames get placed in wrong VLAN — connectivity issues and security risk; Cisco CDP also warns about this)
- Do I need to create VLAN 999 before using it as Native VLAN? (Best practice is yes — create and name it so it's documented, even if nothing uses it)
- Why restrict allowed VLANs? (Security and efficiency — no point sending VLAN 20 traffic to a switch that has no VLAN 20 ports)
