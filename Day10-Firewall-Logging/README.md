# Day 10: Host Firewall Mechanics — UFW Logging Levels, Drop Telemetry, and Kernel Event Auditing

## Overview
Practical evaluation of host-based firewall policy enforcement on Linux. Configuration of UFW logging thresholds, real-time monitoring of packet-filtering events via `journalctl -k`, and field-by-field forensic analysis of kernel drop logs.

## Lab Topology
- **Probe Host:** ThinkPad E14 (`192.168.10.38`)
- **Target Host (Firewall Endpoint):** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Firewall Subsystem:** Uncomplicated Firewall (UFW) / Netfilter Kernel Hooks
- **L3 Gateway:** UniFi Dream Router 7 (`UDR7`)

---

## 1. Firewall Configuration & Rule Revocation
Initial policy verification after switching log verbosity to `medium` and removing the permit rule for TCP port 8080:

```text
Status: active
Logging: on (medium)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
8000/tcp                   ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
8000/tcp (v6)              ALLOW IN    Anywhere (v6)
sudo journalctl -k -f | grep --line-buffered "UFW BLOCK":

Streamed in real-time on the target endpoint using sudo journalctl -k -f | grep --line-buffered "UFW BLOCK" during an inbound connection probe (nc -nv -w 2 192.168.20.135 8080):
:Oct 25 10:43:49 anton-Legion-5-Pro-16IAH7H kernel: [UFW BLOCK] IN=enp52s0 OUT= MAC=9c:2d:cd:23:83:69:96:2a:6f:ee:eb:cd:08:00 SRC=192.168.10.38 DST=192.168.20.135 LEN=60 TOS=0x00 PREC=0x00 TTL=63 ID=21976 DF PROTO=TCP SPT=53922 DPT=8080 WINDOW=64240 RES=0x00 SYN URGP=0 
Oct 25 10:43:50 anton-Legion-5-Pro-16IAH7H kernel: [UFW BLOCK] IN=enp52s0 OUT= MAC=9c:2d:cd:23:83:69:96:2a:6f:ee:eb:cd:08:00 SRC=192.168.10.38 DST=192.168.20.135 LEN=60 TOS=0x00 PREC=0x00 TTL=63 ID=21977 DF PROTO=TCP SPT=53922 DPT=8080 WINDOW=64240 RES=0x00 SYN URGP=0
