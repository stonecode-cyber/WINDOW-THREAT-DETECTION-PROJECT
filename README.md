# # WINDOWS-INITIAL-ACCESS-AND-DISCOVERY-PROJECT

## Windows Initial Access & Discovery Detection with Windows Event Logs and Sysmon

## Table of Contents

1. [Project Overview](#project-overview)
2. [Understanding Windows Initial Access](#understanding-windows-initial-access)
3. [Detecting RDP-Based Initial Access](#detecting-rdp-based-initial-access)
4. [Detecting Phishing-Based Initial Access](#detecting-phishing-based-initial-access)
5. [Detecting Initial Access Through Removable Media](#detecting-initial-access-through-removable-media)
6. [Situational Awareness and Discovery](#situational-awareness-and-discovery)
7. [Tools & Technologies](#tools--technologies)
8. [Lab Walkthrough & Documentation](#lab-walkthrough--documentation)
9. [Conclusion](#conclusion)

---

### 1. Project Overview

This project explores how threat actors gain initial access to Windows systems and the techniques they use to understand their environment after compromising a host.

Using MITRE ATT&CK techniques and practical TryHackMe investigations, I examined Windows Security logs and Sysmon events to identify indicators of RDP brute-force attacks, phishing-based execution and malicious USB activity.

The project also introduces Windows Discovery techniques and the use of command-line utilities to identify system and account information.

### 2. Understanding Windows Initial Access

Initial Access describes the techniques attackers use to gain entry into a system or network.

**Exposed Services**

- **T1133 – External Remote Services:** Attackers target exposed remote services such as RDP, VNC and SSH, particularly where weak or compromised credentials are in use.
- **T1190 – Exploit Public-Facing Application:** Attackers exploit vulnerable or misconfigured internet-facing applications to gain access.

**User-Driven Methods**

- **T1566 – Phishing:** Attackers trick users into opening malicious links or attachments that may execute malware.
- **T1091 – Replication Through Removable Media:** Attackers use infected removable devices, such as USB drives, to spread malware between systems.

### 3. Detecting RDP-Based Initial Access

I investigated an RDP breach scenario using the TryHackMe Windows Security event log:

`RDP-Security.evtx`

**Key detection events:**

| Event ID | Description | SOC investigation |
|---|---|---|
| 4625 | Failed logon | Identify repeated authentication failures |
| 4624 | Successful logon | Identify successful access |
| 4624, Logon Type 10 | RemoteInteractive logon | Investigate RDP access |

**Investigation findings:**

- Most actively brute-forced account: `Administrator`
- Source IP associated with successful RDP access: `203.205.34.107`
- Real threat actor workstation name: Further investigation required using Sysmon logs.

I pivoted from Windows Security logs to Sysmon Operational logs to investigate the workstation information, demonstrating why correlating multiple log sources is important during incident investigation.

### 4. Detecting Phishing-Based Initial Access

Phishing remains a common initial access method because it exploits user interaction.

I examined suspicious attachment types and delivery methods, including:

- **Binary attachments:** Executable file types such as `.exe`, `.com`, `.scr` and `.cpl`.
- **LNK attachments:** Shortcut files that can be abused to launch malicious scripts or commands.
- **Double extensions:** Files such as `photo.jpg.exe`, designed to appear legitimate to unsuspecting users.

These techniques can leave execution evidence in Sysmon Process Creation events, particularly Event ID 1, where the process image, parent process and command line can provide useful investigative context.

### 5. Detecting Initial Access Through Removable Media

I investigated a USB-based initial access scenario using Sysmon logs.

**Common USB malware indicators include:**

- Malicious shortcuts disguised as legitimate files, such as `RECOVERY.lnk`.
- Executables disguised as folders, such as `Photos.exe`.
- Double-extension files designed to deceive users.

**Investigation finding:**

- USB file launched by the user: `E:\Open Sandisk 4GB USB.exe`
- Detection method: Sysmon Event ID 1 – Process Creation.

The investigation demonstrated how Sysmon can reveal the execution of a suspicious file from an external drive.

### 6. Situational Awareness and Discovery

After gaining initial access, attackers often perform discovery activities to understand the compromised environment, identify users, examine privileges and locate valuable resources.

I explored Windows discovery commands, including:

`net user Administrator`

This command displays information about the specified local user account, including its group memberships.

The exercise introduced the importance of identifying privileged accounts and understanding how attackers may use built-in Windows commands to gather information after compromise.

### 7. Tools & Technologies

- Windows Event Viewer
- Windows Security Logs
- Sysmon
- Command Prompt (CMD)
- MITRE ATT&CK Framework
- TryHackMe Windows Event Log Labs

### 8. Lab Walkthrough & Documentation

For the detailed investigation process, including screenshots, event filtering, log analysis and task-by-task findings, see:

**[Windows Initial Access & Discovery – Lab Walkthrough](./Windows-Initial-Access-and-Discovery-Walkthrough.md)**

### 9. Conclusion

This project strengthened my understanding of how attackers gain initial access to Windows systems and how SOC analysts can investigate these activities using Windows Security logs and Sysmon.

**Key takeaway: Initial access detection is not limited to identifying failed logons. It requires correlating authentication events, process execution and user activity to understand how an attacker entered the environment and what happened next.**
