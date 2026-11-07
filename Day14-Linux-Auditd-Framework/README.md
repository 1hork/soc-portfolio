# Day 14: Kernel-Level Telemetry — Linux Audit Framework (auditd), Syscall Tracking, and Credential Access Auditing

## Overview
Deployment and validation of the Linux Audit Daemon (`auditd`) on Ubuntu. Configuration of kernel-space watch rules targeting security-critical system assets, forensic analysis of syscall records via `ausearch`, and identity attribution tracking leveraging `auid` validation.

## Lab Architecture
- **Monitored Endpoint:** Lenovo Legion 5 Pro (`192.168.20.135`)
- **Telemetry Engine:** Linux Audit Framework (`auditd` / Netfilter syscall hooks)
- **Investigation / Attacking Endpoint:** ThinkPad E14 (`192.168.10.x`)

---

## 1. Active Audit Configuration
Output of `sudo auditctl -l` on Legion:
```text
<Вставь сюда вывод команды sudo auditctl -l>
-w /etc/passwd -p wa -k identity_changes
-w /etc/shadow -p rwa -k credential_access
 ```
# Parsed audit event via `sudo ausearch -k credential_access -i`  following `/etc/shadow` query:
type=PROCTITLE msg=audit(11/07/2026 15:04:11.006:704) : proctitle=auditctl -w /etc/shadow -p rwa -k credential_access 
type=PATH msg=audit(11/07/2026 15:04:11.006:704) : item=0 name=/etc/ inode=25690113 dev=103:01 mode=dir,755 ouid=root ogid=root rdev=00:00 nametype=PARENT cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:04:11.006:704) : cwd=/home/anton 
type=SOCKADDR msg=audit(11/07/2026 15:04:11.006:704) : saddr={ saddr_fam=netlink nlnk-fam=16 nlnk-pid=0 } 
type=SYSCALL msg=audit(11/07/2026 15:04:11.006:704) : arch=x86_64 syscall=sendto success=yes exit=1084 a0=0x4 a1=0x7ffd7aebdd60 a2=0x43c a3=0x0 items=1 ppid=53642 pid=53643 auid=anton uid=root gid=root euid=root suid=root fsuid=root egid=root sgid=root fsgid=root tty=pts1 ses=3 comm=auditctl exe=/usr/sbin/auditctl subj=unconfined key=(null) 
type=CONFIG_CHANGE msg=audit(11/07/2026 15:04:11.006:704) : auid=anton ses=3 subj=unconfined op=add_rule key=credential_access list=exit res=yes 
----
type=PROCTITLE msg=audit(11/07/2026 15:04:11.012:706) : proctitle=/usr/sbin/unix_chkpwd anton chkexpiry 
type=PATH msg=audit(11/07/2026 15:04:11.012:706) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:04:11.012:706) : cwd=/home/anton 
type=SYSCALL msg=audit(11/07/2026 15:04:11.012:706) : arch=x86_64 syscall=openat success=yes exit=3 a0=AT_FDCWD a1=0x7d7edd3df62e a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=53645 pid=53646 auid=anton uid=root gid=anton euid=root suid=root fsuid=root egid=shadow sgid=shadow fsgid=shadow tty=pts0 ses=3 comm=unix_chkpwd exe=/usr/sbin/unix_chkpwd subj=unix-chkpwd key=credential_access 
----
type=PROCTITLE msg=audit(11/07/2026 15:04:50.773:711) : proctitle=/usr/sbin/unix_chkpwd anton chkexpiry 
type=PATH msg=audit(11/07/2026 15:04:50.773:711) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:04:50.773:711) : cwd=/ 
type=SYSCALL msg=audit(11/07/2026 15:04:50.773:711) : arch=x86_64 syscall=openat success=yes exit=3 a0=AT_FDCWD a1=0x7515cdfdf62e a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=53696 pid=53698 auid=unset uid=root gid=root euid=root suid=root fsuid=root egid=shadow sgid=shadow fsgid=shadow tty=(none) ses=unset comm=unix_chkpwd exe=/usr/sbin/unix_chkpwd subj=unix-chkpwd key=credential_access 
----
type=PROCTITLE msg=audit(11/07/2026 15:04:50.777:715) : proctitle=sshd-session: anton [priv] 
type=PATH msg=audit(11/07/2026 15:04:50.777:715) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:04:50.777:715) : cwd=/ 
type=SYSCALL msg=audit(11/07/2026 15:04:50.777:715) : arch=x86_64 syscall=openat success=yes exit=10 a0=AT_FDCWD a1=0x712bdcfdf62e a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=2034 pid=53696 auid=anton uid=root gid=root euid=root suid=root fsuid=root egid=root sgid=root fsgid=root tty=(none) ses=38 comm=sshd-session exe=/usr/lib/openssh/sshd-session subj=unconfined key=credential_access 
----
type=PROCTITLE msg=audit(11/07/2026 15:08:03.763:719) : proctitle=/usr/sbin/unix_chkpwd anton nonull 
type=PATH msg=audit(11/07/2026 15:08:03.763:719) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:08:03.763:719) : cwd=/home/anton 
type=SYSCALL msg=audit(11/07/2026 15:08:03.763:719) : arch=x86_64 syscall=openat success=yes exit=3 a0=AT_FDCWD a1=0x7d9a1e3df62e a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=53998 pid=53999 auid=anton uid=root gid=anton euid=root suid=root fsuid=root egid=shadow sgid=shadow fsgid=shadow tty=pts1 ses=38 comm=unix_chkpwd exe=/usr/sbin/unix_chkpwd subj=unix-chkpwd key=credential_access 
----
type=PROCTITLE msg=audit(11/07/2026 15:08:03.771:721) : proctitle=/usr/sbin/unix_chkpwd anton chkexpiry 
type=PATH msg=audit(11/07/2026 15:08:03.771:721) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:08:03.771:721) : cwd=/home/anton 
type=SYSCALL msg=audit(11/07/2026 15:08:03.771:721) : arch=x86_64 syscall=openat success=yes exit=3 a0=AT_FDCWD a1=0x7978b29df62e a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=53998 pid=54000 auid=anton uid=root gid=anton euid=root suid=root fsuid=root egid=shadow sgid=shadow fsgid=shadow tty=pts1 ses=38 comm=unix_chkpwd exe=/usr/sbin/unix_chkpwd subj=unix-chkpwd key=credential_access 
----
type=PROCTITLE msg=audit(11/07/2026 15:08:03.778:725) : proctitle=cat /etc/shadow 
type=PATH msg=audit(11/07/2026 15:08:03.778:725) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:08:03.778:725) : cwd=/home/anton 
type=SYSCALL msg=audit(11/07/2026 15:08:03.778:725) : arch=x86_64 syscall=openat success=yes exit=3 a0=AT_FDCWD a1=0x7ffd98be82c0 a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=54001 pid=54002 auid=anton uid=root gid=root euid=root suid=root fsuid=root egid=root sgid=root fsgid=root tty=pts2 ses=38 comm=cat exe=/usr/lib/cargo/bin/coreutils/cat subj=unconfined key=credential_access 
----
type=PROCTITLE msg=audit(11/07/2026 15:08:31.179:729) : proctitle=/usr/sbin/unix_chkpwd anton chkexpiry 
type=PATH msg=audit(11/07/2026 15:08:31.179:729) : item=0 name=/etc/shadow inode=25690774 dev=103:01 mode=file,640 ouid=root ogid=shadow rdev=00:00 nametype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0 cap_frootid=0 
type=CWD msg=audit(11/07/2026 15:08:31.179:729) : cwd=/home/anton 
type=SYSCALL msg=audit(11/07/2026 15:08:31.179:729) : arch=x86_64 syscall=openat success=yes exit=3 a0=AT_FDCWD a1=0x74c025ddf62e a2=O_RDONLY|O_CLOEXEC a3=0x0 items=1 ppid=54030 pid=54031 auid=anton uid=root gid=anton euid=root suid=root fsuid=root egid=shadow sgid=shadow fsgid=shadow tty=pts0 ses=3 comm=unix_chkpwd exe=/usr/sbin/unix_chkpwd subj=unix-chkpwd key=credential_a
 

