# Multi-Campus Network Infrastructure

A multi-campus enterprise network infrastructure designed and simulated using Cisco Packet Tracer.

---

## 📌 Project Overview
This project models a 5-campus university network topology where each campus operates as an independent LAN connected via WAN serial links. All centralized network services (DHCP, DNS, and Web hosting) are hosted in Campus 5 to minimize hop count across the dual-triangular topology.

### Key Technical Features
* **IP Addressing Scheme:** Class C private addressing (`192.168.0.0/16`) with `/24` LAN subnets and `/30` point-to-point WAN serial links.
* **Static Routing:** Manual static routing tables configured across all 5 Cisco routers to achieve full mesh connectivity.
* **Centralized DHCP Relay:** Distributed DHCP IP pools hosted on Campus 5, served to Campuses 1–4 using `ip helper-address` relay configurations.
* **DNS Service:** Domain Name System resolution mapping `www.university.com` to the central Web Server (`192.168.50.2`).
* **Web Hosting:** HTTP web server hosting the university homepage, accessible across all campuses via IP and domain name.

---

## 📐 Network Architecture & Topology

The topology consists of 5 routers interconnected in two triangular link groups sharing Router 5 as the central hub:
* **Router1 ↔ Router2 ↔ Router5** (Triangle 1)
* **Router3 ↔ Router4 ↔ Router5** (Triangle 2)

```text
               [ Campus 1 ]             [ Campus 3 ]
                    |                        |
                (Router1)----------------(Router3)
                 /     \                  /     \
                /       \                /       \
        (Router2)------(Router5)-------(Router4)
           |               |               |
      [ Campus 2 ]    [ Campus 5 ]    [ Campus 4 ]
                   (DHCP, DNS, Web)
```

### Device Summary
| Campus | Router | Switch | End Devices / Servers |
| :--- | :--- | :--- | :--- |
| **Campus 1** | Router1 | Switch1 | 2 Client PCs |
| **Campus 2** | Router2 | Switch0 | 2 Client PCs |
| **Campus 3** | Router3 | Switch3 | 2 Client PCs |
| **Campus 4** | Router4 | Switch2 | 2 Client PCs |
| **Campus 5** | Router5 | Switch4 | DHCP Server, DNS Server, Web Server |

---

## 📊 IP Addressing Scheme

### Campus LAN Subnets
| Location | Network Address | Subnet Mask | Default Gateway | Usable Host Range |
| :--- | :--- | :--- | :--- | :--- |
| **Campus 1** | `192.168.10.0/24` | `255.255.255.0` | `192.168.10.1` | `192.168.10.2 - 192.168.10.254` |
| **Campus 2** | `192.168.20.0/24` | `255.255.255.0` | `192.168.20.1` | `192.168.20.2 - 192.168.20.254` |
| **Campus 3** | `192.168.30.0/24` | `255.255.255.0` | `192.168.30.1` | `192.168.30.2 - 192.168.30.254` |
| **Campus 4** | `192.168.40.0/24` | `255.255.255.0` | `192.168.40.1` | `192.168.40.2 - 192.168.40.254` |
| **Campus 5** | `192.168.50.0/24` | `255.255.255.0` | `192.168.50.1` | `192.168.50.2 - 192.168.50.254` |

### WAN Serial Links (`/30` Subnets)
| Serial Link | Network | Subnet Mask | Interface A | Interface B |
| :--- | :--- | :--- | :--- | :--- |
| **R1 ↔ R5** | `192.168.100.0/30` | `255.255.255.252` | R1 Se3/0 (`.1`) | R5 Se6/0 (`.2`) |
| **R2 ↔ R5** | `192.168.100.4/30` | `255.255.255.252` | R2 Se2/0 (`.5`) | R5 Se2/0 (`.6`) |
| **R3 ↔ R5** | `192.168.100.8/30` | `255.255.255.252` | R3 Se2/0 (`.9`) | R5 Se3/0 (`.10`) |
| **R4 ↔ R5** | `192.168.100.12/30` | `255.255.255.252` | R4 Se3/0 (`.13`) | R5 Se7/0 (`.14`) |
| **R1 ↔ R2** | `192.168.100.16/30` | `255.255.255.252` | R1 Se2/0 (`.17`) | R2 Se3/0 (`.18`) |
| **R3 ↔ R4** | `192.168.100.20/30` | `255.255.255.252` | R3 Se3/0 (`.21`) | R4 Se2/0 (`.22`) |

---

## ⚙️ Router Configuration Commands

### Centralized Server IPs (Campus 5)
* **Web Server:** `192.168.50.2`
* **DNS Server:** `192.168.50.3`
* **DHCP Server:** `192.168.50.4`

### Cisco IOS Configuration Snippets

#### 1. Router Gateway & DHCP Relay Setup (e.g., Router 1)
```cisconetwork
Router1# configure terminal
Router1(config)# interface FastEthernet0/0
Router1(config-if)# ip address 192.168.10.1 255.255.255.0
Router1(config-if)# ip helper-address 192.168.50.4
Router1(config-if)# no shutdown
Router1(config-if)# exit
```

#### 2. WAN Serial Interface Configuration
```cisconetwork
Router1(config)# interface Serial2/0
Router1(config-if)# ip address 192.168.100.17 255.255.255.252
Router1(config-if)# clock rate 64000
Router1(config-if)# no shutdown
Router1(config-if)# exit

Router1(config)# interface Serial3/0
Router1(config-if)# ip address 192.168.100.1 255.255.255.252
Router1(config-if)# clock rate 64000
Router1(config-if)# no shutdown
Router1(config-if)# exit
```

#### 3. Static Route Configuration
```cisconetwork
Router1(config)# ip route 192.168.20.0 255.255.255.0 192.168.100.18
Router1(config)# ip route 192.168.50.0 255.255.255.0 192.168.100.2
Router1(config)# ip route 192.168.30.0 255.255.255.0 192.168.100.2
Router1(config)# ip route 192.168.40.0 255.255.255.0 192.168.100.2
```

---

## 🧪 Verification & Testing

The network configuration was validated through the following tests:
1. **Dynamic Addressing:** Client PCs on all campuses successfully acquired IP addresses, subnet masks, gateways, and DNS configuration via DHCP.
2. **End-to-End Connectivity:** ICMP ping verified full reachability between independent PCs across different campuses.
3. **DNS Resolution:** `nslookup www.university.com` resolved correctly to `192.168.50.2` from client PCs.
4. **HTTP Web Access:** Embedded web browsers on workstations successfully loaded the university homepage using both direct IP (`http://192.168.50.2`) and domain name (`http://www.university.com`).

---

## 📂 Included Repository Files

* `ABC_University_Campus_Network.pkt` — Complete Cisco Packet Tracer topology and device configurations.
* `CSE350_mini project.pdf` — Comprehensive project report with addressing tables, configuration commands, and verification test logs.
* `README.md` — Project documentation and setup instructions.

---

## 🚀 How to Run in Packet Tracer

1. Clone this repository:
   ```bash
   git clone [https://github.com/subhanishat89-spec/Multi-campus-network-infrastructure.git](https://github.com/subhanishat89-spec/Multi-campus-network-infrastructure.git)
   ```
2. Open **Cisco Packet Tracer** (v8.0 or higher recommended).
3. Open `ABC_University_Campus_Network.pkt`.
4. Select any PC in Campus 1–4, navigate to **Desktop > Web Browser**, and visit `http://www.university.com` to test DNS and HTTP services.

---

## 📝 Author 
* **Name:** Nishat Subha Mithela 
