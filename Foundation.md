Before a SOC analyst reviews alerts, logs, or malware, they must understand what an operating system does and what “normal” looks like at the OS level. Almost every alert encountered in a SOC maps back to operating system behavior in some form.

Day 1 establishes the core mental model needed to answer one critical SOC question:

Is this behavior normal for this operating system?

1. What Is an Operating System (OS)?
What problem does it solve?

Computers are powerful but unsafe to use directly. If applications communicated with hardware on their own:

A single bug could crash the entire system

One program could overwrite another program’s memory

Any malicious code could immediately gain full control

The operating system acts as a controlled manager between applications and hardware. It ensures:

Multiple programs can run safely at the same time

Hardware resources are shared fairly

Applications are isolated from each other

Security rules are enforced consistently

How does the OS implement this?

The OS provides a structured and secure way for applications to request resources instead of accessing hardware directly.

High-level flow:

An application requests a resource (CPU time, memory, file access)

The OS validates the request

The kernel performs the operation on the application’s behalf

Windows flow:
Application → Win32 API → NT Kernel → Hardware

Linux flow:
Application → glibc → Linux Kernel → Hardware

This design keeps applications simple while maintaining system stability and security.

How does this appear in logs and system behavior?

Normal OS activity generates constant observable events:

Processes being created and terminated

Memory allocation and deallocation

Files being opened, read, and written

Windows indicators:

Event ID 4688 – Process creation

Event ID 4689 – Process termination

Linux indicators:

auditd logs (execve events)

Process visibility via ps, top, htop

These events form the baseline of normal behavior for SOC analysts.

How does an attacker abuse this?

Attackers often abuse legitimate OS functionality rather than using custom malware:

Running malicious processes that mimic legitimate ones

Using built-in OS APIs for execution and persistence

Injecting code into trusted system processes

From the OS perspective, these actions may appear valid, which makes context and correlation critical.

How does SOC detect this?

SOC analysts focus on behavioral validation rather than single alerts:

Does this process make sense on this system?

Is the parent process expected?

Is the binary signed and located in a legitimate path?

Suspicious activity is not always malicious—but it should always be investigated.

2. Kernel Space vs User Space
Why was this design created?

Early systems lacked separation between applications and the core system. As a result, one faulty or malicious program could crash the entire machine.

The separation between kernel space and user space exists to provide:

System stability

Security enforcement

Damage containment

How does the OS implement this?

Modern CPUs support privilege levels.

Kernel Mode (Ring 0)

Full control over hardware

Manages memory, CPU, devices, and drivers

User Mode (Ring 3)

Restricted execution environment

No direct access to hardware or other processes

Applications must use system calls to request privileged actions from the kernel.

This design ensures:

Application crashes do not bring down the OS

Malware cannot directly control hardware

All sensitive actions are validated centrally

How does this appear in logs and system behavior?

Normal systems constantly transition between user mode and kernel mode.

Windows:

Sysmon Event ID 6 – Driver loaded

Sysmon Event ID 10 – Process access

Linux:

Kernel messages via dmesg

Audit logs for privileged operations

How does an attacker abuse this?

Attackers attempt to cross the user–kernel boundary by:

Exploiting kernel vulnerabilities

Loading vulnerable or malicious drivers

Installing rootkits to hide activity

Successful kernel-level access often means complete system compromise.

How does SOC detect this?

High-risk indicators include:

Unexpected driver installations

Unsigned or newly introduced kernel modules

User-mode processes interacting abnormally with kernel components

Kernel-level activity should be rare, explainable, and heavily monitored.

3. Types of Operating Systems (Windows & Linux)
Why do different OS types exist?

Different systems serve different purposes. A workstation, a server, and a security appliance have distinct operational and security requirements.

How is this implemented?

Windows:
>Desktop editions – user-interaction focused
>Server editions – service and network focused
>Server Core – minimal interface, reduced attack surface

Linux:

>General purpose – Ubuntu, Debian
>Server focused – RHEL, CentOS
>Security focused – Kali, Security Onion
>Minimal – Alpine

How does this appear in logs and behavior?
>Servers generate continuous service and network activity
>Workstations show user-driven and GUI-related processes
>SOC analysts must always evaluate behavior relative to the OS role.

How does an attacker abuse this?
>Exploiting services that should not be exposed
>Using built-in administrative tools (living-off-the-land)
>Taking advantage of weak or default configurations

How does SOC detect this?
>Desktop tools running on servers
>GUI processes on headless systems
>Services operating outside their expected role

Day 1 – SOC Mindset Takeaway

The operating system is the foundation of all observable behavior
Kernel and user space separation is a critical security boundary
OS type defines what “normal” looks like

A SOC analyst who understands the OS can explain alerts, not just react to them.
