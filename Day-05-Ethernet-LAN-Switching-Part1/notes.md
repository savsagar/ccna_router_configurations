# Day 05 - Ethernet LAN Switching (Part 1)

## 📌 Topics Covered
- What is a LAN (Local Area Network)
- Ethernet Frame Structure
- MAC Addresses
- How switches learn and forward frames
- MAC Address Table (CAM Table)
- Unicast, Broadcast, and Unknown Unicast flooding

## 🖥️ Key Concepts

### What is a LAN?
- A **LAN** = group of devices in the same local area connected by switches
- All devices in a LAN share the same **broadcast domain** (unless VLANs are used)
- Routers separate LANs from each other

### Ethernet Frame Structure
```
| Preamble | SFD | Destination MAC | Source MAC | Type/Length | Data (Payload) | FCS |
|  7 bytes | 1B  |     6 bytes     |   6 bytes  |   2 bytes   |  46–1500 bytes | 4B  |
```

| Field | Size | Purpose |
|---|---|---|
| Preamble | 7 bytes | Sync signal — tells receiver frame is coming |
| SFD (Start Frame Delimiter) | 1 byte | Marks end of preamble, start of frame |
| Destination MAC | 6 bytes | MAC address of the receiving device |
| Source MAC | 6 bytes | MAC address of the sending device |
| Type / EtherType | 2 bytes | Indicates Layer 3 protocol (0x0800 = IPv4, 0x86DD = IPv6) |
| Data / Payload | 46–1500 bytes | Actual data being transmitted |
| FCS (Frame Check Sequence) | 4 bytes | CRC error detection |

> 💡 Minimum frame size = **64 bytes**. If data is less than 46 bytes, **padding** is added.

### MAC Addresses
- **48-bit** address written in hexadecimal (e.g., `AA:BB:CC:DD:EE:FF`)
- Also called: **Physical Address**, **Hardware Address**, **BIA (Burned-In Address)**
- First 3 bytes = **OUI (Organizationally Unique Identifier)** → identifies the manufacturer
- Last 3 bytes = **Device-specific** → unique to each NIC
- Globally unique (no two devices should have same MAC)

### How a Switch Learns MAC Addresses
1. Switch receives a frame on a port
2. It reads the **Source MAC** address
3. Records it in the **MAC Address Table** with the port number
4. Table entry has a default aging time of **300 seconds (5 minutes)**
5. If no frame received from that MAC within aging time → entry is deleted

### How a Switch Forwards Frames
| Situation | Switch Action |
|---|---|
| Destination MAC is in MAC table | **Unicast forward** → send only out the known port |
| Destination MAC is NOT in MAC table | **Unknown Unicast Flood** → send out ALL ports except the one it came in on |
| Destination MAC is a Broadcast (FF:FF:FF:FF:FF:FF) | **Broadcast Flood** → send out ALL ports except incoming |

### Frame Types
| Type | MAC Address | Description |
|---|---|---|
| **Unicast** | Specific device MAC | One-to-one communication |
| **Broadcast** | FF:FF:FF:FF:FF:FF | One-to-all communication (sent to all in LAN) |
| **Multicast** | Starts with 01:00:5E... | One-to-many (selected group) |

## ⌨️ Commands Learned
| Command | Mode | What it does |
|---|---|---|
| `show mac address-table` | Privileged EXEC | Displays the MAC address table |
| `show mac address-table dynamic` | Privileged EXEC | Shows only dynamically learned MACs |
| `clear mac address-table dynamic` | Privileged EXEC | Clears all dynamic MAC entries |

## 🔧 Lab Configuration — Viewing MAC Table

```
Switch> enable
Switch# show mac address-table
Switch# show mac address-table dynamic
Switch# clear mac address-table dynamic
Switch# show interfaces fastEthernet 0/1
```

## 🔧 Lab Summary
- Built a topology with 3 PCs connected to a switch in Packet Tracer
- Sent pings between PCs and observed frame flooding in simulation mode
- Used `show mac address-table` to verify MAC learning
- Cleared MAC table and observed re-learning process
- Observed unknown unicast flooding before MAC was learned

## 💡 Important Notes
- Switches are **Layer 2** devices — they only look at MAC addresses, not IPs
- Flooding is normal behavior — not an attack (unless it is a MAC flood attack)
- Each switch port builds its own entry in the MAC table
- One port can have multiple MACs (e.g., if a hub or another switch is connected)
- Switches never flood known unicast frames — only unknown unicast and broadcasts

## ❓ Things I Was Confused About
- Why does flooding happen? (Switch doesn't yet know which port the destination is on)
- Difference between flooding and broadcasting (flooding = switch behavior, broadcast = frame type with FF:FF:FF:FF:FF:FF)
- What is FCS used for? (Error detection — if FCS doesn't match, frame is dropped)
