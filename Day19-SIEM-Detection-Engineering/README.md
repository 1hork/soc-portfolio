# Day 19: SIEM Detection Engineering — Custom Correlation Rules and MITRE ATT&CK Mapping

## Overview
Design, implementation, and validation of custom SIEM detection rules (`local_rules.xml`) mapped to the MITRE ATT&CK framework. Simulation of post-exploitation persistence (`T1053.003`) and suspicious staging execution (`T1059.004`) to verify host telemetry capture and alert triage logic.

## Lab Architecture
- **Monitored Target Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Simulated Threat Actor Host:** ThinkPad E14 (`192.168.10.x`)
- **Detection Schema:** Wazuh XML Ruleset / Systemd Journal Telemetry
- **Mapped Techniques:** `T1053.003` (Cron), `T1059.004` (Unix Shell)

---

## 1. Custom Detection Rules (`local_rules.xml`)
```xml
<group name="local,linux,persistence,execution,">
  <rule id="100101" level="10">
    <if_sid>5402</if_sid>
    <match>crontab</match>
    <description>SOC Custom Alert: Crontab modification detected (Potential Persistence)</description>
    <mitre><id>T1053.003</id></mitre>
  </rule>

  <rule id="100102" level="12">
    <match>/tmp/|/dev/shm/</match>
    <description>SOC Custom Alert: Execution inside world-writable staging directory</description>
    <mitre><id>T1059.004</id></mitre>
  </rule>
</group>

## 2 Captured events via sudo journalctl -f -t sudo -t CRON -t crontab on Legion:


```
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H sudo[50191]: pam_unix(sudo:session): session opened for user root(uid=0) by anton(uid=1000)
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H sudo[50191]: anton : TTY=/dev/pts/0 ; PWD=/home/anton ; USER=root ; COMMAND=/tmp/soc_beacon_test.sh
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H sudo[50191]: pam_unix(sudo:session): session closed for user root
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H crontab[50213]: (anton) LIST (anton)
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H crontab[50212]: (anton) REPLACE (anton)
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H crontab[50214]: (anton) LIST (anton)
Nov 13 10:04:35 anton-Legion-5-Pro-16IAH7H crontab[50216]: (anton) REPLACE (anton)
Nov 13 10:17:01 anton-Legion-5-Pro-16IAH7H CRON[51974]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
Nov 13 10:17:01 anton-Legion-5-Pro-16IAH7H CRON[51976]: (root) CMD (cd / && run-parts --report /etc/cron.hourly)
Nov 13 10:17:01 anton-Legion-5-Pro-16IAH7H CRON[51974]: pam_unix(cron:session): session closed for user root
Nov 13 10:30:01 anton-Legion-5-Pro-16IAH7H CRON[53690]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
Nov 13 10:30:01 anton-Legion-5-Pro-16IAH7H CRON[53692]: (root) CMD (if [ -x /etc/init.d/anacron ] && ! [ -d /run/systemd/system ]; then exec /usr/sbin/invoke-rc.d anacron start >/dev/null; fi)
Nov 13 10:30:01 anton-Legion-5-Pro-16IAH7H CRON[53690]: pam_unix(cron:session): session closed for user root
```
