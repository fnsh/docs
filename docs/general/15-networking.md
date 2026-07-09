---
icon: lucide/network
---

# Networking

This Article gives some general guidance on how networking is done in general. It aims at being easily understandable
and practically explorable in a regular home network environment.

## Exploration

### Local Network

Start by exploring your own network. You can use the `ip` command to list all network interfaces on your device:

```bash
ip link show
```

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: wwp195s0f3u4: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 76:fc:4c:eb:8b:7e brd ff:ff:ff:ff:ff:ff
3: wlp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1280 qdisc noqueue state UP mode DORMANT group default qlen 1000
    link/ether 04:7b:cb:c8:b3:36 brd ff:ff:ff:ff:ff:ff
    altname wlx047bcbc8b336
```

This command lists all network interfaces on your device, along with their status and MAC addresses.

We can see that the device has three network interfaces: a loopback interface (`lo`),
a wireless interface (`wlp1s0`), and a WWAN (5G) interface (`wwp195s0f3u4`).

It also lists the MAC addresses of each interface, which are unique identifiers assigned to each network interface.

As the most commonly used form of networking today is packet-based networking, the maximum size of each
packet is limited by the Maximum Transmission Unit (MTU) of the network interface. The MTU is the
largest size of a packet that can be transmitted over a network interface without fragmentation.

!!! note

    An alternative form of networking is circuit-based networking, which is used in traditional telephone networks. In circuit-based networking, a dedicated communication path is established between two devices for the duration of the communication session.

Next, we can explore the IP addresses assigned to each network interface using the following command:

```bash
ip addr show
```

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: wwp195s0f3u4: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 76:fc:4c:eb:8b:7e brd ff:ff:ff:ff:ff:ff
3: wlp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1280 qdisc noqueue state UP group default qlen 1000
    link/ether 04:7b:cb:c8:b3:36 brd ff:ff:ff:ff:ff:ff
    altname wlx047bcbc8b336
    inet 10.20.1.36/20 brd 10.20.15.255 scope global dynamic noprefixroute wlp1s0
       valid_lft 450sec preferred_lft 450sec
    inet6 2a13:fcc0:2ed8:1002:c436:fd76:19b9:7422/64 scope global dynamic noprefixroute 
       valid_lft 3592sec preferred_lft 1792sec
    inet6 fd01:67c:2ed8:1002:c2e5:529:eb61:997f/64 scope global dynamic noprefixroute 
       valid_lft 86008sec preferred_lft 14008sec
    inet6 fe80::f2c6:7bf2:6d25:5a32/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

We can see that the wireless interface `wlp1s0` has been assigned an IPv4 address of `10.20.1.36` and multiple IPv6 addresses. 

The IPv4 address is a private address, which is not routable on the public Internet. The IPv6 addresses include a global address, a unique local address (ULA), and a link-local address.

The global address is routable on the public Internet, while the ULA and link-local addresses are not.

!!! note

    The link-local address is automatically assigned to the interface and is used for communication
    between devices on the same link (e.g. the same subnet). It is not routable on the public Internet.

    The ULA is similar to the private IPv4 address and is used for communication within a private network.
    It is not routable on the public Internet but may be routed within a private network.

Regardless of the type of address (IPv4 or IPv6), each address is associated with a subnet mask, which defines the range of addresses that belong to the same subnet.

You can use the `sipcalc` command to calculate the network address, broadcast address, and range of usable host addresses for a given IP address and subnet mask.

```bash
$ sipcalc 10.20.1.36/20

[CIDR]
Host address		- 10.20.1.36
Host address (decimal)	- 169083172
Host address (hex)	- A140124
Network address		- 10.20.0.0
Network mask		- 255.255.240.0
Network mask (bits)	- 20
Network mask (hex)	- FFFFF000
Broadcast address	- 10.20.15.255
Cisco wildcard		- 0.0.15.255
Addresses in network	- 4096
Network range		- 10.20.0.0 - 10.20.15.255
Usable range		- 10.20.0.1 - 10.20.15.254
```

We can see that the subnet offers 4096 addresses, with a usable range of `10.20.0.1 - 10.20.15.254`.

This stems from the fact that the first address in the subnet is reserved as the network
address and the last address is reserved as the broadcast address.

!!! note

    The network address is used to identify the subnet, while the broadcast address is used to send messages to all devices within the subnet.

### Routing

Find out how your device is connected to the Internet by checking the routing table using the following command:

```bash
ip route show
default via 10.20.0.1 dev wlp1s0 proto dhcp src 10.20.1.36 metric 600 
10.20.0.0/20 dev wlp1s0 proto kernel scope link src 10.20.1.36 metric 600 
```

!!! info

    To get a list of IPv6 routes, use the following command:

    ```bash
    ip -6 route show
    ```

The first route is the default route, which is used to send packets to destinations outside of the local network.
The second route is for the local network, which is used to send packets to devices within the same subnet.

!!! note

    Routes for local networks are automatically added by the operating system when an IP address is assigned to a network interface.

The default route consists of the following information:

 - `default`: This indicates that this is the default route.
 - via `10.20.0.1`: This is the IP address of the next hop (usually the router) that packets should be sent to when the destination is not within the local network.
 - dev `wlp1s0`: This is the network interface that should be used to send packets to the next hop.
 - proto `dhcp`: This indicates that the route was added by the DHCP client when the IP address was assigned to the interface.
 - src `10.20.1.36`: This is the source IP address that should be used as the source when sending packets to the next hop.

You can use the `ping` command to test reachability of hosts (Routers, Servers, etc.) on an IP network. The `ping` command sends ICMP Echo Request packets to the target host and waits for ICMP Echo Reply packets in response.

```bash
$ ping -c 4 10.20.0.1
PING 10.20.0.1 (10.20.0.1) 56(84) bytes of data.
64 bytes from 10.20.0.1: icmp_seq=1 ttl=64 time=4.27 ms
64 bytes from 10.20.0.1: icmp_seq=2 ttl=64 time=5.46 ms
64 bytes from 10.20.0.1: icmp_seq=3 ttl=64 time=6.02 ms
64 bytes from 10.20.0.1: icmp_seq=4 ttl=64 time=4.59 ms

--- 10.20.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 4.265/5.084/6.018/0.694 ms
```

!!! tip

    The `-c` option specifies the number of packets to send. In this example, we are sending 4 packets.

You can see the round-trip time (RTT) for each packet, as well as the average RTT for all packets sent.

Typical Round-Trip Times (RTT) for a home network are usually in the range of 1-3 milliseconds,
while RTTs for connections to servers on the Internet can vary widely depending on the distance
and quality of the connection.

!!! note

    The test was performed on a home network with a wireless connection. The RTT may be higher on a wireless connection compared to a wired connection due to potential interference and signal strength issues.

### Internet Connectivity

As a next step, you can also test the connectivity to the Internet by pinging a public DNS server,
such as Google's DNS server at `8.8.8.8`.

```bash
$ ping -c 4 8.8.8.8
```

The output will show the round-trip time for each packet, similar to the previous example with the local network.

As this is a host reachable over the Internet, the round-trip time will be higher than for the local network,
and may vary depending on the distance and quality of the connection.

!!! tip

    Google DNS server in this example is using a technology called Anycast, which allows multiple
    servers to share the same IP address. This means that the server you are pinging may not be the
    same physical server each time, but rather the closest one to your location.

Checking the connectivity to a public DNS server is a good way to verify that your device has access to the Internet.

Being a host on the public internet, a packet has to traverse multiple routers (and possibly other networks) to
reach the destination. Each router along the path will forward the packet to the next hop until it reaches
the destination.

You can use the `mtr` utility to trace the route packets take to reach a destination host. The `mtr` command
combines the functionality of the `traceroute` and `ping` commands, providing a continuous view of the
route and round-trip times for each hop along the path.

!!! note

    The `mtr` command may require superuser privileges to run. You can use `sudo mtr` to run the command with elevated privileges. (This is usually the case when using MacOS)

!!! attention

    The `mtr` command provides a continuous view of the route and round-trip times for each hop along the path to the destination. You can use the `q` key to quit the command and return to the command prompt.


```bash
$ mtr 8.8.8.8

                                       My traceroute  [v0.96]
dbauer-x13g2 (192.168.77.43) -> 8.8.8.8 (8.8.8.8)                           2026-07-09T21:53:19+0200
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                            Packets               Pings
 Host                                                     Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. _gateway                                               0.0%    24    2.8   2.5   2.1   3.2   0.3
 2. 217.71.219.173                                         0.0%    24    2.6   3.8   2.6  25.1   4.5
 3. xe-2-0-2-81.core1.ix.ffm.configo.net                   0.0%    24   20.1   4.4   2.9  20.1   3.5
 4. 217.71.216.70                                          0.0%    24    3.6   4.6   3.1  21.8   3.9
 5. 100.et14.r1-fra1-de.as5405.net                         0.0%    24    3.4   3.5   3.1   4.9   0.5
 6. r5-fra1-de.as5405.net                                  0.0%    23    3.7   3.8   3.2   7.6   0.9
 7. r7-fra1-de.as5405.net                                  0.0%    23    3.4   3.6   3.2   6.6   0.7
 8. r4-fra1-de.as5405.net                                  0.0%    23    3.6   3.5   2.6   4.7   0.4
 9. 142.251.200.164                                        0.0%    23    3.8   4.0   3.5   5.0   0.4
10. 72.14.239.217                                          0.0%    23    3.6   3.6   3.2   4.4   0.3
11. 142.250.46.245                                         0.0%    23    4.7   4.6   3.3   9.4   1.1
12. dns.google                                             0.0%    23    3.8   3.9   3.5   5.1   0.4
```

In the output, you can see the following information for each hop along the path to the destination:

 - Host: The hostname or IP address of the router at that hop.
 - Loss%: The percentage of packets lost at that hop.
 - Snt: The number of packets sent to that hop.
 - Last: The round-trip time for the last packet sent to that hop.
 - Avg: The average round-trip time for all packets sent to that hop.
 - Best: The best (lowest) round-trip time for all packets sent to that hop.
 - Wrst: The worst (highest) round-trip time for all packets sent to that hop.
 - StDev: The standard deviation of the round-trip times for all packets sent to that hop.

!!! note

    As a routers primary function is to forward packets, it may not prioritize responding to ICMP Echo Request packets.
    This can result in higher round-trip times or packet loss for some hops, even if the overall connection to the
    destination is stable.

An important consideration to be taken into account is that it only shows the route taken by packets to reach
the destination, but it does not provide information about the route taken by packets on the return path.
The return path may be different due to routing policies and network conditions.

This applies to each hop along the path to the destination. As each port sends the packet back on the route
it considers best, the return path may be different for each hop along the path.

This is visible in case the latency of hops along the path is significantly different from
the latency of the destination.

Consider the following case:

![Traffic Map](/_media/mtr-map.svg)

```bash
$ mtr 8.8.8.8

                                       My traceroute  [v0.96]
core1.dar.as4242.net  -> core4.ber.as4242.net                               2026-07-09T21:53:19+0200
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                            Packets               Pings
 Host                                                     Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. core1.dar.as4242.net                                   0.0%    24    0.4   0.3   0.1   1.1   0.3
 2. core2.fra.as4242.net                                   0.0%    24    0.8   0.7   0.5   1.2   0.2
 3. core3.dus.as4242.net                                   0.0%    24    9.1   8.9   8.8   9.1   0.1
 4. core6.ham.as4242.net                                   0.0%    24    8.8   8.9   8.7   9.1   0.1
 5. core4.ber.as4242.net                                   0.0%    24   10.1  10.0   9.9  10.1   0.1
```

The return-path is not visible in the output. The latency of hop `core6.ham.as4242.net` is slightly
lower than the latency of hop `core3.dus.as4242.net`, which indicates that the return
path from `core6.ham.as4242.net` to the source is different from the return path from
`core3.dus.as4242.net` to the source.

This is also visible in the path graph, as the return path of `core3.dus.as4242.net` is
traversing `core5.ams.as4242.net`. This is not visible in the traceroute output, as only the
forward path is shown.


!!! tip

    Traffic is often routed through the "cheapest" path, which may not be the most direct path. This can result in higher latency and packet loss for some hops along the path, even if the overall connection to the destination is stable.

You can often also extract information about the Network from such a trace, as the hostnames of routers often contain
information about the location and network of the router. Refer to this
[presentation](https://archive.nanog.org/sites/default/files/10_Roisman_Traceroute.pdf)
for more information about the naming conventions used in hostnames of routers.


## Protocols

With networking we usually refer to Internet Protocol (IP) based networking. This is most commonly used today
for home networks and the Internet.

There are two layer models commonly used in networking.

 - The OSI model and the TCP/IP model.
 - The TCP/IP model is a simplified version of the OSI model and is more commonly used in practice.

Regardless of the model used, IP networking is based on the concept of packets.

### Ethernet

Ethernet is for practical purposes the lowest layer of the TCP/IP model. It is responsible for the physical transmission of data over a network medium.

In the OSI model, Ethernet is part of the Data Link layer (Layer 2).

An Ethernet frame is constructed in the following way:

```
+-------------------+-------------------+-------------------+-------------------+-------------------+
| Destination MAC   | Source MAC        | EtherType         | Payload           |  Checksum          |
| aa:bb:cc:dd:ee:ff | 11:22:33:44:55:66 | 0x0800 (IPv4)     | ...               | 0x12345678         |
+-------------------+-------------------+-------------------+-------------------+-------------------+
```


#### MAC Address

A MAC address is a unique identifier assigned to a network interface controller (NIC) for use as a network address in
communications within a network segment. Each end-user device has a unique MAC address, which is used to identify the
device on the network.

You can find the MAC address of your device using the following command:

```bash
ip link show
```

This command lists all network interfaces on your device, along with their MAC addresses.


```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: wwp195s0f3u4: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 76:fc:4c:a3:88:23 brd ff:ff:ff:ff:ff:ff
3: wlp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1280 qdisc noqueue state UP mode DORMANT group default qlen 1000
    link/ether 04:7b:cb:00:a2:22 brd ff:ff:ff:ff:ff:ff
    altname wlx047bcbc8b336
```

In this example output, the MAC address of the wireless interface `wlp1s0` is `04:7b:cb:00:a2:22`.

There also exist special MAC addresses, which are used for specific purposes.

| MAC Address | Purpose |
| ----------- | ------- |
| ff:ff:ff:ff:ff:ff | Broadcast |
| 01:00:5e:xx:xx:xx | Multicast (IPv4) |
| 33:33:xx:xx:xx:xx | Multicast (IPv6) |

!!! note

	The special MAC addresses shall only be used as the receiver address. The source address shall always be a unique MAC address assigned to the NIC.

### Internet Protocol (IP)

Internet Protocol (IP) is the primary protocol used for communication over the Internet and other
IP-based networks. It operates at the Network layer (Layer 3) of the OSI model.

There exist two versions of IP in use today: IPv4 and IPv6. IPv4 is the most widely used version,
while IPv6 is gradually being adopted to address the limitations of IPv4, such as the
exhaustion of available addresses.

You can find the IP address of your device using the following command:

```bash
ip addr show
```

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: wwp195s0f3u4: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 76:fc:4c:eb:8b:7e brd ff:ff:ff:ff:ff:ff
3: wlp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1280 qdisc noqueue state UP group default qlen 1000
    link/ether 04:7b:cb:c8:b3:36 brd ff:ff:ff:ff:ff:ff
    altname wlx047bcbc8b336
    inet 10.20.1.36/20 brd 10.20.15.255 scope global dynamic noprefixroute wlp1s0
       valid_lft 352sec preferred_lft 352sec
    inet6 2a13:fcc0:2ed8:1002:c436:fd76:19b9:7422/64 scope global dynamic noprefixroute 
       valid_lft 3573sec preferred_lft 1773sec
    inet6 fd01:67c:2ed8:1002:c2e5:529:eb61:997f/64 scope global dynamic noprefixroute 
       valid_lft 86218sec preferred_lft 14218sec
    inet6 fe80::f2c6:7bf2:6d25:5a32/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

In this example output, the IPv4 address of the wireless interface `wlp1s0` is
`10.20.1.36`.

The device also has multiple IPv6 addresses assigned to it.

 - 2a13:fcc0:2ed8:1002:c436:fd76:19b9:7422/64 (global)
 - fd01:67c:2ed8:1002:c2e5:529:eb61:997f/64 (global)
 - fe80::f2c6:7bf2:6d25:5a32/64 (link-local)

#### Subnetting

In above example, the IP addresses contain a subnet mask, which is represented in
CIDR notation (e.g. /20 for IPv4 and /64 for IPv6).

This allows for the division of an IP address space into smaller subnets, which can be used to organize and manage
network resources more effectively.

In a home setting, a /24 subnet is commonly used for IPv4, which allows for up to 254 usable host addresses.

In IPv6, networks with end-user devices are typically assigned a /64 subnet.

You can use the `sipcalc` command to calculate the network address, broadcast address,
and range of usable host addresses for a given IP address and subnet mask.

```bash
$ sipcalc 192.168.32.1/24
-[ipv4 : 192.168.32.1/24] - 0

[CIDR]
Host address		- 192.168.32.1
Host address (decimal)	- 3232243713
Host address (hex)	- C0A82001
Network address		- 192.168.32.0
Network mask		- 255.255.255.0
Network mask (bits)	- 24
Network mask (hex)	- FFFFFF00
Broadcast address	- 192.168.32.255
Cisco wildcard		- 0.0.0.255
Addresses in network	- 256
Network range		- 192.168.32.0 - 192.168.32.255
Usable range		- 192.168.32.1 - 192.168.32.254
```

Each subnet also has a network address and a broadcast address. The network address is used to identify
the subnet, while the broadcast address is used to send messages to all devices within the subnet.

The network address is the first address in the subnet, while the broadcast address is the last address in the subnet.

#### IPv4 Address

An IPv4 address is a 32-bit numerical address that uniquely identifies a device on an IPv4 network.
It is usually represented in dotted decimal notation, consisting of four octets separated by periods
(e.g. 192.168.1.122).

In the past, Classful addressing was used to divide the IPv4 address space into different
classes (A, B, C, D, E). However, this approach has been largely replaced by
Classless Inter-Domain Routing (CIDR), which allows for more flexible allocation of IP addresses.

There exists special IPv4 addresses reserved for private networks, which are not routable on the public Internet. These addresses are defined in RFC 1918 and include:

| Network (CIDR) | Network Address | Broadcast Address | Number of Addresses |
| --------------- | ------------- | ----------------- | ------------------- |
| 10.0.0.0/8 | 10.0.0.0 | 10.255.255.255 | 16777216 |
| 172.16.0.0/12 | 172.16.0.0 | 172.31.255.255 | 1048576 |
| 192.168.0.0/16 | 192.168.0.0 | 192.168.255.255 | 65536 |

!!! note

    These private addresses can be used in multiple home networks without conflict, as they are not routable on the public Internet.