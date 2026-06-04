# Linux Log Analysis: Privilege Escalation Investigation Using sudo Logs

## Objective

To investigate privileged command execution events recorded in Ubuntu authentication logs and identify administrative actions performed through sudo.

## Log Source

* File: `/var/log/auth.log`
* Service: `sudo`
* Host: `lordson-VirtualBox`

## Event Overview

The authentication logs recorded multiple instances of the user `lordson` executing commands with elevated privileges using sudo. Each command resulted in the creation of a root session, allowing administrative actions to be performed on the system.

## Sample Log Entry

2026-06-03T12:14:50 sudo: lordson : TTY=pts/0 ; PWD=/home/lordson ; USER=root ; COMMAND=/usr/bin/apt update

2026-06-03T12:14:50 sudo: pam_unix(sudo:session): session opened for user root(uid=0) by lordson(uid=1000)

## Investigation Findings

### User Performing Administrative Actions

| Field               | Value              |
| ------------------- | ------------------ |
| Username            | lordson            |
| Target Account      | root               |
| Host                | lordson-VirtualBox |
| Privilege Mechanism | sudo               |

### Commands Executed

The following privileged commands were identified:

* apt update
* apt upgrade -y
* apt install openssh-server
* apt install wireshark tshark tcpdump nmap net-tools curl wget git htop tree jq unzip
* dpkg -i splunk-10.4.0-linux-amd64.deb
* apt --fix-broken install -y
* splunk start
* journalctl -u ssh
* grep operations against authentication logs

### Session Activity

For each sudo execution:

1. User authentication occurred.
2. A root session was created.
3. Administrative command executed.
4. Root session was terminated.

Example:

Session Opened:
pam_unix(sudo:session): session opened for user root(uid=0) by lordson(uid=1000)

Session Closed:
pam_unix(sudo:session): session closed for user root

## Security Analysis

The logs indicate successful privilege escalation through authorized sudo usage.

No evidence of:

* Unauthorized sudo access
* Privilege escalation abuse
* Unknown user activity
* Failed sudo authentication attempts

was observed.

The commands executed were consistent with legitimate system administration and SOC lab setup activities.

## Indicators of Interest

| Type             | Value                                                   |
| ---------------- | ------------------------------------------------------- |
| User             | lordson                                                 |
| Elevated Account | root                                                    |
| Service          | sudo                                                    |
| Host             | lordson-VirtualBox                                      |
| Actions          | Package installation, system updates, Splunk deployment |

## MITRE ATT&CK Mapping

| Technique                         | ID    |
| --------------------------------- | ----- |
| Abuse Elevation Control Mechanism | T1548 |
| Valid Accounts                    | T1078 |

## Recommendations

1. Monitor sudo activity for unusual commands.
2. Alert on privilege escalation outside normal working hours.
3. Review failed sudo authentication attempts.
4. Implement least-privilege access controls.
5. Log and audit all administrative actions.

## Conclusion

The investigation identified multiple authorized privilege escalation events performed through sudo. Administrative actions included software installation, package management, SSH service deployment, and Splunk installation. Session creation and termination events were successfully correlated, demonstrating the ability to audit and investigate privileged activity using Linux authentication logs.
