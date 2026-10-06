# Final-Project----CN
# Canvasback Terrarium & Houseplant Subscription Co. – Network Design

Case Study 156 · Computer Networking & Cyber Security · B.Tech CSE (2025-29), School of Future Tech

A complete network design for a new greenhouse and fulfillment center with a remote propagation annex. The network was planned on paper first (topology, VLSM addressing, protocol mapping) and then built and tested in Cisco Packet Tracer.

## The problem

The company is networking its main building (four working areas) plus a remote annex connected by a leased line. Nothing exists yet, so the network had to be designed before any equipment is bought.

| Segment | Hosts needed |
|---|---|
| Greenhouse Growing Floor | 36 |
| Packing & Fulfillment Area | 16 |
| Administration | 9 |
| IT | 6 |
| Propagation Annex WAN link | 2 |

## Design

**Topology:** extended star. Every area connects to one central switch, which connects to the router. The annex connects to the router over a separate point-to-point link. This keeps cabling cheap, limits a failure to one device, and lets new devices or access switches be added easily.

**Addressing (VLSM on 10.185.0.0/22, with 20% spare capacity on every LAN):**

| Segment | VLAN | Network | Mask | Usable range | Broadcast | Gateway |
|---|---|---|---|---|---|---|
| Greenhouse | 10 | 10.185.0.0/26 | 255.255.255.192 | .1 – .62 | 10.185.0.63 | 10.185.0.1 |
| Packing | 20 | 10.185.0.64/27 | 255.255.255.224 | .65 – .94 | 10.185.0.95 | 10.185.0.65 |
| Admin | 30 | 10.185.0.96/28 | 255.255.255.240 | .97 – .110 | 10.185.0.111 | 10.185.0.97 |
| IT | 40 | 10.185.0.112/28 | 255.255.255.240 | .113 – .126 | 10.185.0.127 | 10.185.0.113 |
| WAN link | – | 10.185.0.128/30 | 255.255.255.252 | .129 – .130 | 10.185.0.131 | – |

The whole design uses private addresses (10.0.0.0/8, RFC 1918). The WAN link can use them too because it is a company-owned leased line that never crosses the public internet.

## Technologies used

| Technology | Role in this project |
|---|---|
| VLSM | Divides 10.185.0.0/22 into five right-sized subnets |
| VLANs (802.1Q) and trunking | Separate the four departments on one switch |
| Router-on-a-stick | One router port with four sub-interfaces acting as gateways |
| DHCP | The router hands out addresses, gateway and DNS server |
| DNS | The IT server resolves `monitor.canvasback.local` to 10.185.0.114 |
| ARP and ICMP | Gateway MAC discovery and connectivity testing |
| Static default route | R-Annex sends unknown traffic to R-Main |

## Devices

| Device | Model | Role |
|---|---|---|
| R-Main | Cisco 2911 | Gateways for all VLANs, DHCP server, WAN link |
| R-Annex | Cisco 2911 | Remote annex end of the leased line |
| SW-Core | Cisco 2960-24TT | Connects all devices and carries the VLANs |
| IT-Server | Server | DNS server, static 10.185.0.114 |
| GH-PC1–3, PK-PC1–2, AD-LT1–2, IT-PC1 | PCs and laptops | Sample hosts for each segment |

## How to open the project

1. Install Cisco Packet Tracer (built with version 8.2.2).
2. Open `packet-tracer/casestudy156.pkt`.
3. Click any PC, open **Desktop**, then **IP Configuration** to see its DHCP address. The exact host addresses can differ after reopening the file, but each device always stays inside its own subnet.
