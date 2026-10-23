# Day 08: Reconnaissance Telemetry — Nmap Scan Mechanics (SYN vs Connect) and RST Packet Signatures

## Overview
Comparative analysis of transport-layer network reconnaissance methods using Nmap. Inspection of raw TCP packet flags (`[S]`, `[S.]`, `[R]`, `[R.]`) during full TCP Connect (`-sT`) and half-open SYN Stealth (`-sS`) sweeps against target services.

## Lab Environment
- **Attacking / Scanning Host:** ThinkPad E14 (`192.168.10.132`)
- **Target Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Router / L3 Gateway:** UniFi Dream Router 7 (`UDR7`)

## 1. Nmap Scan Execution
Scan command output (`sudo nmap -sS -p 22,80,443 -Pn -n 192.168.20.135`):

