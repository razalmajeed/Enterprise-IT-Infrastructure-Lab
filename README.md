# 🛡️ SOC Home Lab — Threat Detection & Incident Response

A home-based SOC simulation built to experience real-world attack detection using Splunk SIEM, Sysmon, and Active Directory.

## 🎯 What I Built

4 virtual machines on VMware replicating an enterprise network:

| Machine | Role | IP |
|---|---|---|
| Windows Server 2022 | Domain Controller (soc.local) | 192.168.50.129 |
| Windows 11 (sales) | Victim Machine | 192.168.50.128 |
| Ubuntu | Splunk SIEM Server | 192.168.50.127 |
| Kali Linux | Attack Machine | 192.168.50.131 |

## ⚔️ Attacks Simulated

**RDP Brute Force**
- Used Hydra and xFreeRDP from Kali to brute force domain accounts
- Generated 1,853 failed logins (EventCode 4625)
- Triggered account lockout (4740) and captured new admin creation (4720)

**Phishing with Live Malware**
- Delivered real malware inside an ISO file via email
- 61 of 72 VirusTotal vendors flagged it as Trojan/Backdoor
- Sysmon revealed true binary name TRAPFALL.exe despite attacker renaming it
- ISO bypasses Windows Mark-of-the-Web SmartScreen protection

## 🔍 Detection

Behavioral SPL query in Splunk — catches any EXE running from a mounted ISO drive:

```
index=main source="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 Image="E:\\*"
| table _time, host, User, ParentImage, Image, CommandLine
```

Automated scheduled alert fired with Critical severity — confirmed in Triggered Alerts.

## 🗺️ MITRE ATT&CK Coverage

| Technique | ID |
|---|---|
| Spearphishing Attachment | T1566.001 |
| Mark-of-the-Web Bypass | T1553.005 |
| Malicious File Execution | T1204.002 |
| Password Brute Force | T1110.001 |
| Valid Accounts | T1078 |
| Local Account Creation | T1136.001 |
| Account Lockout | T1531 |

## 🛠️ Tools Used

Splunk Enterprise, Sysmon64, VMware Workstation Pro, Active Directory, Hydra, xFreeRDP, Kali Linux, VirusTotal

## 📄 Full Report

Full lab report with screenshots and findings is available in this repo.
