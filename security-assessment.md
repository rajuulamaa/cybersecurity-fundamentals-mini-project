# Day 6 — Security Assessment Report

## 1. Executive Summary

An authorized security assessment was conducted against Metasploitable 2 in an isolated UTM virtual network.

The assessment identified 30 open TCP ports, legacy network services, anonymous FTP access, anonymous writable SMB access, and a root-level shell accessible through TCP port 6200.

The most serious finding was confirmed root command execution on the target system. This demonstrates the potential impact of an exposed backdoor service.

Overall qualitative risk: CRITICAL

## 2. Scope and Environment

| Item | Details |
|---|---|
| Assessment type | Authorized educational lab |
| Attacker system | Kali Linux ARM64 |
| Target system | Metasploitable 2 Linux |
| Virtualization | UTM |
| Network | Isolated host-only network |
| Subnet | 192.168.128.0/24 |
| Kali address | 192.168.128.3 |
| Target address | 192.168.128.2 |
| Tools | Nmap, Netcat, Metasploit, smbclient, Linux utilities |

The assessment was limited to the intentionally vulnerable target VM.

## 3. Methodology

The assessment followed these phases:

1. Network connectivity and host discovery
2. Full TCP port scanning
3. Service and version enumeration
4. FTP, HTTP, and SMB enumeration
5. Investigation of the vsftpd 2.3.4 backdoor
6. Verification of remote command execution
7. Limited post-exploitation inspection
8. Documentation and session cleanup

## 4. Findings

### Finding 1 — Root-Level Shell Accessible on TCP Port 6200

**Severity:** Critical

**Evidence:**
- Service enumeration identified vsftpd 2.3.4 on TCP port 21.
- The Metasploit vsftpd backdoor module was investigated.
- Metasploit attempts did not establish a framework session.
- A manual Netcat connection to TCP port 6200 succeeded.
- The commands whoami and id confirmed execution as root.
- Process inspection identified a root-owned sh process listening on TCP port 6200.
- The shell process had xinetd as its parent.

**Impact:**
A remote user reaching the exposed shell could execute commands with root privileges, resulting in complete compromise of the target system.

**Recommendations:**
- Remove the backdoored or compromised service.
- Rebuild affected systems from trusted images where compromise is suspected.
- Restrict network exposure and block unauthorized access to TCP port 6200.
- Investigate unexpected privileged listeners and review service configurations.
- Use maintained software and verify package integrity.

### Finding 2 — Anonymous Writable SMB Share

**Severity:** High

**Evidence:**
- SMB share enumeration succeeded without credentials.
- The tmp share was accessible anonymously.
- A harmless text file was successfully uploaded to the share.

**Impact:**
Unauthenticated users could place files on the target's shared storage. Depending on other system weaknesses, this may enable data tampering, storage abuse, or further attacks.

**Recommendations:**
- Disable anonymous or guest write access.
- Require authenticated access.
- Apply least-privilege permissions.
- Limit SMB access to trusted hosts.
- Monitor shared-directory changes.

### Finding 3 — Anonymous FTP Authentication

**Severity:** Medium

**Evidence:**
- Anonymous FTP login succeeded.
- The accessible directory listing was empty during the test.

**Impact:**
Anonymous FTP exposes an unauthenticated access path. The assessment did not establish that sensitive files were exposed or that FTP uploads were permitted.

**Recommendations:**
- Disable anonymous FTP unless required.
- Restrict access to approved users and networks.
- Prefer encrypted file-transfer protocols.

### Finding 4 — Extensive Legacy Service Exposure

**Severity:** High

**Evidence:**
- A full TCP scan identified 30 open ports.
- Version enumeration identified services including OpenSSH 4.7p1, Apache 2.2.8, MySQL 5.0.51a, PostgreSQL 8.3.x, and vsftpd 2.3.4.
- Additional listening services were observed during system inspection.

**Impact:**
A broad attack surface increases opportunities for unauthorized access. Legacy software may lack current security maintenance. Individual services were not all independently exploited or proven vulnerable.

**Recommendations:**
- Disable unnecessary services.
- Patch or replace unsupported software.
- Apply firewall rules and network segmentation.
- Regularly audit listening ports and installed packages.

## 5. Post-Exploitation Observations

After root command execution was verified, limited inspection identified:

- Hostname: metasploitable
- Linux kernel: 2.6.24-16-server
- Target network address: 192.168.128.2
- Local accounts including root, msfadmin, and service accounts
- Running network services and web application directories
- A root-owned shell process associated with TCP port 6200

The permissions of /etc/shadow were inspected without publishing password hashes. No persistence mechanisms were installed, and the remote shell was exited.

## 6. Risk Summary

| ID | Finding | Severity |
|---|---|---|
| F-01 | Root-level shell on TCP 6200 | Critical |
| F-02 | Anonymous writable SMB share | High |
| F-03 | Anonymous FTP authentication | Medium |
| F-04 | Extensive legacy service exposure | High |

These are qualitative educational risk ratings, not formal CVSS scores.

## 7. Remediation Priorities

**Immediate priority:** Remove or isolate the exposed privileged shell and investigate the compromised service.

**High priority:** Restrict anonymous SMB write access and reduce unnecessary exposed services.

**Medium priority:** Review anonymous FTP access and replace outdated software.

**Ongoing:** Maintain patch management, network segmentation, service inventories, and security monitoring.

## 8. Limitations

- Testing was performed only in an isolated educational environment.
- The target was intentionally configured to be insecure.
- Metasploit did not establish a framework-managed session.
- Root command execution was confirmed through a manual Netcat connection.
- Not every exposed service was independently tested for vulnerabilities.
- The assessment was not a comprehensive penetration test.

## 9. Conclusion

This lab demonstrated how host discovery, service enumeration, configuration review, and careful verification can reveal serious security weaknesses.

The most significant outcome was confirmation of root command execution through an exposed shell service. Anonymous SMB write access and extensive legacy service exposure were additional important findings.

All activity remained within the authorized training environment.

## 10. Evidence

Eighteen reviewed terminal screenshots are included in the screenshots directory. See the Evidence Index in README.md for the complete categorized list.

