# Day 07: ICMP Protocol Mechanics, Type/Code Telemetry, and Covert Exfiltration Analysis

## Overview
Analysis of the Internet Control Message Protocol (ICMP) under the OSI Layer 3 framework. Investigation of operational codes (Echo Request/Reply, TTL Exceeded) and detection of covert data exfiltration via crafted ICMP payloads.

## Lab Environment
- **Source (Client):** ThinkPad E14 (`192.168.10.132`)
- **Destination (Target):** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Intermediate Router:** UniFi Dream Router 7 (`192.168.10.1 / 192.168.20.1`)

## 1. TTL Exceeded Error Simulation
Output of `ping -c 1 -t 1 192.168.20.135`:
```text
<Вставь сюда вывод терминала с ошибкой Time to live exceeded>
