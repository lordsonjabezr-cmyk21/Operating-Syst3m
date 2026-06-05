# Windows Process Analysis for SOC Analysts

## Introduction

Windows process analysis is one of the most important skills for a Security Operations Center (SOC) Analyst. Every application, service, script, or malware executed on a Windows system runs as a process. Understanding how processes interact helps analysts identify suspicious behavior, investigate alerts, and detect potential security incidents.

---

## What is a Process?

A process is a running instance of a program in memory.

For example:

```text
notepad.exe
```

When a user launches Notepad, Windows creates a process that contains:

* Process Name
* Process ID (PID)
* Parent Process ID (PPID)
* User Context
* Command Line Arguments
* File Path
* Start Time

SOC analysts use this information to determine whether activity is legitimate or malicious.

---

## Parent-Child Process Relationships

Processes rarely operate independently. Most processes are created by another process known as the parent process.

Example:

```text
explorer.exe
    └── chrome.exe
```

This indicates that the user launched Google Chrome through Windows Explorer, which is normal behavior.

### Why Parent Processes Matter

Parent processes often reveal the source of suspicious activity.

Example:

```text
winword.exe
    └── powershell.exe
```

Microsoft Word typically should not launch PowerShell. This may indicate:

* Malicious macros
* Phishing attachments
* Initial malware execution

---

## Critical Windows Processes Every SOC Analyst Should Know

### System

Core Windows kernel process.

### smss.exe

Session Manager Subsystem responsible for initializing user sessions.

### wininit.exe

Windows Initialization Process responsible for launching critical system services.

### services.exe

Service Control Manager responsible for starting and managing Windows services.

### svchost.exe

Hosts Windows services running from DLLs.

### lsass.exe

Local Security Authority Subsystem Service responsible for:

* User authentication
* Kerberos
* NTLM
* Credential handling

### explorer.exe

Windows graphical shell responsible for launching user applications.

### cmd.exe

Command Prompt interpreter.

### powershell.exe

Advanced command-line and automation framework frequently abused by attackers.

---

## Process Investigation Methodology

Whenever a suspicious process is observed, the following questions should be asked:

### 1. What is the Process Name?

Example:

```text
powershell.exe
```

Determine whether the process is expected within the environment.

---

### 2. What is the Parent Process?

Example:

```text
winword.exe
    └── powershell.exe
```

Suspicious parent-child relationships often indicate malicious execution.

---

### 3. What is the Command Line?

Example:

```powershell
powershell.exe -enc SQBFAFgA
```

Investigate indicators such as:

* Encoded Commands (-enc)
* EncodedCommand
* IEX
* DownloadString
* Net.WebClient
* ExecutionPolicy Bypass

---

### 4. What is the Executable Path?

Legitimate:

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

Suspicious:

```text
C:\Users\Public\powershell.exe
```

Attackers frequently disguise malware using legitimate filenames.

---

### 5. Which User Executed the Process?

Determine:

* Username
* Privilege level
* Expected activity
* Time of execution

Unexpected administrative activity should be investigated immediately.

---

### 6. What Child Processes Were Spawned?

Example:

```text
powershell.exe
    └── certutil.exe
```

Child processes often reveal attacker objectives.

---

## Common Normal Process Relationships

### User Launches Chrome

```text
explorer.exe
    └── chrome.exe
```

### User Opens Notepad

```text
explorer.exe
    └── notepad.exe
```

### Windows Services

```text
services.exe
    └── svchost.exe
```

These process relationships are generally expected.

---

## Common Suspicious Process Relationships

### Word Launching PowerShell

```text
winword.exe
    └── powershell.exe
```

Potential phishing or macro-based attack.

---

### Outlook Launching PowerShell

```text
outlook.exe
    └── powershell.exe
```

Potential malicious attachment execution.

---

### PowerShell Launching Certutil

```text
powershell.exe
    └── certutil.exe
```

Potential file download or payload execution.

---

## Highly Suspicious Process Relationships

### LSASS Launching Command Prompt

```text
lsass.exe
    └── cmd.exe
```

Abnormal behavior requiring immediate investigation.

---

### LSASS Launching PowerShell

```text
lsass.exe
    └── powershell.exe
```

Potential compromise or malicious process injection.

---

## Example Attack Chain

```text
outlook.exe
    └── winword.exe
          └── powershell.exe
                └── certutil.exe
                      └── payload.exe
```

Possible attack progression:

1. Phishing email received
2. Malicious document opened
3. PowerShell executed
4. Payload downloaded using Certutil
5. Malware executed

This process tree allows SOC analysts to reconstruct the attack timeline and identify the root cause.

---

## SOC Analyst Investigation Checklist

When investigating a process alert:

```text
1. Process Name
2. Parent Process
3. Command Line
4. File Path
5. User Context
6. Child Processes
7. Network Connections
8. File Hash
9. Digital Signature
10. Historical Activity
```

Following this methodology helps analysts distinguish legitimate activity from potential threats and improves incident response effectiveness.

---

## Key Takeaways

* Process analysis is a foundational SOC skill.
* Parent-child relationships provide critical attack context.
* PowerShell activity should always be reviewed carefully.
* Command-line arguments often reveal attacker intent.
* Legitimate process names do not guarantee legitimate behavior.
* Understanding process trees enables effective threat investigations.

Mastering Windows process analysis significantly improves a SOC analyst's ability to detect, investigate, and respond to security incidents.
