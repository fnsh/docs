---
icon: lucide/server
---

# Infrastructure Frankfurt

We colocate in Frankfurt at Digital Realty.

## Access

### Physical

!!! note

    Access is possible 24/7 but required prior coordination with Configo.


The following persons are registered to access the Rack at Digital Realty.

 - noxnox
 - liv

### Management Access

We utilize a Teltonika RUT955 Router for remote access to the infrastructure. It is connected to the switch and provides a backup connection to the Internet via LTE / 5G RedCap. In addition we are provided with a Port from Configo for remote access to the infrastructure.

| IP Address | Default Gateway | Notes |
| ---------- | --------------- | ----- |
| 217.71.221.89 | 217.71.221.1 | |
| 2a00:1fe8:2::89/64 | 2a00:1fe8:2::1 | |

The following Persons are granted access to the infrastructure via the RUT955:

 - blocktrron
 - liv

The RUT955 is connected to the switch RS232 port to provide access to the console in the ~~unlikely~~ event of expedited configuration changes or troubleshooting.

## Network

The following VLANs are currently in use in Frankfurt:

| VLAN ID | Purpose | IPv4 | IPv6 |Notes |
| ------- | ------- | ----- | ----- |----- |
| 21 | IC Configo | | | Transports QinQ |
| 21 - 21 | Upstream Configo | 217.71.220.138/31 | 2a00:1fe8:1::38/127 | |
| 21 - 426 | Upstream Inter.Link | 81.27.69.84/31 | 2a11:4140:9002::30/127 | |
| 31 | Upstream CIX | | | |
| 110 | Frontend Network for Machines | 194.180.249.0/27 | 2a13:fcc0:ebbe:1:401:/80 | |
| 130 | Mesh Transport | | LL | |
| 200 | IPMI Management | 172.20.200.0/24 | | |
| 210 | Hypervisor / VM Management | 172.20.210.0/24 | | |
| 300 | Neanderfunk Uplink | 172.20.210.0/24 | | |
| 301 | Neanderfunk Cross-GW | | | |

### IP Addresses

We are assigned the following IP address blocks:

 - 194.180.249.0/24
 - 2a13:fcc0::/29

These are partitioned in the following Blocks

| Subnet | Purpose | Notes |
| ----- | ------- | ----- |
| 194.180.249.0/27 | Frontend Addresses for Gateways | |
| 194.180.249.32/30 | Anycast IPs for services | |
| 194.180.249.40/29 | Block for Neanderfunk |  |
| 2a13:fcc0:ebbe:1:401:/80 | Frontend Addresses for Gateways | |
| 2a13:fcc0:2ed8::/48 | Block for Client Assignments | |
| 2a13:fcc0:2ed9:ffff::/64 | Anycast IPs for services | |
| 2a13:fcc0:2f00::/48 | Client Nets Neanderfunk | |
| 2a13:fcc0:3000::/48 | Transfer-Net Allocs for third parties | |
| 2a13:fcc0:3000:300::/64 | Transfer-Net Neanderfunk | |


#### Assignments

##### VLAN 110 - Frontend Network for Machines

| IP-Address | Purpose | Notes |
| ---------- | ------- | ----- |
| 194.180.249.1 | Gateway IP | |
| 194.180.249.3 | gw01 | |
| 194.180.249.4 | gw01 NAT | |
| 194.180.249.5 | gw02 | |
| 194.180.249.6 | gw02 NAT | |
| 194.180.249.7 | gw03 | |
| 194.180.249.8 | gw03 NAT | |
| 194.180.249.9 | gw04 | |
| 194.180.249.10 | gw04 NAT | |
| 194.180.249.11 | gw05 | |
| 194.180.249.12 | gw05 NAT | |
| 194.180.249.13 | gw06 | |
| 194.180.249.14 | gw06 NAT | |
| 194.180.249.15 | gw07 | |
| 194.180.249.16 | gw07 NAT | |
| 194.180.249.17 | gw08 | |
| 194.180.249.18 | gw08 NAT | |
| 194.180.249.19 | | |
| 194.180.249.20 | | |
| 194.180.249.21 | | |
| 194.180.249.22 | | |
| 194.180.249.23 | | |
| 194.180.249.24 | | |
| 194.180.249.25 | | |
| 194.180.249.26 | | |
| 194.180.249.27 | | |
| 194.180.249.28 | | |
| 194.180.249.29 | | |
| 194.180.249.30 | | |


##### VLAN 200 - IPMI Management

##### VLAN 210 - Hypervisor / VM Management


### Mac Addresses

<!-- ToDo: Validate Schema -->

| MAC Address | Purpose | Notes |
| ----------- | ------- | ----- |
| da:ff:<vlan hex>:2<gw id>:00:01 | Gateway MAC Address | <vlan hex> is the VLAN ID in hexadecimal, <gw id> is the Gateway ID (1-4) |
|  da:ff:<vlan hex>:1<router id>:00:01 | Router MAC Address | <vlan hex> is the VLAN ID in hexadecimal, <router id> is the Router ID (1-4) |

### Peers

The following Configuration is active for Peer connections

| Peer | Remote AS | Remote IPv4 | Local IPv4 | Remote IPv6 | Local IPv6 | Description |
| ---- | --------- | ----------- | ---------- | ----------- | ---------- | ----------- |
| Inter.Link | 5405 | 81.27.69.84 | 81.27.69.85 | 2a11:4140:9002::30 | 2a11:4140:9002::31 | Peer with Inter.Link |
| Configo | 34154 | 217.71.220.138 | 217.71.220.139 | 2a00:1fe8:1::38 | 2a00:1fe8:1::39 | Peer with Configo |

## Hardware

We currently operate the following Hardware in Frankfurt

### Servers

- 2x AsRockRack 1U2N2G-AM5/2T
   - AMD Ryzen 7 9700X
   - 32GB DDR5
   - 1TB NVMe
   - Mellanox ConnectX-4

Each of these chassis contain two nodes, totalling up to 4 systems in Frankfurt.

### Networking

#### Switch

We operate a MikroTik CRS317-1G-16S+RM Switch for interconnecting Servers and Upstream.

##### Ports

| Port | Type | Description |
| ---- | ---- | ----------- |
| sfp-sfpplus-1 | SFP+ | Connected to Chassis 1 / Node 1 |
| sfp-sfpplus-2 | SFP+ | Connected to Chassis 1 / Node 2 |
| sfp-sfpplus-3 | SFP+ | Connected to Chassis 2 / Node 1 |
| sfp-sfpplus-4 | SFP+ | Connected to Chassis 2 / Node 2 |
| sfp-sfpplus-5 | SFP RJ45 | Connected to Chassis 1 / Node 1 IPMI |
| sfp-sfpplus-6 | SFP RJ45 | Connected to Chassis 1 / Node 2 IPMI |
| sfp-sfpplus-7 | SFP RJ45 | Connected to Chassis 2 / Node 1 IPMI |
| sfp-sfpplus-8 | SFP RJ45 | Connected to Chassis 2 / Node 2 IPMI |
| sfp-sfpplus-9 |  |  |
| sfp-sfpplus-10 |  |  |
| sfp-sfpplus-11 |  |  |
| sfp-sfpplus-12 |  |  |
| sfp-sfpplus-13 |  |  |
| sfp-sfpplus-14 | SFP+ | Connected to Configo |
| sfp-sfpplus-15 | SFP+ | Prepared for CIX |
| sfp-sfpplus-16 |  |  |
| ether1 | 1G RJ45 | Connected to RUT955 |