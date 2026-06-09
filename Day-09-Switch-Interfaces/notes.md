# Day 09 - Switch Interfaces

## 📌 Topics Covered
- Interface speed and duplex settings
- Auto-negotiation
- Interface errors and what they mean
- Speed/duplex mismatch problems
- Configuring interface ranges
- show interfaces status output
- Full-duplex vs Half-duplex

---

## 🖥️ Key Concepts

### Full-Duplex vs Half-Duplex

| Type | Description | Used With |
|---|---|---|
| **Half-Duplex** | Can send OR receive — not both at same time | Old hubs |
| **Full-Duplex** | Can send AND receive simultaneously | Modern switches |

> 💡 All modern switches use **Full-Duplex** — half-duplex is only seen with old hubs or legacy devices

---

### Speed and Duplex Settings

| Setting | Meaning |
|---|---|
| `auto` | Device negotiates best speed/duplex with the other end |
| `10` | Force 10 Mbps |
| `100` | Force 100 Mbps |
| `1000` | Force 1000 Mbps (Gigabit) |
| `full` | Force full-duplex |
| `half` | Force half-duplex |

---

### Auto-Negotiation
- When **both sides are set to auto** → they negotiate the best common speed and duplex
- When **one side is forced, other is auto** → the auto side cannot fully negotiate:
  - Speed: auto side detects and matches the speed
  - Duplex: auto side defaults to **half-duplex** if speed is 10 or 100 Mbps → **mismatch!**

### Speed/Duplex Mismatch — What Happens
- One side = full-duplex, other side = half-duplex
- Performance is severely degraded
- High collision/error counts on the interface
- Symptoms: very slow transfer speeds, high CRC errors, late collisions

**Best Practice:** Either set both sides to `auto` OR force both sides to the same speed and duplex

---

### show interfaces status — Output Explained

```
SW1# show interfaces status

Port      Name       Status       Vlan  Duplex  Speed  Type
Fa0/1                connected    1     a-full  a-100  10/100BaseTX
Fa0/2                notconnect   1     auto    auto   10/100BaseTX
Fa0/3                connected    1     a-full  a-100  10/100BaseTX
Gi0/1                connected    trunk a-full  a-1000 10/100/1000BaseTX
```

| Column | Meaning |
|---|---|
| Port | Interface name |
| Name | Description if set |
| Status | connected / notconnect / disabled |
| Vlan | VLAN assigned (trunk = trunk port) |
| Duplex | a-full = auto-negotiated full; full = forced |
| Speed | a-100 = auto-negotiated 100M; 100 = forced |
| Type | Physical media type |

---

### Interface Errors — show interfaces

```
SW1# show interfaces fastEthernet 0/1

  Input errors: 0, CRC: 0, frame: 0, overrun: 0, ignored: 0
  Output errors: 0, collisions: 0, interface resets: 0
  Runts: 0, Giants: 0
```

| Error | Meaning |
|---|---|
| **Runts** | Frames smaller than 64 bytes — caused by collisions or faulty NIC |
| **Giants** | Frames larger than 1518 bytes — caused by faulty NIC |
| **CRC errors** | Frame failed checksum — caused by cable problems or duplex mismatch |
| **Frame errors** | Received frame has incorrect format |
| **Input errors** | Total count of all input-side errors |
| **Collisions** | Frames that collided — should be 0 in full-duplex |
| **Late collisions** | Collision after first 64 bytes — sign of duplex mismatch |
| **Interface resets** | Interface was reset — can indicate connectivity problems |

---

### Interface Range Configuration
- Configure multiple interfaces at once using `interface range`
- Saves time when configuring many ports the same way

---

## ⌨️ Commands Learned

| Command | Mode | What it does |
|---|---|---|
| `show interfaces status` | Privileged EXEC | Summary of all interfaces — status, VLAN, speed, duplex |
| `show interfaces [int]` | Privileged EXEC | Detailed interface stats including error counters |
| `speed [10/100/1000/auto]` | Interface Config | Sets interface speed |
| `duplex [auto/full/half]` | Interface Config | Sets interface duplex |
| `interface range [int range]` | Global Config | Configures multiple interfaces simultaneously |
| `description [text]` | Interface Config | Adds a label to the interface |
| `shutdown` | Interface Config | Disables the interface |
| `no shutdown` | Interface Config | Enables the interface |

---

## 🔧 Lab Configuration

```
! View all interface statuses
SW1# show interfaces status
SW1# show interfaces fastEthernet 0/1

! Force speed and duplex on a single interface
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# speed 100
SW1(config-if)# duplex full
SW1(config-if)# description ## PC1 ##
SW1(config-if)# exit

! Configure a range of interfaces at once
SW1(config)# interface range fastEthernet 0/1 - 24
SW1(config-if-range)# duplex full
SW1(config-if-range)# speed 100
SW1(config-if-range)# description ## Access Port ##
SW1(config-if-range)# exit

! Shut down unused ports (security best practice)
SW1(config)# interface range fastEthernet 0/10 - 24
SW1(config-if-range)# shutdown
SW1(config-if-range)# exit

! Set uplink to auto (gigabit to router/other switch)
SW1(config)# interface gigabitEthernet 0/1
SW1(config-if)# speed auto
SW1(config-if)# duplex auto
SW1(config-if)# description ## Uplink to R1 ##
SW1(config-if)# no shutdown
SW1(config-if)# end

SW1# show interfaces status
SW1# show interfaces fastEthernet 0/1
```

---

## 🖧 Network Topology

![Day 09 Topology](topology.png)

---

## 🔧 Lab Summary
- Viewed all interface statuses using `show interfaces status`
- Observed auto-negotiated speed and duplex (a-full, a-100)
- Forced speed and duplex on specific ports and observed the change
- Configured an interface range to set all access ports at once
- Shut down unused ports as a security measure
- Read error counters using `show interfaces` and identified what each means

---

## 💡 Important Notes
- Cisco **router** interfaces are **shutdown by default** — need `no shutdown`
- Cisco **switch** interfaces are **NOT shutdown by default** — they come up automatically
- `a-full` and `a-100` in status output = **auto-negotiated** values
- `full` and `100` without the "a-" = **manually forced** values
- Always shut down unused switch ports — open ports are a security risk
- `interface range fa0/1 - 10` and `interface range fa0/1, fa0/3, fa0/5` are both valid range formats

---

## ❓ Things I Was Confused About
- Why do switches come up without `no shutdown` but routers don't? (Switches are plug-and-play devices; routers need deliberate configuration before activating)
- What causes CRC errors? (Bad cable, damaged connector, or duplex mismatch — check cable first, then check duplex settings)
- When should I force speed/duplex vs use auto? (Use auto for most cases; force both ends to same value only if you see negotiation problems)
