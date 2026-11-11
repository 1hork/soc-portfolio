# Day 17: Automated Host Security Auditing — Lynis Framework, Hardening Index, and Configuration Baselines

## Overview
Automated host-level security evaluation and compliance auditing using the Lynis framework on Ubuntu. Establishing an initial Hardening Index baseline, extracting critical operational warnings and suggestions, and parsing structured audit artifacts for SecOps integration.

## Lab Architecture
- **Audited Target Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Audit Framework:** Lynis v3.1.6 (CIS / NIST Aligned)
- **Reporting Artifacts:** `/var/log/lynis.log`, `/var/log/lynis-report.dat`
- **Analyst Controller:** ThinkPad E14 (`192.168.10.x`)

---

## 1. Lynis Baseline Execution
Execution command on target:
```bash
sudo lynis audit system --quick
Details:
  Hardening index : 68 [#############       ]
  Tests performed : 267
  Plugins enabled : 1
```
2026-11-11 11:12:45 Warning: Found some information disclosure in SMTP banner (OS or software name) [test:MAIL-8818] [details:-] [solution:-]
 

# Runtime modification
sudo sysctl -w net.ipv4.conf.all.rp_filter=1

# Persistence configuration
echo "net.ipv4.conf.all.rp_filter = 1" | sudo tee /etc/sysctl.d/99-hardening-rpfilter.conf
