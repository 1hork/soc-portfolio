# Day 15: SecOps Automation — Bash Log Telemetry Parser, IoC Extraction, and Incident Reporting

## Overview
Development and deployment of a lightweight, automated host-based log auditing script (`soc_auth_monitor.sh`) using POSIX text-processing utilities (`awk`, `grep`, `uniq`, `sort`). Extraction of authentication indicators of compromise (IoC), top targeted accounts, and active HIPS mitigations.

## Lab Architecture
- **Target Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Auditing Host:** ThinkPad E14 (`192.168.10.x`)
- **Automation Framework:** GNU Bash (Strict Mode: `set -euo pipefail`)
- **Data Ingestion Source:** `systemd-journald` (`ssh.service`, `sudo`)

---

## 1. Automation Script Implementation (`soc_auth_monitor.sh`)
```bash

#!/usr/bin/env bash
#
# SOC Daily Security Audit Script: SSH & PAM Telemetry Parser
# Target: Ubuntu Server / OpenSSH
#

set -euo pipefail

REPORT_DIR="/var/log/soc-reports"
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")
REPORT_FILE="${REPORT_DIR}/auth_summary_${TIMESTAMP}.txt"

sudo mkdir -p "${REPORT_DIR}"

echo "======================================================" > "${REPORT_FILE}"
echo "           SOC ENDPOINT SECURITY AUDIT REPORT         " >> "${REPORT_FILE}"
echo " Host: $(hostname) | Date: $(date -u) " >> "${REPORT_FILE}"
echo "======================================================" >> "${REPORT_FILE}"
echo "" >> "${REPORT_FILE}"

echo "[+] 1. Top 5 Attacking IP Addresses (Failed Passwords):" >> "${REPORT_FILE}"
sudo journalctl -u ssh --since "24 hours ago" --no-pager \
  | grep "Failed password" \
  | awk '{for(i=1;i<=NF;i++) if($i=="from") print $(i+1)}' \
  | sort | uniq -c | sort -nr | head -n 5 >> "${REPORT_FILE}" || true
echo "" >> "${REPORT_FILE}"

echo "[+] 2. Top Targeted Invalid Usernames:" >> "${REPORT_FILE}"
sudo journalctl -u ssh --since "24 hours ago" --no-pager \
  | grep "Invalid user" \
  | awk '{for(i=1;i<=NF;i++) if($i=="user") print $(i+1)}' \
  | sort | uniq -c | sort -nr | head -n 5 >> "${REPORT_FILE}" || true
echo "" >> "${REPORT_FILE}"

echo "[+] 3. Fail2ban Active Jail Status:" >> "${REPORT_FILE}"
if command -v fail2ban-client &> /dev/null; then
    sudo fail2ban-client status sshd >> "${REPORT_FILE}" 2>&1 || echo "sshd jail not running" >> "${REPORT_FILE}"
else
    echo "Fail2ban not installed." >> "${REPORT_FILE}"
fi
echo "" >> "${REPORT_FILE}"

echo "[+] 4. Sudo Execution & Privilege Escalation Events:" >> "${REPORT_FILE}"
sudo journalctl _COMM=sudo --since "24 hours ago" --no-pager \
  | grep "COMMAND=" \
  | awk '{print $1, $2, $3, $6, $NF}' \
  | tail -n 10 >> "${REPORT_FILE}" || true

echo "Report generated successfully at: ${REPORT_FILE}"
cat "${REPORT_FILE}"
```
