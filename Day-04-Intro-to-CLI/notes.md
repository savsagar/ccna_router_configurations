# Day 04 - Intro to the CLI

## 📌 Topics Covered
- How to connect to a Cisco device (Console connection)
- CLI Modes: User EXEC, Privileged EXEC, Global Configuration
- Basic navigation between modes
- Configuring hostnames and passwords
- Saving configurations
- Basic show commands

## 🖥️ Key Concepts

### How to Connect to a Cisco Device
- Use a **Console Cable** (RJ-45 to DB9 / USB) connected to the Console port
- Use terminal software: PuTTY, Tera Term, or Packet Tracer terminal
- Default console settings: **9600 baud, 8 data bits, no parity, 1 stop bit**

### CLI Modes
| Mode | Prompt | How to Enter |
|---|---|---|
| User EXEC | `Router>` | Default on login |
| Privileged EXEC | `Router#` | Type `enable` from User EXEC |
| Global Config | `Router(config)#` | Type `configure terminal` from Privileged EXEC |
| Interface Config | `Router(config-if)#` | Type `interface [int-id]` from Global Config |
| Line Config | `Router(config-line)#` | Type `line [type] [number]` from Global Config |

### Moving Between Modes
```
Router>              → enable →           Router#
Router#              → configure terminal → Router(config)#
Router(config)#      → exit / Ctrl+Z →    Router#
Router#              → disable →           Router>
```

## ⌨️ Commands Learned

### Navigation Commands
| Command | Mode | What it does |
|---|---|---|
| `enable` | User EXEC | Enter Privileged EXEC mode |
| `disable` | Privileged EXEC | Return to User EXEC |
| `configure terminal` (or `conf t`) | Privileged EXEC | Enter Global Config mode |
| `exit` | Any config mode | Go back one level |
| `end` or `Ctrl+Z` | Any config mode | Jump back to Privileged EXEC |

### Configuration Commands
| Command | Mode | What it does |
|---|---|---|
| `hostname [name]` | Global Config | Sets the device hostname |
| `enable password [pass]` | Global Config | Sets unencrypted enable password |
| `enable secret [pass]` | Global Config | Sets encrypted enable password (preferred!) |
| `service password-encryption` | Global Config | Encrypts all plain-text passwords |
| `no [command]` | Any config | Removes/undoes a configuration |

### Console & VTY Password
| Command | Mode | What it does |
|---|---|---|
| `line console 0` | Global Config | Enters console line config |
| `line vty 0 15` | Global Config | Enters VTY (Telnet/SSH) lines config |
| `password [pass]` | Line Config | Sets the line password |
| `login` | Line Config | Requires password on login |

### Saving Configuration
| Command | Mode | What it does |
|---|---|---|
| `show running-config` | Privileged EXEC | Shows current active config (RAM) |
| `show startup-config` | Privileged EXEC | Shows saved config (NVRAM) |
| `write memory` (or `wr`) | Privileged EXEC | Saves running-config to startup-config |
| `copy running-config startup-config` | Privileged EXEC | Same as write memory |

### Helpful CLI Tricks
| Shortcut | What it does |
|---|---|
| `Tab` | Auto-completes a command |
| `?` | Shows available commands or options |
| `Ctrl+C` | Aborts current command |
| `Ctrl+Z` | Returns to Privileged EXEC from any config mode |
| Up Arrow | Recalls previous command |

## 🔧 Lab Configuration — Basic Router Setup

```
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# enable secret ccna
R1(config)# service password-encryption
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 15
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# end
R1# write memory
```

## 🔧 Lab Summary
- Connected PC to Router console port in Packet Tracer
- Navigated through all CLI modes
- Configured hostname, enable secret, console and VTY passwords
- Used `show running-config` to verify configuration
- Saved config using `write memory`

## 💡 Important Notes
- **enable secret** ALWAYS beats **enable password** — if both are set, secret is used
- `service password-encryption` uses weak **Type 7** encryption — not very secure
- `enable secret` uses **MD5 hash (Type 5)** — much stronger
- **Running-config** = stored in RAM — lost on reboot
- **Startup-config** = stored in NVRAM — survives reboot
- Always save your config with `wr` before powering off!

## ❓ Things I Was Confused About
- Difference between `enable password` and `enable secret` (always use secret — it's encrypted)
- Why `service password-encryption` is not enough alone (Type 7 is easily cracked — use secret instead)
- Difference between running-config and startup-config (RAM vs NVRAM — must save manually)
