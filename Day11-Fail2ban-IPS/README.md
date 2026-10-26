# Day 11: Host Intrusion Prevention System (HIPS) — Fail2ban SSH Protection and Dynamic Netfilter Mitigation

## Overview
Deployment and validation of host-based intrusion prevention mechanisms using Fail2ban. Automated parsing of `systemd-journald` authentication telemetry, brute-force threshold detection, dynamic modification of kernel packet-filtering chains, and structured unban verification.

## Lab Architecture
- **Probe / Attacking Host:** ThinkPad E14 (`192.168.10.x`)
- **Target Host (Protected Endpoint):** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Defense Subsystem:** Fail2ban v0.11+ / Netfilter iptables
- **Monitored Endpoint:** OpenSSH Daemon (`port 22/tcp`)

---

## 1. Fail2ban Configuration (`/etc/fail2ban/jail.local`)
```ini
[sshd]enabled = true
port = 22
mode = normal
backend = systemd
maxretry = 3
findtime = 600
bantime = 600

Captured ban event from /var/log/fail2ban.log:

2026-10-26 11:24:19,304 fail2ban.filter         [13395]: INFO      maxLines: 1
2026-10-26 11:24:19,309 fail2ban.filtersystemd  [13395]: INFO    [sshd] Added journal match for: '_SYSTEMD_UNIT=ssh.service + _COMM=sshd'
2026-10-26 11:24:19,309 fail2ban.filter         [13395]: INFO      maxRetry: 3
2026-10-26 11:24:19,309 fail2ban.filter         [13395]: INFO      findtime: 600
2026-10-26 11:24:19,309 fail2ban.actions        [13395]: INFO      banTime: 600
2026-10-26 11:24:19,309 fail2ban.filter         [13395]: INFO      encoding: UTF-8
2026-10-26 11:24:19,310 fail2ban.jail           [13395]: INFO    Jail 'sshd' started
2026-10-26 11:24:19,310 fail2ban.filtersystemd  [13395]: INFO    [sshd] Jail is in operation now (process new journal entries)
2026-10-26 12:09:26,832 fail2ban.filter         [13395]: INFO    [sshd] Found 192.168.10.38 - 2026-10-26 12:09:26
2026-10-26 12:09:38,261 fail2ban.filter         [13395]: INFO    [sshd] Found 192.168.10.38 - 2026-10-26 12:09:38
2026-10-26 12:10:10,805 fail2ban.filter         [13395]: INFO    [sshd] Found 192.168.10.38 - 2026-10-26 12:10:10
2026-10-26 12:10:10,876 fail2ban.actions        [13395]: NOTICE  [sshd] Ban 192.168.10.38
^C

