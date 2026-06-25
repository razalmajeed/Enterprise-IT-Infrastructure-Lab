🖥️ Enterprise IT Infrastructure Lab

A home-based enterprise IT environment built to practice real-world IT support and infrastructure administration tasks — Active Directory, account/identity management, security policy configuration, and endpoint monitoring — across a 4-VM Windows/Linux network.

## 🛠️ Lab Environment

4 virtual machines on VMware Workstation Pro — isolated NAT network replicating an enterprise environment.

| VM Role | OS | IP Address |
|---|---|---|
| Domain Controller | Windows Server 2022 | 192.168.50.129 |
| End-User Workstation | Windows 11 | 192.168.50.128 |
| SIEM Server | Ubuntu 64-bit (Splunk) | 192.168.50.127 |
| Security Tools Machine | Kali Linux | 192.168.50.131 |

Domain: `soc.local`  |  Subnet: `192.168.50.0/24`  |  Platform: VMware Workstation Pro

> Note: the domain name `soc.local` is a holdover from the lab's original scope and isn't a reflection of the project's current focus on general IT infrastructure.

## 🗂️ Identity & Access Management

- Created and configured the Active Directory domain on Windows Server 2022.
- Built Organizational Units (OUs) by department — **Sales** and **Accounting** — and created a domain user account in each, mirroring how a real organization segments staff for permissions and policy.
- Joined a Windows 11 workstation to the domain and tested sign-in with department-specific accounts.
- Enabled **audit policy logging** on the domain controller to track authentication and account events.
- Configured **account lockout policy** (lockout threshold and duration) as a security baseline — this policy is what later triggered Event Code 4740 during brute-force testing (see Security Monitoring below), directly tying policy configuration to real detection results.

## 📋 Planned Additions (in progress)

The following are scoped as next steps to round out the lab as a full IT operations environment:

- [ ] Shared folder with NTFS/share permissions scoped to department OUs (e.g. Sales folder accessible only to Sales account)
- [ ] Printer deployment via Group Policy to a specific OU
- [ ] DNS/DHCP troubleshooting writeup (real incident encountered while running static IP configuration)
- [ ] Simulated helpdesk ticket log mapping common support requests (onboarding, lockouts, connectivity issues) to actions taken in this lab

## 🔍 Security Monitoring

The lab also includes a monitoring and detection layer, used to validate that the account lockout policy and audit settings above actually produce visible, actionable signal — and to practice log-based troubleshooting and threat analysis.

**Tools:** Splunk Enterprise 9.3.1, Sysmon64, Splunk Universal Forwarder, TA-Windows, TA-Sysmon

**Scenarios tested:**

1. **RDP brute-force / account lockout monitoring** — Simulated failed login attempts against domain accounts to validate the account lockout policy above. Generated and analyzed Event Codes 4625 (failed logon), 4740 (account lockout), 4720 (account creation), and 4624 (successful logon) in Splunk. Built a 4-panel dashboard (failed logins over time, top source IP, targeted accounts, attack chain summary) to visualize the full event sequence — directly useful for triaging real user lockout tickets.

2. **Malware execution detection (phishing simulation)** — Tested delivery of a malicious payload via a mounted ISO attachment, which bypasses Windows Mark-of-the-Web (MOTW) protection. Used Sysmon (Event ID 1) to detect process execution from the mounted drive regardless of filename, since the file had been renamed by the attacker. Verified detection end-to-end with VirusTotal classification and a scheduled Splunk alert.

**Detection query used (Splunk SPL):**
```
index=main source="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 Image="E:\\*"
| table _time, host, User, ParentImage, Image, CommandLine
```

**MITRE ATT&CK mapping:**

| Technique ID | Technique | Tactic | Event ID |
|---|---|---|---|
| T1110.001 | Password Guessing (Brute Force) | Credential Access | 4625 |
| T1078 | Valid Accounts | Persistence | 4624 |
| T1136.001 | Local Account Creation | Persistence | 4720 |
| T1531 | Account Access Removal (Lockout) | Impact | 4740 |
| T1566.001 | Spearphishing Attachment | Initial Access | — |
| T1553.005 | Mark-of-the-Web Bypass | Defense Evasion | Sysmon EID 1 |
| T1204.002 | Malicious File Execution | Execution | Sysmon EID 1 |

## 🧰 Tools & Technologies

| Category | Tools |
|---|---|
| Identity & Access | Active Directory, Group Policy (GPO), DNS, Audit Policy |
| Virtualization | VMware Workstation Pro |
| Operating Systems | Windows Server 2022, Windows 11, Ubuntu, Kali Linux |
| SIEM / Logging | Splunk Enterprise, Sysmon64, Windows Event Logs |
| Threat Intelligence | VirusTotal |

## 📄 Full Lab Report

A full lab report with screenshots and technical findings is available in this repository.

---

**Mohammed Razal**
linkedin.com/in/razal-majeed | razalmajeed0609@gmail.com
