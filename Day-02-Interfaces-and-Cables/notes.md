# Day 02 - Interfaces and Cables

## 📌 Topics Covered
- Ethernet Standards (IEEE 802.3)
- UTP (Unshielded Twisted Pair) copper cables
- Straight-through vs Crossover cables
- Auto MDI-X feature
- Fiber Optic cables (Single-mode vs Multi-mode)
- SFP transceivers

## 🖥️ Key Concepts

### Ethernet Standards (Copper - UTP)
| Standard | Speed | Cable Required | Max Distance |
|---|---|---|---|
| 10BASE-T | 10 Mbps | Cat3 | 100m |
| 100BASE-TX | 100 Mbps | Cat5 | 100m |
| 1000BASE-T | 1 Gbps | Cat5e / Cat6 | 100m |
| 10GBASE-T | 10 Gbps | Cat6a | 100m |

### UTP Cable Types
| Cable Type | Connects |
|---|---|
| **Straight-through** | Different device types → PC to Switch, Switch to Router |
| **Crossover** | Same device types → PC to PC, Switch to Switch, Router to Router |

> 💡 **Auto MDI-X**: Modern Cisco devices detect cable type automatically — no need to worry about straight-through vs crossover on newer equipment.

### Pin Usage
| Standard | Pins Used |
|---|---|
| 10BASE-T & 100BASE-TX | Pins 1, 2 (Tx) and 3, 6 (Rx) — 2 pairs |
| 1000BASE-T & 10GBASE-T | Pins 1–8 — all 4 pairs (bidirectional) |

### Fiber Optic Cable Types
| Type | Core Size | Light Source | Distance | Cost |
|---|---|---|---|---|
| **Multi-Mode (MMF)** | Wider | LED | Up to ~500m | Cheaper |
| **Single-Mode (SMF)** | Narrow | Laser | Many km | Expensive |

### Fiber Ethernet Standards
| Standard | Speed | Fiber Type | Max Distance |
|---|---|---|---|
| 1000BASE-LX | 1 Gbps | SMF / MMF | 5km (SMF) / 550m (MMF) |
| 10GBASE-SR | 10 Gbps | MMF | ~400m |
| 10GBASE-LR | 10 Gbps | SMF | 10km |
| 10GBASE-ER | 10 Gbps | SMF | 30km |

### SFP Transceivers
- **SFP** = Small Form-factor Pluggable
- Used to connect fiber cables to switches/routers
- Plugged into SFP ports on the device
- Different SFPs for different fiber standards

## ⌨️ Commands Learned
| Command | Mode | What it does |
|---|---|---|
| `show interfaces` | Privileged EXEC | Shows detailed info about all interfaces |
| `show interfaces [int]` | Privileged EXEC | Shows details of a specific interface |
| `show ip interface brief` | Privileged EXEC | Summary of all interfaces and status |

## 🔧 Lab Summary
- Identified different cable types in Packet Tracer
- Connected devices using correct cable type (straight-through / crossover)
- Observed interface speed and duplex settings
- Connected fiber devices using SFP ports

## 💡 Important Notes
- UTP copper cables max out at **100 meters**
- Fiber is immune to **EMI (Electromagnetic Interference)** — copper is not
- Fiber is preferred for **long distances** and **between buildings**
- The "T" in 1000BASE-**T** = Twisted pair (copper)
- The "X" or "R" in fiber standards = fiber type indicator

## ❓ Things I Was Confused About
- When to use MMF vs SMF (use MMF for short distances inside building, SMF for long distances/between buildings)
- Why all 4 pairs are needed for Gigabit but only 2 for Fast Ethernet (higher speed needs more bandwidth = more pairs)
