---
icon: lucide/infinity
---

# Fastd Server Side Ratelimit

The Fastd erver Side Ratelimit is a mechanism to apply traffic shaping on the VPN Link.

We previously used the OpenWrt provided `sqm-scripts` package to apply ingress and egress
shaping on the VPN link. This shaping however was performed in both directions on the node
itself.

This comes with an increased load on the local node. While this generally works fine for modern
hardware even in the range on hundrets of megabits, it does not scale for older, weaker nodes.

Fastd Server Side Ratelimit (as its name implies) distributes the shaping between local node
and the connected gateway as VPN concentrator. Traffic is always shaped on the egress side of the link.

From the perspective of a user, the downstream is shaped on the gateway while the upstream is shaped
on the local node

## Rationale

We see multiple benefits in shaping traffic in downstream direction on the gateway.

 - Reduces the risk of the fastd link becoming a DoS vectory for the uplink connection by applying
   a limit before traffic traverses the upstream connection
 - Reduction in CPU load on the local node. For weaker models, traffic itself is not the limiting factor but
   CPU usage. Thus there is a total limit of traffic that can be processed in sum.

Shaping the upstream itself (regardless of the application point) is improving fairness between different
users sharing the same uplink connection by employing fair queueing technology.
