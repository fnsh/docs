---
icon: lucide/ship-wheel
---

# Addressing

## IP-Addresses

In terms of IP-Addressing, it has to be distinguished between RFC1918 private addresses and publicly routed
addresses.

We are assigned publicly routed IP-Addresses by RIPE NCC.

### IPv4 / IPv6 Address Schema

We usually assign a IPv6 network prefix length of /112 to networks that are used as non-client networks (e.g. Server Frontend networks / Management networks.)

We usually assign at max a /24 to a net. Thus the last double octet of the IPv6 address is filled with the last octet of the IPv4 address. This allows us to easily identify the IPv6 address that corresponds to a given IPv4 address.

Example:

```
2001:db8:abcd:1234::/112
194.222.21.0/24

Host 222:
2001:db8:abcd:1234::222
194.222.21.222
```

### Allocations

We are assigned publicly routed IP-Addresses by RIPE NCC.

 - [194.180.249.0/24](https://apps.db.ripe.net/db-web-ui/lookup?source=ripe&key=194.180.249.0%20-%20194.180.249.255&type=inetnum)
 - [2a13:fcc0::/29](https://apps.db.ripe.net/db-web-ui/lookup?source=ripe&key=2a13:fcc0::%2F29&type=inet6num)

They are assigned as follows:

| Subnet | Location |
| ----- | ------- |
| 194.180.249.0/24 | Frankfurt |
| 2a13:fcc0::/32 | Frankfurt |

## VLANs

Currently we use VLAN IDs <256 for our internal purposes. They are assigned by the following scheme:

| VLAN Start | VLAN End | Purpose |
| ---------- | -------- | ------- |
| 0 | 99 | Transfer Nets for BGP Peers |
| 100 | 199 | Internal Networks (Mesh, Frontend) |
| 200 | 250 | Management Networks (IPMI, Hypervisor) |
| 300 | 399 | Hosted Communities (Transfer Nets) |
| 400 | 499 | VLANs for Hosted Communities |

The individual VLAN assignments are documented in the respective location documentation.

## MAC Addresses

MAC Addresses are usually based upon the VLAN ID the NIC is supposed to be assigned in.

The following Location IDs are used:

| Location | Location ID |
| -------- | ----------- |
| Frankfurt | 01 |

### Gateways

```text
"da:ff:<HVID>:2<GWID>:<LID>:01"

HVID = VLAN ID of the NIC (Hexadecimal prefixed 0)
GWID = Gateway ID (Hexadecimal)
LID = Location ID (Hexadecimal)
```

### Router

```text
"da:ff:<HVID>:1<RID>:<LID>:01"

HVID = VLAN ID of the NIC (Hexadecimal prefixed 0)
RID = Router ID (Hexadecimal)
LID = Location ID (Hexadecimal)
```

### Transfer Networks

TODO - WTF did we think there?
