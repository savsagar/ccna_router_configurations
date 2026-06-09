# Day 16 - VLANs (Part 1)

## 📌 Topics Covered
- What is a VLAN and why it is needed
- Default VLAN behaviour without VLANs
- How VLANs split broadcast domains on a switch
- Creating VLANs and assigning names
- Access ports — what they are and how to configure them
- show vlan brief output explained

---

## 🖥️ Key Concepts

### What is a VLAN?
- **VLAN = Virtual Local Area Network**
- Logically divides a single physical switch into multiple separate networks
- Devices in different VLANs **cannot communicate directly** — they need a router
- Each VLAN = its own **broadcast domain**
- Without VLANs, all devices on a switch are in the same broadcast domain

### Why Use VLANs?
| Reason | Explanation |
|---|---|
| **Security** | HR and Engineering traffic stays separated even on same switch |
| **Performance** | Smaller broadcast domains = less unnecessary broadcast traffic |
| **Flexibility** | Logically group users regardless of physical location |
| **Cost Saving** | No need for separate physical switches per department |

---

### VLAN Ranges

| Range | Type | Description |
|---|---|---|
| VLAN 1 | Default VLAN | All ports belong here by default — cannot be deleted |
| VLAN 2 – 1001 | Normal range | Standard VLANs — create and use these |
| VLAN 1002 – 1005 | Legacy | Reserved for old Token Ring / FDDI — avoid |
| VLAN 1006 – 4094 | Extended range | Used in large networks / service providers |

> 💡 **VLAN 1** is the default — every switch port is in VLAN 1 unless changed

---

### Access Ports
- An **access port** belongs to **one single VLAN**
- The connected device (PC, printer, server) does NOT know about VLANs
- The switch tags traffic internally but the end device never sees a VLAN tag
- Used to connect end hosts to the switch

---

### show vlan brief — Output Explained

```
SW1# show vlan brief

VLAN  Name                Status    Ports
----  ----                ------    -----
1     default             active    Fa0/1, Fa0/2, Fa0/5
10    ENGINEERING         active    Fa0/3, Fa0/4
20    HR                  active    Fa0/6, Fa0/7
30    SALES               active    Fa0/8
1002  fddi-default        act/unsup
1003  token-ring-default  act/unsup
1004  fddinet-default     act/unsup
1005  trnet-default       act/unsup
```

| Column | Meaning |
|---|---|
| VLAN | VLAN number |
| Name | VLAN name (default = "VLAN 000X" if not named) |
| Status | active = working; act/unsup = legacy, unsupported |
| Ports | Access ports assigned to this VLAN |

> 💡 Trunk ports do NOT appear in `show vlan brief` — use `show interfaces trunk`

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show vlan brief` | Privileged EXEC | Lists all VLANs and their assigned access ports |
| `show vlan` | Privileged EXEC | Full VLAN details |
| `show vlan id [vlan-id]` | Privileged EXEC | Info about a specific VLAN |
| `vlan [vlan-id]` | Global Config | Creates the VLAN and enters VLAN config mode |
| `name [vlan-name]` | VLAN Config | Assigns a name to the VLAN |
| `interface [int]` | Global Config | Enter interface config |
| `switchport mode access` | Interface Config | Sets port as an access port |
| `switchport access vlan [id]` | Interface Config | Assigns the port to a VLAN |
| `no vlan [vlan-id]` | Global Config | Deletes a VLAN |

---

## 🔧 Lab Configuration — Creating VLANs and Assigning Access Ports

```
Topology:
PC1 (Eng) ─── Fa0/1 ┐
PC2 (Eng) ─── Fa0/2 ┤── SW1 ── (to router later)
PC3 (HR)  ─── Fa0/3 ┤
PC4 (HR)  ─── Fa0/4 ┘

! ---- Step 1: Create VLANs ----
SW1(config)# vlan 10
SW1(config-vlan)# name ENGINEERING
SW1(config-vlan)# exit

SW1(config)# vlan 20
SW1(config-vlan)# name HR
SW1(config-vlan)# exit

SW1(config)# vlan 30
SW1(config-vlan)# name SALES
SW1(config-vlan)# exit

! ---- Step 2: Assign Access Ports ----
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/3
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/4
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20
SW1(config-if)# exit

! ---- Using interface range (faster) ----
SW1(config)# interface range fastEthernet 0/1 - 2
SW1(config-if-range)# switchport mode access
SW1(config-if-range)# switchport access vlan 10
SW1(config-if-range)# exit

SW1(config)# interface range fastEthernet 0/3 - 4
SW1(config-if-range)# switchport mode access
SW1(config-if-range)# switchport access vlan 20
SW1(config-if-range)# exit

! ---- Verify ----
SW1# show vlan brief
SW1# show interfaces fastEthernet 0/1 switchport
```

---

## 🖧 Network Topology

![Day 16 Topology](topology.png)

---

## 🔧 Lab Summary
- Created VLAN 10 (ENGINEERING), VLAN 20 (HR), VLAN 30 (SALES) on the switch
- Assigned access ports to each VLAN using `switchport access vlan`
- Used `interface range` to configure multiple ports at once
- Verified VLAN assignment with `show vlan brief`
- Tested that PC1 (VLAN 10) could NOT ping PC3 (VLAN 20) — confirmed separation
- Tested that PC1 (VLAN 10) CAN ping PC2 (VLAN 10) — confirmed same VLAN works

---

## 💡 Important Notes
- If you assign a port to a VLAN that doesn't exist yet, the switch **automatically creates** that VLAN
- VLAN 1 is the default VLAN and **cannot be deleted or renamed**
- VLANs 1002–1005 are **reserved** — do not use them
- Deleting a VLAN does NOT automatically move its ports — those ports lose connectivity
- Always create the VLAN first, then assign ports to it — cleaner practice
- `switchport mode access` should always be set explicitly even if it's already the default

---

## ❓ Things I Was Confused About
- Can devices in different VLANs on the same switch communicate? (No — VLANs are separate broadcast domains; a router is needed for inter-VLAN communication)
- What happens to a port if its VLAN is deleted? (The port becomes inactive — it still shows the old VLAN number but traffic is dropped)
- Is VLAN 1 special? (Yes — it's the default and cannot be deleted; avoid using it for user traffic as a security best practice)
