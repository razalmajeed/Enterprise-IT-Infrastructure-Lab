🛡️ SOC Home Lab — Threat Detection & Incident Response
A home-based SOC simulation built to experience real-world attack detection using Splunk SIEM, Sysmon, and Active Directory. Simulated live attacks, built automated detection rules, and mapped all findings to MITRE ATT&CK.

🖥️ Lab Environment
4 virtual machines on VMware Workstation Pro — isolated NAT network replicating an enterprise environment.
VMRoleIP AddressWindows Server 2022Domain Controller (soc.local)192.168.50.129Windows 11 (sales)Victim / End-user Machine192.168.50.128Ubuntu 64-bitSplunk SIEM Server192.168.50.127Kali LinuxAttack Machine192.168.50.131
Domain: soc.local  |  Subnet: 192.168.50.0/24  |  Platform: VMware Workstation Pro

⚔️ Attacks Simulated
1. RDP Brute Force

Used Hydra and xFreeRDP from Kali to brute force domain accounts
Generated 1,853 failed login events (EventCode 4625)
Triggered account lockout (EventCode 4740) after exceeding threshold
Captured new admin account creation via RDP session (EventCode 4720)
Verified successful login (EventCode 4624) after credentials obtained

2. Phishing with Live Malware

Delivered real malware inside an .iso file attachment via Thunderbird email
ISO mounted as DVD Drive E:\ — bypassing Windows Mark-of-the-Web (MOTW) SmartScreen protection
61 out of 72 VirusTotal vendors flagged the payload as malicious
Malware classified as: Trojan.Agent.EJZZ / Backdoor:Win32/Injector
Sysmon revealed true binary name TRAPFALL.exe despite attacker renaming it
SHA256: F74D3364EF9D3E3DF4173A7F12D48348AF44BF150C0FFCD01F0054E86EBB7E0E


🔍 Detection Engineering
Behavioral SPL Detection Query
Catches any EXE executing from a mounted ISO drive — works regardless of filename or hash:
splindex=main source="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 Image="E:\\*"
| table _time, host, User, ParentImage, Image, CommandLine
Automated Alerts
AlertSeverityTriggerSuspicious Execution (Phishing Indicator)CriticalEXE running from mounted ISO driveRDP Brute Force MonitorHighMultiple failed logins from single IP

Alerts run on a scheduled basis every hour — SOC best practice for resource efficiency
Phishing alert auto-fired at 04:15:00 on 30/05/2026 — verified end-to-end in Triggered Alerts

Dashboard — RDP Brute Force Monitor
Built a custom Splunk dashboard with 4 panels:

Failed Logins Over Time — attack spike visualized (May 21, ~2,000 attempts)
Top Attacking IP — confirmed Kali (192.168.50.131) as attacker
Targeted Accounts — shows which domain accounts were most targeted
Attack Chain Summary — EventCodes 4624, 4625, 4720, 4740 visualized together


🗺️ MITRE ATT&CK Coverage
Technique IDTechniqueTacticEvent IDT1566.001Spearphishing AttachmentInitial Access—T1553.005Mark-of-the-Web BypassDefense EvasionSysmon EID 1T1204.002Malicious File ExecutionExecutionSysmon EID 1T1110.001Password Guessing (Brute Force)Credential Access4625T1078Valid AccountsPersistence4624T1136.001Local Account CreationPersistence4720T1531Account Access Removal (Lockout)Impact4740

🛠️ Tools & Technologies
CategoryToolsSIEMSplunk Enterprise 9.3.1Endpoint LoggingSysmon64, Windows Event LogsLog ForwardingSplunk Universal Forwarder, TA-Windows, TA-SysmonAttack ToolsHydra, xFreeRDP, Kali LinuxIdentity & AccessActive Directory, Group Policy (GPO), DNSThreat IntelligenceVirusTotalVirtualizationVMware Workstation ProOperating SystemsWindows Server 2022, Windows 11, Ubuntu, Kali Linux

💡 Key Findings
Behavioral detection beats signature detection
The phishing SPL query targets any EXE running from a mounted virtual drive — not a specific filename or hash. Even after the attacker renamed the malware, the detection still fired because the behavior stayed the same.
ISO files bypass Windows SmartScreen (MOTW)
Files inside ISO containers bypass Mark-of-the-Web protection. Sysmon-based detection catches execution at the process level instead — making it a reliable alternative.
Sysmon reveals what Windows Event Logs miss
Default Event Logs miss critical details. Sysmon captures parent process, command line, original filename, hash, and user — giving a complete forensic picture on every process creation.
Automated alerts = 24/7 SOC coverage
Scheduled alerts mean the environment watches itself without manual searching. The phishing alert fired automatically with Critical severity — confirming the full detection pipeline works end-to-end.

📄 Full Lab Report
Full lab report with screenshots and technical findings is available in this repository.

👤 
Mohammed Razal
linkedin.com/in/mohammed-razal-cyber | razalmajeed0609@gmail.com
