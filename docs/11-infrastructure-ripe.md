---
icon: lucide/ethernet-port
---

# RIPE Infrastructure

The Freie Netze Südhessen e.V. is a member of the RIPE NCC. We are registered as a Local Internet Registry (LIR) and have been assigned the ASN62028 as well as the IPv4 and IPv6 address blocks.

Access to the RIPE LIR Account is granted to the following persons:

 - noxnox
 - blocktrron
 - skorpy

## RIPE Atlas Probe

RIPE Atlas is a project of the RIPE NCC to establish a global measurement network. This Network can be used to
start Measurements to asses connectivity problems and provide network operators or interested individuals
with vantage points of diverse kinds such as multiple End User ISP providers or core locations.

A [Ripe Atlas Probe](https://atlas.ripe.net/probes/1016329/overview) is deployed with the Frankfurt
Infrastructure as a VM. The VM is based on OpenWrt with the
[ripe-atlas-probe](https://github.com/openwrt/packages/blob/master/net/atlas-sw-probe/Makefile)
package installed.

The Probe is shared with the LIR organisation.

### Hosting your own RIPE Atlas Probe

In the past, RIPE NCC sent out hardware Probes based on TP-Link routers upon request. This is rarely the case nowadays.

In case you are interested in hosting your own Probe, you can either do this in a VM or setup a OpenWrt based router
with the [ripe-atlas-probe](https://github.com/openwrt/packages/blob/master/net/atlas-sw-probe/Makefile) package
installed.

In case you are interested in hosting your own RIPE Atlas probe, you can take a old legacy Routers from our stack
and install the OpenWrt package to deploy for you at home. By doing so, you earn credits which you can spend for
creating your own measurements.
