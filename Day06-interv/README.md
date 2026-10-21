```markdown
# Day 06: Inter-VLAN Routing, L2/L3 Header Transitions, and Network Segmentation

## Overview
Investigation of Layer 3 inter-VLAN routing mechanics, Time-To-Live (TTL) decrementation, and Layer 2 frame encapsulation behavior across isolated subnets managed by the UniFi Dream Router 7 (UDR7).

## Lab Topology
- **VLAN 10 (Client Subnet):** ThinkPad E14 (`192.168.10.132/24`), Gateway: `192.168.10.1`
- **VLAN 20 (Target/Server Subnet):** Lenovo Legion 5 Pro (`192.168.20.135/24`), Gateway: `192.168.20.1`
- **Router / Default Gateway:** UniFi Dream Router 7 (`UDR7`)

## 1. Trace Hop Telemetry
Output of `traceroute -I -n -m 5 -q 1 192.168.20.135`:
```text
traceroute to 192.168.20.135 (192.168.20.135), 5 hops max, 60 byte packets
 1  192.168.10.1  0.548 ms
 2  192.168.20.135  0.912 ms
Output of sudo tcpdump -ni enp52s0 -e icmp -c 2 on Legion:
13:32:46.221567 96:2a:6f:ee:eb:cd > 9c:2d:cd:23:83:69, ethertype IPv4 (0x0800), length 98: 192.168.10.132 > 192.168.20.135: ICMP echo request, id 3627, seq 1, length 64
13:32:46.221694 9c:2d:cd:23:83:69 > 96:2a:6f:ee:eb:cd, ethertype IPv4 (0x0800), length 98: 192.168.20.135 > 192.168.10.132: ICMP echo reply, id 3627, seq 1, length 64
