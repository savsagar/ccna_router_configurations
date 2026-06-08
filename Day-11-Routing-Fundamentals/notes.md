# Day 11 - Routing Fundamentals

## 📌 Topics Covered
- What is Routing and why routers need it
- The Routing Table (show ip route)
- Types of Routes: Connected, Local, Static, Dynamic
- Configuring Static Routes
- Default Routes (Gateway of Last Resort)
- Longest Prefix Match rule
- Directly connected vs Remote networks

---

## 🖥️ Key Concepts

### What is Routing?
- Routing = process of forwarding packets from source network to destination network
- Routers use the **Routing Table** to decide where to send each packet
- Each router only knows the **next hop** — not the full path to destination
- Routers look at the **Destination IP** in the packet header to make decisions

---

### The Routing Table

```
R1# show ip route

Codes: C - connected, L - local, S - static, R - RIP,
       O - OSPF, B - BGP ...

      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, GigabitEthernet0/0
L        10.0.0.1/32 is directly connected, GigabitEthernet0/0
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, GigabitEthernet0/1
L        192.168.1.1/32 is directly connected, GigabitEthernet0/1
S        192.168.2.0/24 [1/0] via 10.0.0.2
S*       0.0.0.0/0 [1/0] via 10.0.0.2
```

---

### Route Types and Their Codes

| Code | Type | How it gets into the table |
|---|---|---|
| **C** | Connected | Added automatically when interface is configured with IP and `no shutdown` |
| **L** | Local | Added automatically — the router's own interface IP as a /32 host route |
| **S** | Static | Manually configured by network admin using `ip route` command |
| **R** | RIP | Learned via RIP routing protocol |
| **O** | OSPF | Learned via OSPF routing protocol |
| **B** | BGP | Learned via BGP routing protocol |

---

### Connected (C) and Local (L) Routes

- **Connected route (C)** = the network that the interface belongs to
  - Example: Interface has `192.168.1.1/24` → Connected route = `192.168.1.0/24`
- **Local route (L)** = the exact IP of the router's own interface as a /32
  - Example: Interface IP = `192.168.1.1` → Local route = `192.168.1.1/32`
- Both are added **automatically** when you assign an IP and run `no shutdown`

---

### Static Routes

- Manually configured by the admin
- Used for small networks or specific paths
- Two ways to configure:

**Method 1 — Next-Hop IP:**
```
ip route [destination-network] [subnet-mask] [next-hop-ip]
```

**Method 2 — Exit Interface:**
```
ip route [destination-network] [subnet-mask] [exit-interface]
```

**Method 3 — Both (recommended for point-to-point):**
```
ip route [destination-network] [subnet-mask] [exit-interface] [next-hop-ip]
```

---

### Default Route (Gateway of Last Resort)

- Used when no specific route matches the destination
- Catches all traffic that doesn't match any other route
- Written as `0.0.0.0/0` — matches EVERY IP address
- Shown as `S*` in routing table (S = static, * = default route candidate)

```
ip route 0.0.0.0 0.0.0.0 [next-hop-ip]
```

---

### Longest Prefix Match Rule

When multiple routes match a destination IP, the router picks the **most specific** (longest prefix):

| Destination IP | Route 1 | Route 2 | Route 3 | Winner |
|---|---|---|---|---|
| 192.168.1.25 | 0.0.0.0/0 | 192.168.1.0/24 | 192.168.1.0/26 | **/26** (most specific) |

> 💡 A /26 matches fewer IPs than /24, which matches fewer than /0 → longer prefix = more specific = preferred

---

### Administrative Distance (AD)

- Used when multiple routes to same destination exist from different sources
- Lower AD = more trusted/preferred

| Route Type | Default AD |
|---|---|
| Connected (C) | 0 |
| Static (S) | 1 |
| OSPF (O) | 110 |
| RIP (R) | 120 |
| Unknown / Unreachable | 255 |

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show ip route` | Privileged EXEC | Displays the full routing table |
| `show ip route [ip-address]` | Privileged EXEC | Shows the best route for a specific IP |
| `ip route [net] [mask] [next-hop]` | Global Config | Adds a static route |
| `no ip route [net] [mask] [next-hop]` | Global Config | Removes a static route |
| `show ip route static` | Privileged EXEC | Shows only static routes |
| `show ip route connected` | Privileged EXEC | Shows only connected routes |

---

## 🔧 Lab Configuration — Static Routing

```
Topology:
PC1 (192.168.1.10) — SW1 — R1 — R2 — SW2 — PC2 (192.168.3.10)
                    .1  10.0.0.1/30  10.0.0.2/30  .1

! ---- R1 Configuration ----
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit

! Static route to reach PC2's network via R2
R1(config)# ip route 192.168.3.0 255.255.255.0 10.0.0.2

! Default route (optional — for internet traffic)
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2

! ---- R2 Configuration ----
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip address 10.0.0.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface gigabitEthernet 0/1
R2(config-if)# ip address 192.168.3.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

! Static route back to PC1's network
R2(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1

! Verify
R1# show ip route
R2# show ip route
R1# ping 192.168.3.10 source 192.168.1.1
```

---

## 🔧 Lab Summary
- Built a 2-router topology with 2 separate LANs in Packet Tracer
- Verified connected and local routes appear automatically after IP assignment
- Configured static routes on R1 to reach R2's LAN and vice versa
- Configured a default route using `0.0.0.0 0.0.0.0`
- Used `show ip route` to verify all routes in the routing table
- Tested connectivity with `ping` from PC1 to PC2 — verified end-to-end reachability
- Observed longest prefix match by adding two overlapping static routes

---

## 💡 Important Notes
- Static routing does **not** scale well in large networks — dynamic routing is used instead
- Routing is **bidirectional** — you must configure routes in BOTH directions (R1 → R2 and R2 → R1)
- Connected routes are added **automatically** — no manual config needed
- The default route `0.0.0.0/0` is the "last resort" — used only if nothing else matches
- `/32` local routes are for the router's own interface IPs — they are NOT used to forward traffic to other hosts

---

## ❓ Things I Was Confused About
- Why do we need routes in BOTH directions? (A packet needs a path there AND a path back — without the return route, replies are dropped)
- Difference between Connected and Local routes? (Connected = the network the interface is on; Local = the exact IP of the interface itself as /32)
- When to use next-hop IP vs exit interface in static route? (Use next-hop IP for multi-access networks like Ethernet; exit interface alone works for point-to-point but can cause issues — always use next-hop IP when possible)
- What happens if no route matches? (Packet is dropped — router sends ICMP Destination Unreachable back to source)
