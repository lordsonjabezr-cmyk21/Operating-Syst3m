# Linux Log Analysis: Persistence Through User Account Creation

## Objective

To investigate the creation of a new user account and subsequent privilege assignment using Linux authentication logs. This activity simulates a common persistence technique used by attackers after obtaining privileged access to a system.

## Log Source

* File: `/var/log/auth.log`
* Host: `lordson-VirtualBox`
* Services: `sudo`, `useradd`, `passwd`, `usermod`

## Incident Summary

A new user account named `attacker` was created by the user `lordson` using sudo privileges. Following account creation, a password was assigned and the account was added to the `sudo` group, granting administrative access.

This sequence of events represents a common persistence mechanism used by adversaries to maintain long-term access to compromised systems.

## Timeline of Events

### Account Creation

Timestamp:
2026-06-04 11:39:59

Evidence:

useradd[3929]: new user: name=attacker, UID=1002, GID=1002, home=/home/attacker, shell=/bin/sh

Associated Command:

sudo: lordson : COMMAND=/usr/sbin/useradd attacker

### Password Assignment

Timestamp:
2026-06-04 11:41:35

Evidence:

passwd[3976]: password changed for attacker

Associated Command:

sudo: lordson : COMMAND=/usr/bin/passwd attacker

### Privilege Assignment

Timestamp:
2026-06-04 11:42:04

Evidence:

usermod[3981]: add 'attacker' to group 'sudo'

Associated Command:

sudo: lordson : COMMAND=/usr/sbin/usermod -aG sudo attacker

## Evidence Collected

| Field                   | Value              |
| ----------------------- | ------------------ |
| Account Created         | attacker           |
| UID                     | 1002               |
| Home Directory          | /home/attacker     |
| Shell                   | /bin/sh            |
| Created By              | lordson            |
| Password Assigned       | Yes                |
| Sudo Privileges Granted | Yes                |
| Host                    | lordson-VirtualBox |

## Security Analysis

The logs demonstrate a complete account provisioning workflow:

1. New account created.
2. Password configured.
3. Administrative privileges granted.

In a production environment, this sequence would be highly suspicious if not associated with a documented administrative change request.

Attackers frequently create new accounts after gaining privileged access in order to:

* Maintain persistence
* Bypass password resets
* Create backup access paths
* Escalate privileges
* Evade detection

## Indicators of Compromise (IOCs)

| IOC Type         | Value              |
| ---------------- | ------------------ |
| Username         | attacker           |
| Privileged Group | sudo               |
| Creator Account  | lordson            |
| Host             | lordson-VirtualBox |

## MITRE ATT&CK Mapping

Technique: Create Account

ID: T1136

Technique: Account Manipulation

ID: T1098

Technique: Valid Accounts

ID: T1078

## Detection Opportunities

Monitor for:

* useradd execution
* passwd changes for newly created accounts
* usermod additions to privileged groups
* sudo group modifications
* creation of accounts outside approved maintenance windows

## Conclusion

The investigation identified the creation of a new local user account named `attacker`, followed by password assignment and addition to the sudo group. The activity resulted in a fully functional administrative account capable of executing privileged commands. This behavior closely mirrors persistence techniques commonly observed following successful system compromise.
