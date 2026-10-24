# Day 09: SSH Service Analysis, Banner Grabbing, and Authentication Telemetry

## Overview
Analysis of the Secure Shell (SSH) protocol at Layer 7, examination of service banner exposure, simulation of credential brute-force attempts, and inspection of host authentication telemetry via `journalctl`.

## Lab Environment
- **Client / Probe Host:** ThinkPad E14 (`192.168.10.132`)
- **Server / Target Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Monitored Service:** OpenSSH Daemon (`port 22/tcp`)

## 1. Banner Grabbing & Service Enumeration
Output of `nc -nv 192.168.20.135 22`:
```text
(UNKNOWN) [192.168.20.135] 22 (ssh) open
SSH-2.0-OpenSSH_10.2p1 Ubuntu-2ubuntu3.8

nmap -sV -p 22 -Pn -n 192.168.20.135:
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.2p1 Ubuntu 2ubuntu3.8 (Ubuntu Linux; protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

sudo journalctl -u ssh -f:
Oct 24 12:16:16 anton-Legion-5-Pro-16IAH7H sshd-session[10904]: Invalid user fake_analyst from 192.168.10.132 port 38394
Oct 24 12:16:33 anton-Legion-5-Pro-16IAH7H sshd-session[10904]: pam_unix(sshd:auth): check pass; user unknown
Oct 24 12:16:33 anton-Legion-5-Pro-16IAH7H sshd-session[10904]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.10.132
Oct 24 12:16:35 anton-Legion-5-Pro-16IAH7H sshd-session[10904]: Failed password for invalid user fake_analyst from 192.168.10.132 port 38394 ssh2
