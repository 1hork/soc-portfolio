# Day 02: Socket Auditing, TCP Handshake and Netcat Inspection

## Overview
Investigation of transport-layer communication, local socket binding, and TCP three-way handshake telemetry using `ss`, `netcat`, and `tcpdump`.




## 1. Socket Verification
Output of `sudo ss -tulpn` on target:Netid State  Recv-Q Send-Q  Local Address:Port    Peer Address:Port Process                                                             
udp   UNCONN 0      0             0.0.0.0:58855        0.0.0.0:*     users:(("firefox-esr",pid=3665,fd=174))                            
udp   UNCONN 0      0             0.0.0.0:51329        0.0.0.0:*     users:(("firefox-esr",pid=3665,fd=194))                            
udp   UNCONN 0      0             0.0.0.0:43315        0.0.0.0:*     users:(("firefox-esr",pid=3665,fd=95))                             
udp   UNCONN 0      0             0.0.0.0:39907        0.0.0.0:*     users:(("firefox-esr",pid=3665,fd=185))                            
tcp   LISTEN 0      128           0.0.0.0:22           0.0.0.0:*     users:(("sshd",pid=1271,fd=6))                                     
tcp   LISTEN 0      128              [::]:22              [::]:*     users:(("sshd",pid=1271,fd=7))                  


## 2. TCP Handshake Capture
Observed TCP Flags in tcpdump:
- Client -> Server: `[S]` (SYN)
- Server -> Client: `[S.]` (SYN-ACK)
- Client -> Server: `[.]` (ACK)
- Data transfer: `[P.]` (PSH-ACK)

## 3. Defense & Monitoring Insights
- Unused open listening ports directly increase the attack surface of the endpoint.
- Netcat listeners can be abused as unauthorized bind/reverse shells; their processes must be audited via Endpoint Detection and Response (EDR) or periodic socket checks.

git add README.md
git commit -m "feat: complete Day 02 socket analysis and TCP handshake inspection"
