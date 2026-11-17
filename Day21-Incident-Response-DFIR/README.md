# Day 21: Incident Response (IR) and Digital Forensics (DFIR) — Live Response, Artifact Collection, and Process Tree Analysis

## Overview
# Day 21: Incident Response (IR) and Digital Forensics (DFIR) — Live Response, Artifact Collection, and Process Tree Analysis

## Overview
Execution of structured Incident Response (IR) methodologies on Linux. Live system artifact acquisition (`ss`, `ps`), preservation of volatile evidence without compromising chain of custody, and identification of suspicious process inheritance patterns.

## Lab Architecture
- **Target Compromised Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Forensic Analyst Station:** ThinkPad E14 (`192.168.10.38)
- **Framework Standard:** NIST / SANS Incident Response Lifecycle

---

## 1. Live Artifact Acquisition
Command: `sudo ss -tunap > ~/incident_netstat.txt` and `ps auxf > ~/incident_processes.txt`
Summary of captured evidence files on target:
```text
-rw-rw-r-- 1 anton anton 53K Nov 17 07:35 /home/anton/incident_netstat.txt
```
