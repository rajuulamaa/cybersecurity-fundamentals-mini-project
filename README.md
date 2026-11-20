# Day 6 — Security Fundamentals Mini Project

## Overview
This project documents an authorized cybersecurity assessment conducted in an isolated virtual lab. The goal was to discover hosts, enumerate network services, investigate security weaknesses, demonstrate the impact of a vulnerable service, and record remediation recommendations.

## Lab Environment
- Attacker: Kali Linux ARM64, running in UTM
- Target: Metasploitable 2 Linux, running in UTM
- Network: Isolated host-only lab, 192.168.128.0/24
- Kali IP: 192.168.128.3
- Target IP: 192.168.128.2
- Tools: Nmap, smbclient, Netcat, Metasploit Framework, Linux utilities

All testing was performed against an intentionally vulnerable VM in an authorized lab.

## Methodology and Results

### 1. Host Discovery
- Confirmed connectivity to the target using ping.
- Used Nmap host discovery to identify three active hosts on the lab subnet.

### 2. Port Scanning and Service Enumeration
- Identified 30 open TCP ports on Metasploitable 2.
- Identified services including vsftpd 2.3.4, OpenSSH 4.7p1, Apache 2.2.8, Samba, MySQL 5.0.51a, and PostgreSQL 8.3.x.

### 3. FTP Enumeration
- Confirmed anonymous FTP authentication was allowed.
- The anonymous FTP directory listing was empty during testing.

### 4. HTTP Enumeration
- Identified the Apache 2.2.8 web server.
- Observed intentionally vulnerable web applications in the target's web directory.

### 5. SMB Enumeration
- Enumerated available SMB shares without credentials.
- Accessed the tmp share anonymously.
- Successfully uploaded a harmless text file to the tmp share.

### 6. vsftpd Backdoor Investigation
- Investigated the vsftpd 2.3.4 backdoor using Metasploit.
- Metasploit exploitation attempts did not establish a framework session.
- A manual Netcat connection to TCP port 6200 provided a command shell.
- Verified root-level execution using whoami and id.
- Inspected the system and identified a root-owned shell listening on TCP port 6200, with xinetd as its parent process.

### 7. Post-Exploitation Inspection
- Recorded the hostname, operating system, and kernel information.
- Inspected network interfaces and listening ports.
- Reviewed running processes, local account names, and web directories.
- Checked sensitive-file permissions without collecting password hashes.
- Exited the remote shell after completing the assessment.

## Security Findings

| Finding | Risk | Evidence |
|---|---|---|
| Root-level shell accessible through backdoor service | Critical | Netcat connection, whoami, id |
| Anonymous writable SMB share | High | Anonymous access and successful file upload |
| Anonymous FTP authentication enabled | Medium | Successful anonymous login |
| Numerous exposed legacy network services | High | Nmap port and version scans |
| Outdated software and intentionally vulnerable applications | High | Service enumeration and system inspection |

Risk ratings are qualitative lab assessments, not formal CVSS scores.

## Recommended Remediation
1. Remove compromised or backdoored services and rebuild affected systems from trusted images.
2. Restrict network access to necessary services using host and network firewalls.
3. Disable anonymous SMB write access and apply least-privilege permissions.
4. Disable anonymous FTP unless explicitly required.
5. Update unsupported operating systems and legacy software.
6. Monitor unexpected listening ports, processes, and privileged shell activity.
7. Segment vulnerable test systems from production networks.

## Key Lessons Learned
- Network discovery and service enumeration provide the foundation for security assessments.
- Misconfigured file shares can allow unauthorized file uploads.
- Exploitation tools may fail to create sessions even when manual investigation demonstrates a security issue.
- Verifying command execution and process details produces stronger evidence than relying on tool messages alone.
- Responsible security testing includes documentation, remediation guidance, and session cleanup.

## Evidence
Eighteen reviewed terminal screenshots are included in the Evidence Index below.

## Scope and Ethics
This project was performed solely within an isolated, personally controlled training environment. No third-party systems were targeted.



## Project Documentation

- [Security Assessment Report](security-assessment.md)
- [Lab Network Diagram](network-diagram.md)

## Evidence Index

The following screenshots document the authorized assessment
performed against the isolated Metasploitable 2 lab.

### FTP and HTTP Enumeration
- [Anonymous FTP Login](screenshots/ftp-anonymous-login.png)
- [HTTP Apache Enumeration](screenshots/http-apache-service-enumeration.png)

### SMB Enumeration
- [SMB Share Enumeration](screenshots/smb-anonymous-share-enumeration.png)
- [SMB Temporary Directory Listing](screenshots/smb-anonymous-tmp-listing.png)
- [SMB Anonymous Write Test](screenshots/smb-anonymous-write-test.png)

### VSFTPD 2.3.4 Backdoor Investigation
- [Metasploit Module Discovery](screenshots/metasploit-vsftpd-module-discovery.png)
- [Metasploit Exploit Attempt](screenshots/metasploit-vsftpd-exploit-attempt.png)
- [Backdoor Port 6200 Investigation](screenshots/vsftpd-backdoor-port-6200-investigation.png)
- [Backdoor Port State Recheck](screenshots/vsftpd-backdoor-port-state-recheck.png)
- [Backdoor Root Access](screenshots/vsftpd-backdoor-root-access.png)
- [Root Privilege Verification](screenshots/root-privilege-system-verification.png)
- [Root Filesystem Enumeration](screenshots/root-filesystem-enumeration.png)
- [Backdoor Listener Process](screenshots/vsftpd-backdoor-listener-process.png)

### System Enumeration
- [Network Interface and Uptime](screenshots/root-network-interface-uptime.png)
- [Running Process Enumeration](screenshots/root-running-process-enumeration.png)
- [Linux User Account Enumeration](screenshots/linux-user-account-enumeration.png)
- [Web Root Directory Enumeration](screenshots/web-root-directory-enumeration.png)
- [Listening Network Services](screenshots/network-listening-services-enumeration.png)

