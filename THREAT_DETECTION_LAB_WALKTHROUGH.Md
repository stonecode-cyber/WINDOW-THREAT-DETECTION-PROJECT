All window threat is always initaited through initial access by attackers,   there are way attacker can  gain access into our systems such as in accordance to MITRE techniques.

**EXPOSED SERVICES**

[T1133 / External Remote Services](https://attack.mitre.org/techniques/T1133/): Threat actors will look for exposed RDP/VNC/SSH with weak passwords to get remote access to the machine.
[T1190 / Exploit Public-Facing Application](https://attack.mitre.org/techniques/T1190/): Threat actors will look for misconfigured or vulnerable websites and applications.

**USER DRIVEN METHOD**

Human often help threat actors by clicking on malicious links, launching phishing attachments, using pirated software, picking up unknown USB devices, and so on. And since humans are still the weakest link in cyber security and Windows is the most popular OS for user workstations, you are very likely to handle user-driven Windows Initial Access alerts frequently. The methods are covered by these MITRE techniques:
[T1566 / Phishing](https://attack.mitre.org/techniques/T1566/): Threat actors employ different phishing techniques, tricking users into launching the malware themselves
[T1091 / Removable Media](https://attack.mitre.org/techniques/T1091/): Threat actors infect a USB device and hope that users will use the USB on multiple PCs.

**DETECING INITIAL ACCESS  THROUGH AN RDP**

I have tabulated  below steps OF RDP attacks and the detEction methodsfor a SOC analyst.

<img width="238" height="236" alt="RDP STEPS OF ATTCK" src="https://github.com/user-attachments/assets/7647bdfe-1da4-44c4-a9d8-2d3c3449d489" />


I will be following the steps above in the RDP practical project with  the Tryhackme  extracted logs beloW
For this task, open the VM and analyze the logs of the RDP breach scenario:
C:\Users\Administrator\Desktop\Practice\RDP Case\RDP-Security.evtx

TASK 1: Which user seems to be most actively brute-forced by botnets?

In opened the logs , filter the log for unsuccesfull loging with event ID 4625 , this indicated the most actively attcked user is the **ADMINISTRATOR**  account s shown in the screenshot below:

<img width="485" height="366" alt="RDP DETCETION TSK 1 A" src="https://github.com/user-attachments/assets/85e050c1-4a50-46bf-854b-789dc8048228" />

<img width="472" height="367" alt="RDP DETECTION TSK 1B" src="https://github.com/user-attachments/assets/e97e8140-2dd5-46c0-8d8b-69bde895f6f6" />

TASK 2: Which IP managed to breach the host via RDP ?

Since remote  successfull logon id=s indicated by **EVENT ID 4624,**. which indicated succcesfull login and i search for logon type  10 that insicated RDP login:  This reveal the  ip address to be **203.205.34.107**

<img width="493" height="377" alt="rdp detection tsk 2a" src="https://github.com/user-attachments/assets/6b91cd24-4253-47cb-a38b-fb54efc49fcf" />

<img width="495" height="376" alt="rdp detection task 2b" src="https://github.com/user-attachments/assets/0bf23f17-0ca1-4265-8049-92a8a9847c30" />

TASK 3: Can you get the real Workstation Name (hostname) of the threat actor?

In this task, the real workstaion is not revealed by only filtering the  evnt ID 4624.
I then option to switch to sysmon logs for more analysis of the log by  opening the  **Applications and Services Logs → Microsoft → Windows → Sysmon → Operational**** within the event veiwer as shown below:

<img width="276" height="335" alt="rdp detcteion task 3a1" src="https://github.com/user-attachments/assets/2da9a3d0-82c8-49fa-9f73-dd39282c64a0" />

<img width="228" height="321" alt="rdp detcetion tsk 3b2" src="https://github.com/user-attachments/assets/28d6274a-4bfa-4649-9d58-1d56cf7c46a9" />

It is not difficult to detect an exposed RDP service simply by examining Windows Security logs. If you assign a public IP address to a server, disable the firewall, enable RDP, and leave it exposed to the internet for about an hour, you will likely start seeing authentication attempts in your logs.
Automated bots constantly scan the internet for exposed RDP services and attempt to gain access through brute-force attacks. These attempts can generate hundreds of Event ID 4625 (Failed Logon) events within a short period, making the activity relatively easy to spot.
However, keep in mind that RDP compromise does not always involve brute-force attacks. Threat actors may gain access using credentials they already possess, allowing them to authenticate successfully without generating a large volume of failed login attempts. That is a different attack scenario worth exploring separately.

**### INITIAL ACCESS VIA PHISHING**

Phishing attacks are still a major threat as they can't be mitigated as easily as blocking RDP access. To make it worse.

**Binary Attachments:**  
There are [lots](https://github.com/michalzobec/Security-Blocked-File-Extensions-Attachments/blob/main/list-of-blocked-file-extensions.txt) of executable extensions, and while most people know not to open untrusted .exe files, tMajority are not aware  about **_.com, .scr, or .cpl_** files and the harm they can cause.

**LNK Attachments**
Threat actors may prefer attaching PowerShell , Visual Basic, or BAT scripts over binaries to avoid Antivirus. A popular way to make the scripts look trustworthy is to hide them behind LNK shortcuts - the same files you have on your Desktop that point to real executables somewhere in the Program Files folder.

Phishing file  may  contain the bnary  attachment, LNK attachement or conatin double extension as shown t he the screenshot below:

<img width="437" height="265" alt="phishun detection rta" src="https://github.com/user-attachments/assets/6e8ad895-e396-4a4d-b8e1-ca772e27a5bf" />

**### Detecting an Infected  removable media (USB)**

In  most cases malware on USB doenst run themself automatically, they require someone to run the., if not for recent  advance malwr that could run automtically. Example include:

- Malware hides all legitimate files on USB and creates a malicious "RECOVERY.lnk" file
- Malware creates a "Photos.exe" binary and sets its icon to look like a simple folder
- Malware creates double-extension copies of all files, like "photo_2024_1_12.jpg.exe"

However, the detection of Initial Access via USB looks very similar to the phishing attachments. Both  rely on a user running malicious binary via a graphical interface (explorer.exe). The evidence can be find in  external drives like "E:\malware.exe"

I will be looking at theb task below using the log provided by Tryhack me log

**TASK 1: Which USB file was launched by the user?**

In this task, i opened the log and filter for process creation to have a look at how the attack was initited with EVENT ID 1,
from the log we find an envnet that show fromthe command line that the USB  use to initiate the attack is **_E:\Open Sandisk 4GB USB.exe_** . As shown in the screenshot below:

<img width="488" height="359" alt="USB INITIAL ACCESS TASK 1" src="https://github.com/user-attachments/assets/ad642444-c07e-480b-b831-79ab719e7ba7" />

TAKE AWAY:

Windows Initial Access relies mainly on exposed services (detectable via default Windows authentication logs 4624/4625 for RDP) and user-driven attacks (best detected via Sysmon process execution logs). Each specific delivery vector, such as LNK files, produces distinct artifacts that require hands-on practice to recognize.

 **### Situational awareness and Discovery Commands**

After and attacker gain initial access to  system and network, the  attacker tend to understand its environment by looking around for the hiden treasure known as the **Discovery Tactics.**
I have tabulated what attacker  will be looking out for below:

<img width="379" height="271" alt="discovery detail" src="https://github.com/user-attachments/assets/50a6486e-7716-495b-b8eb-39ebd08869bf" />

**Open CMD and type "net user Administrator".
Which privileged group does the user belong to?**

<img width="476" height="294" alt="cmd discovery overveiw" src="https://github.com/user-attachments/assets/0d03aeee-5066-4337-bf6d-72fa64649bdf" />



