# Day 06 - Ethernet LAN Switching (Part 2)

## 📌 Topics Covered
- ARP (Address Resolution Protocol)
- ARP Request and ARP Reply
- ARP Table (ARP Cache)
- Ping and ICMP
- MAC Address Table aging
- How end hosts communicate across a LAN step by step

## 🖥️ Key Concepts

### Why ARP is Needed
- To send data, a device needs both the **IP address** AND the **MAC address** of the destination
- IP address = known (from the user/config)
- MAC address = unknown → ARP is used to find it

### ARP — Address Resolution Protocol
- ARP maps **IP addresses → MAC addresses**
- Operates at **Layer 2 / Layer 3 boundary**
- Two types of ARP messages:
  - **ARP Request** → Broadcast (asks "who has this IP?")
  - **ARP Reply** → Unicast (answers "I have that IP, here's my MAC")

### ARP Process Step by Step
```
Step 1: PC1 wants to ping PC2 (knows PC2's IP, not MAC)
Step 2: PC1 sends ARP Request (Broadcast FF:FF:FF:FF:FF:FF)
        → "Who has 192.168.1.2? Tell 192.168.1.1"
Step 3: Switch floods ARP Request out all ports
Step 4: PC2 receives it, sends ARP Reply (Unicast back to PC1)
        → "192.168.1.2 is at AA:BB:CC:DD:EE:02"
Step 5: PC1 receives reply, stores MAC in ARP table
Step 6: PC1 can now send frames directly to PC2
```

### ARP Table (ARP Cache)
- Each device stores learned IP-to-MAC mappings in its **ARP table**
- Entries expire after a timeout period
- View it using OS commands or Cisco commands

### ICMP and Ping
- **ICMP** = Internet Control Message Protocol — used for network diagnostics
- **Ping** uses two ICMP messages:
  - **Echo Request** → sent by the source
  - **Echo Reply** → sent back by the destination
- Ping checks: Is the destination reachable? How fast is the connection?

### Ping Output Meaning
```
!!!!!   → 5 successful replies (! = success)
.....   → 5 timeouts (. = failed/no reply)
U....   → Destination unreachable message received
```

## ⌨️ Commands Learned
| Command | Mode/Device | What it does |
|---|---|---|
| `show arp` | Privileged EXEC (Router) | Shows the router's ARP table |
| `show mac address-table` | Privileged EXEC (Switch) | Shows the switch MAC table |
| `ping [ip-address]` | Privileged EXEC | Sends ICMP echo to test connectivity |
| `arp -a` | Windows PC | Shows the ARP cache on a Windows PC |

## 🔧 Lab Configuration — Testing ARP and Ping

```
! On Router - view ARP table
R1# show arp

! On Switch - view MAC address table
SW1# show mac address-table

! Ping from Router to PC
R1# ping 192.168.1.10

! Clear ARP table on router
R1# clear arp-cache
```

## 🔧 Lab Summary
- Built a topology: PC1 → Switch → Router → Switch → PC2
- Used simulation mode to trace ARP Request broadcast step by step
- Observed switch flooding the ARP request to all ports
- Verified ARP reply was unicast back to source
- Used `show arp` to confirm IP-to-MAC mapping was learned
- Tested connectivity with ping and observed ! vs . output

## 💡 Important Notes
- ARP Request = **Broadcast** (all devices in LAN receive it)
- ARP Reply = **Unicast** (only back to the requester)
- Switches do NOT have an ARP table — that is for Layer 3 devices (routers, PCs)
- Switches only have a **MAC Address Table**
- If ping shows `.....`, check: Is IP correct? Is ARP resolving? Is there a firewall?
- Gratuitous ARP = a device sends ARP reply without a request (used to update others' ARP tables)

## ❓ Things I Was Confused About
- Why is ARP request a broadcast but reply is unicast? (Request doesn't know who to send to, reply knows the requester's MAC from the request)
- Does a switch have an ARP table? (No — only MAC table. ARP is for Layer 3 devices)
- What happens if ARP request gets no reply? (Ping fails — destination is unreachable or down)
