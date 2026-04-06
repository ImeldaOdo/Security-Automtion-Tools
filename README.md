
# Security Automation Tools

A Python tool that runs nmap, classifies every open port by risk level, and generates a security audit report automatically. I built this because during my penetration testing lab I was manually classifying 20+ open ports one by one. That gets old fast. So I automated it.

---

## What problem does this solve?

nmap gives you a list of open ports. That is useful but it does not tell you which ones are actually dangerous or what to do about them. This script takes that raw output and turns it into something actionable - every port gets a risk level, a reason why it is risky, and a specific recommendation to fix it.

---

## How it works

```
Run the script with a target IP
        |
        v
nmap scans all 65,535 ports with service detection
        |
        v
Script parses every open port and matches it to a risk database
        |
        v
Each service gets classified: CRITICAL, HIGH, MEDIUM, or LOW
        |
        v
Color-coded results print to terminal
        |
        v
Markdown audit report saved to a file you can share
```

---

## How to run it

```bash
python3 nmap_security_audit.py <target_ip>
```

Against a public practice target:
```bash
python3 nmap_security_audit.py scanme.nmap.org
```

Against a local VM:
```bash
python3 nmap_security_audit.py 172.17.0.2
```

---

## Sample output

```
+--------------------------------------------------+
|     nmap Security Audit & Classification Tool    |
|     Author: Imelda Odo-Effimi                    |
|     github.com/ImeldaOdo                         |
+--------------------------------------------------+

[*] Running nmap scan on 172.17.0.2...
[*] This may take a few minutes for a full port scan.

============================================================
  SECURITY AUDIT RESULTS
  Target: 172.17.0.2
  Scan completed: 2026-03-31 23:45:00
  Total open ports: 20
============================================================

RISK SUMMARY:
  CRITICAL   6 port(s)
  HIGH       4 port(s)
  MEDIUM     8 port(s)
  LOW        2 port(s)

[CRITICAL]
--------------------------------------------------
  Port   : 21/tcp
  Service: ftp
  Reason : Plaintext protocol, known backdoor vulnerabilities (CVE-2011-2523)
  Fix    : Disable FTP. Use SFTP or SCP instead.

  Port   : 23/tcp
  Service: telnet
  Reason : Completely unencrypted. Credentials sent in plaintext.
  Fix    : Disable immediately. Use SSH instead.
```

---

## Risk levels explained

| Level | What it means | Examples |
|-------|--------------|---------|
| CRITICAL | Fix this right now. Direct exploitation vector. | FTP, Telnet, rexec, shell, login |
| HIGH | Fix this soon. Significant exposure risk. | MySQL, PostgreSQL, SMTP, SMB, IRC |
| MEDIUM | Manageable but needs proper configuration. | SSH, HTTP, DNS, SNMP, Tomcat |
| LOW | Generally fine with standard security practices. | HTTPS |

---

## Services the risk database covers

20+ service types including:

**CRITICAL:** ftp, telnet, rexec, rlogin, rsh, exec, shell, login

**HIGH:** mysql, postgresql, mssql, mongodb, redis, smtp, smb, netbios, distccd, vnc, rdp, irc

**MEDIUM:** ssh, http, dns, snmp, tomcat, ajp, rpcbind

**LOW:** https

Anything not in the database gets flagged as MEDIUM with a note to investigate.

---

## What gets saved to a file

After each scan a markdown report gets saved automatically:

```
security_audit_<target>_<timestamp>.md
```

It includes an executive summary table, per-port findings with reasons and fixes, and the raw nmap output at the bottom.

---

## Requirements

```bash
# Install nmap first
brew install nmap        # Mac
sudo apt install nmap    # Ubuntu/Debian

# No extra Python packages needed
python3 nmap_security_audit.py <target>
```

---

## Why I built this

This came directly out of my pentest lab work. I ran nmap manually against Metasploitable, found 20+ open ports, and had to classify every single one by hand. I identified CVE-2011-2523 (vsFTPd backdoor) on port 21 and exploited it using Metasploit to get root access. The whole experience made me realize that classification at scale is not something you want to do manually.

This script automates the reconnaissance and classification steps so you can focus on the actual investigation.

Full pentest writeup: [cyb101-project4-pentest](https://github.com/ImeldaOdo/cyb101-project4-pentest)

---

## Things I want to add next

- CVE lookup via the NVD API for each detected service
- JSON output for piping into other tools
- Email alerts for CRITICAL findings
- Support for scanning multiple targets or CIDR ranges
- A simple web dashboard for the report output

---

*Part of my CYB101 CodePath coursework. Building toward cloud security.*
