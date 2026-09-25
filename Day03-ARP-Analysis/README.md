# Day 03: Layer 2 Resolution and ARP Traffic Inspection

## Overview
Analysis of the Address Resolution Protocol (ARP), kernel neighbor tables, and broadcast/unicast resolution mechanics between hosts in the lab.

## 1. Neighbor Table State
Command: `ip neigh show` on Legion:
```text
192.168.20.1 dev enp52s0 lladdr 96:2a:6f:ee:eb:cd REACHABLE


13:36:29.493490 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 192.168.20.1 tell 192.168.20.135, length 28
13:36:29.493869 ARP, Ethernet (len 6), IPv4 (len 4), Reply 192.168.20.1 is-at 96:2a:6f:ee:eb:cd, length 46
