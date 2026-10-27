# Day 12: Centralized Telemetry Aggregation — Remote Syslog Architecture, RFC Compliance, and Network Stream Auditing

## Overview
Implementation and analysis of a host-to-collector centralized logging pipeline using `rsyslog`. Configuration of a remote Syslog daemon on Ubuntu, network forwarding over UDP/514 from Kali Linux, socket state auditing via `ss`, and deep-packet payload inspection of security alerts using `tcpdump`.

## Lab Topology
- **Forwarding Host (Client):** ThinkPad E14 (`192.168.10.38`)
- **Collector / Aggregator (Server):** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Transport Protocol:** UDP port 514 (Syslog Service)
- **L3 Boundary:** UniFi Dream Router 7 (`UDR7`)

---

## 1. Collector Socket Configuration
Output of `sudo ss -ulpn | grep 514` on Legion:
```text
UNCONN 0      0            0.0.0.0:514        0.0.0.0:*    users:(("rsyslogd",pid=11767,fd=5))
UNCONN 0      0               [::]:514           [::]:*    users:(("rsyslogd",pid=11767,fd=6))

Captured via sudo tcpdump -ni enp52s0 udp port 514 -A -v on Legion during event dispatch:

IP (tos 0x0, ttl 63, id 47749, offset 0, flags [DF], proto UDP (17), length 212)
    192.168.10.38.47065 > 192.168.20.135.514: SYSLOG, length: 184
    Facility authpriv (10), Severity alert (1)
    Msg: 1 2026-10-27T10:41:10.495422+04:00 localhost chigurh - - [timeQuality tzKnown="1" isSynced="1" syncAccuracy="119000"] SEC_INCIDENT_TEST: Unauthorized access detected from subnet 10
E.....@.?.....
&..........1.<81>1 2026-10-27T10:41:10.495422+04:00 localhost chigurh - - [timeQuality tzKnown="1" isSynced="1" syncAccuracy="119000"] SEC_INCIDENT_TEST: Unauthorized access detected from subnet 10
