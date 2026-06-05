# Day 03 - OSI Model & TCP/IP Suite

## 📌 Topics Covered
- What is a Networking Model and why it exists
- OSI Model — 7 Layers
- TCP/IP Suite — 4 Layers
- PDUs (Protocol Data Units) at each layer
- Encapsulation and De-encapsulation
- Same-layer interaction vs Adjacent-layer interaction

## 🖥️ Key Concepts

### OSI Model — 7 Layers (Top to Bottom)
| Layer | Number | Name | Function | PDU |
|---|---|---|---|---|
| Application | 7 | Application | Interface for user applications (HTTP, FTP, DNS) | Data |
| Presentation | 6 | Presentation | Data formatting, encryption, compression | Data |
| Session | 5 | Session | Managing sessions/connections between hosts | Data |
| Transport | 4 | Transport | Reliable/unreliable delivery, port numbers (TCP/UDP) | Segment |
| Network | 3 | Network | Logical addressing (IP), path selection (routing) | Packet |
| Data Link | 2 | Data Link | Physical addressing (MAC), framing, error detection | Frame |
| Physical | 1 | Physical | Transmitting raw bits over a medium (cables, signals) | Bit |

### Memory Trick for OSI Layers
- **Top to Bottom**: **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing
- **Bottom to Top**: **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

### TCP/IP Model — 4 Layers
| TCP/IP Layer | Equivalent OSI Layers |
|---|---|
| Application | OSI Layers 5, 6, 7 |
| Transport | OSI Layer 4 |
| Internet | OSI Layer 3 |
| Network Access / Link | OSI Layers 1, 2 |

### PDUs at Each Layer
| Layer | PDU Name |
|---|---|
| Layer 4 (Transport) | **Segment** |
| Layer 3 (Network) | **Packet** |
| Layer 2 (Data Link) | **Frame** |
| Layer 1 (Physical) | **Bit** |

### Encapsulation (Sending Data)
```
Application Data
  → Transport adds: [TCP/UDP Header | DATA]          = SEGMENT
    → Network adds: [IP Header | SEGMENT]             = PACKET
      → Data Link adds: [ETH Header | PACKET | ETH Trailer] = FRAME
        → Physical converts FRAME to bits → sends over wire
```

### De-encapsulation (Receiving Data)
- Exact reverse of above — headers are stripped at each layer as data moves up

### Same-Layer vs Adjacent-Layer Interaction
- **Same-layer**: Communication between the same layer on two different devices (e.g., Layer 4 on PC talks to Layer 4 on server)
- **Adjacent-layer**: Communication between neighboring layers on the SAME device (e.g., Layer 3 passes packet to Layer 2)

## ⌨️ Commands Learned
> No Cisco CLI commands specific to this lesson — conceptual day.

## 🔧 Lab Summary
- Traced how data is encapsulated layer by layer in Packet Tracer simulation mode
- Observed PDU information at each layer using the simulation panel
- Clicked through packet flow to see how frames are built and stripped

## 💡 Important Notes
- OSI model is a **theoretical/conceptual** framework — real world uses TCP/IP
- **Routers** operate at Layer 3 (read IP headers)
- **Switches** operate at Layer 2 (read MAC addresses)
- **Hubs** operate at Layer 1 (just repeat signals — dumb device)
- The TCP/IP model is what is actually used in real networks today
- Layers 5, 6, 7 of OSI are combined into "Application" layer in TCP/IP

## ❓ Things I Was Confused About
- Difference between OSI and TCP/IP models (OSI = theoretical reference, TCP/IP = practical implementation)
- Why do we still learn OSI if TCP/IP is the real one? (OSI is used for troubleshooting and understanding — "Layer 3 problem" is easier to say than listing protocols)
- What exactly Presentation and Session layers do (in practice these are handled by Application layer protocols)
