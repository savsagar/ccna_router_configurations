# Day 08 - IPv4 Addressing (Part 2)

## 📌 Topics Covered
- Calculating Maximum Hosts per Network
- Network Address and Broadcast Address
- Configuring IP Addresses on Cisco Routers
- Interface status (up/up, up/down, down/down)
- Loopback Interfaces
- show ip interface brief output explained

## 🖥️ Key Concepts

### Calculating Maximum Usable Hosts
**Formula:**  2^(host bits) - 2

| CIDR | Host Bits | Total Addresses | Usable Hosts |
|---|---|---|---|
| /8 | 24 | 16,777,216 | 16,777,214 |
| /16 | 16 | 65,536 | 65,534 |
| /24 | 8 | 256 | 254 |
| /25 | 7 | 128 | 126 |
| /26 | 6 | 64 | 62 |
| /27 | 5 | 32 | 30 |
| /28 | 4 | 16 | 14 |
| /29 | 3 | 8 | 6 |
| /30 | 2 | 4 | 2 |
| /31 | 1 | 2 | 0 (special use) |
| /32 | 0 | 1 | 1 (host route) |

> 💡 We subtract **2** because:
> - **-1** for the Network Address (all host bits = 0)
> - **-1** for the Broadcast Address (all host bits = 1)

### Network Address and Broadcast Address
Given: `192.168.5.0 /24`
| Type | Address | Explanation |
|---|---|---|
| Network Address | `192.168.5.0` | All host bits are 0 — identifies the network |
| First Usable Host | `192.168.5.1` | First assignable IP |
| Last Usable Host | `192.168.5.254` | Last assignable IP |
| Broadcast Address | `192.168.5.255` | All host bits are 1 — reaches all hosts |

### show ip interface brief — Output Explained
```
Interface        IP-Address      OK?  Method  Status                Protocol
GigabitEthernet0/0  192.168.1.1  YES  manual  up                    up
GigabitEthernet0/1  unassigned   YES  unset   administratively down down
GigabitEthernet0/2  10.0.0.1     YES  manual  up                    up
```

| Status | Protocol | Meaning |
|---|---|---|
| `up` | `up` | ✅ Interface working — Layer 1 and Layer 2 OK |
| `up` | `down` | ⚠️ Physical connection OK but Layer 2 issue (e.g., encapsulation mismatch) |
| `administratively down` | `down` | ❌ Manually shut down with `shutdown` command |
| `down` | `down` | ❌ Physical issue — cable unplugged or other device is down |

### Interface Status Fields
- **Status** = Layer 1 (Physical) — is there a signal/connection?
- **Protocol** = Layer 2 (Data Link) — is the connection operational?

### Loopback Interface
- A **virtual interface** — not a physical port
- Always stays **up/up** as long as the router is on (never goes down)
- Used for: management, BGP router-ID, testing, identifying the router
- Can be assigned any IP address (usually a /32)

## ⌨️ Commands Learned
| Command | Mode | What it does |
|---|---|---|
| `interface loopback [number]` | Global Config | Creates/enters loopback interface |
| `ip address [ip] [mask]` | Interface Config | Assigns IP to the interface |
| `no shutdown` | Interface Config | Enables the interface (brings it up) |
| `shutdown` | Interface Config | Disables the interface manually |
| `show ip interface brief` | Privileged EXEC | Summary of all interfaces and status |
| `show interfaces [int]` | Privileged EXEC | Detailed info including MAC, bandwidth, errors |
| `description [text]` | Interface Config | Adds a description/label to the interface |

## 🔧 Lab Configuration — Full Router Interface Setup

```
R1> enable
R1# configure terminal

! Configure GigabitEthernet 0/0
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# description ## Link to SW1 ##
R1(config-if)# no shutdown
R1(config-if)# exit

! Configure GigabitEthernet 0/1
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# description ## Link to R2 ##
R1(config-if)# no shutdown
R1(config-if)# exit

! Configure Loopback Interface
R1(config)# interface loopback 0
R1(config-if)# ip address 1.1.1.1 255.255.255.255
R1(config-if)# exit

! Save configuration
R1(config)# end
R1# write memory

! Verify
R1# show ip interface brief
R1# show interfaces gigabitEthernet 0/0
```

## 🔧 Lab Summary
- Practiced calculating network address, broadcast, and usable host range for given IPs
- Configured IP addresses on multiple router interfaces
- Added descriptions to interfaces for documentation
- Configured a loopback interface with a /32 address
- Used `show ip interface brief` to verify all interface states
- Practiced enabling and disabling interfaces with `no shutdown` and `shutdown`
- Saved all configs with `write memory`

## 💡 Important Notes
- Cisco router interfaces are **shutdown by default** — always use `no shutdown`
- Cisco switch interfaces are **NOT shutdown by default** — they come up automatically
- Always add a `description` to interfaces — good practice for documentation
- /31 networks have no usable hosts in traditional subnetting (but RFC 3021 allows point-to-point links to use /31)
- /32 is used for **host routes** and loopback addresses — one single IP
- Loopback interfaces are extremely useful in real networks for router identification

## ❓ Things I Was Confused About
- Why subtract 2 from host count? (Network address and broadcast address are not assignable)
- Why use /30 for point-to-point links? (Only 2 usable hosts needed — one for each end of the link, wastes least IPs)
- Why is loopback always /32? (Loopback represents the router itself — just one address needed, no network)
- Difference between `down/down` and `administratively down/down`? (admin down = manually shut, down/down = physical problem)
