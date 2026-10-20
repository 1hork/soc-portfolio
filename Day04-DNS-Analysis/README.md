# Day 04: DNS Architecture, Record Analysis, and Traffic Inspection

## Overview
Practical examination of Domain Name System (DNS) query mechanics, record types (A, PTR), and telemetry inspection using `dig`, `nslookup`, and `tcpdump`.

## 1. Direct and Reverse Resolution
Command output for `dig google.com A`:google.com.             84      IN      A       192.178.24.46
;; Query time: 8 msec
;; SERVER: 192.168.10.1#53(192.168.10.1) (UDP)
;; WHEN: Tue Oct 20 05:22:08 UTC 2026
;; MSG SIZE  rcvd: 55


Reverse lookup result (`dig -x 8.8.8.8`):dns.google.

## 3. Threat Hunting & SOC Observations
- **DNS Tunneling / Exfiltration:** Plaintext DNS over UDP/53 often bypasses perimeter firewalls. Malicious actors encode data into domain prefixes to exfiltrate secrets.
- **Monitoring Indicators:**
  - High volume of queries to high-entropy, randomly generated domain names (DGA — Domain Generation Algorithms).
  - Unusually large TXT record responses.
  - Queries directly sent to external resolvers bypassing internal corporate DNS / gateway (UDR7).
