# Day 16: Automated Incident Notification — Webhook Integration, JSON Telemetry Dispatch, and SOAR Foundations

## Overview
Engineering and verification of an automated event-driven incident alerting dispatcher (`soc_alert_dispatcher.sh`). Parsing local HIPS enforcement states, constructing structured JSON telemetry payloads, and dispatching out-of-band alerts via HTTPS Webhooks to incident response communication channels.

## Lab Topology
- **Target / Alerting Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Attacking Probe:** ThinkPad E14 (`192.168.10.x`)
- **HIPS Daemon:** Fail2ban (`sshd` jail)
- **Transport Mechanism:** HTTPS POST / cURL REST Webhook

---

## 1. Alert Dispatcher Implementation (`soc_alert_dispatcher.sh`)
```bash
 Triggering incident webhook for banned IP(s): 192.168.10.38
```
## 2. Captured HTTP POST payload delivered to webhook receiver:
```
POST #a17cc 212.58.114.45
11/10/2026 8:48:28 AM   120 bytes
```
