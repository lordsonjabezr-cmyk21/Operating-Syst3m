# Linux Security Investigation: Unauthorized Account Access and Privilege Escalation

## Incident Summary

A newly created user account named **attacker** was identified on the system. The account was subsequently assigned a password, added to the privileged sudo group, used to access the system, and then leveraged to obtain root privileges.

The investigation focused on identifying the complete attack chain from account creation through privilege escalation.

---

# Objective

Investigate suspicious account creation and privilege escalation activity on a Linux endpoint.

---

# Evidence Collection

## Account Creation

Command executed:

```bash
sudo useradd attacker
```

Log Evidence:

```text
useradd[3929]: new group: name=attacker, GID=1002
useradd[3929]: new user: name=attacker, UID=1002
```

### Analysis

A new local account named **attacker** was created.

---

## Password Assignment

Command executed:

```bash
sudo passwd attacker
```

Log Evidence:

```text
passwd[3976]: password changed for attacker
```

### Analysis

A password was assigned to the newly created account, making it usable for authentication.

---

## Privilege Assignment

Command executed:

```bash
sudo usermod -aG sudo attacker
```

Log Evidence:

```text
usermod[3981]: add 'attacker' to group 'sudo'
```

### Analysis

The account was granted administrative privileges through membership in the sudo group.

---

## Failed Authentication Attempt

Log Evidence:

```text
su: pam_unix(su:auth): authentication failure
user=attacker
```

### Analysis

An unsuccessful attempt was made to authenticate as attacker.

This may indicate:

* Incorrect password usage
* User error
* Credential guessing activity

---

## Successful User Access

Log Evidence:

```text
su[7165]: pam_unix(su:session): session opened for user attacker(uid=1002)
```

Timestamp:

```text
2026-06-04 12:09:03
```

### Analysis

The attacker account successfully authenticated and obtained an interactive session.

---

## Privilege Escalation

Log Evidence:

```text
sudo: attacker : USER=root ; COMMAND=/usr/bin/su -
```

Timestamp:

```text
2026-06-04 12:10:08
```

### Analysis

The attacker account leveraged sudo permissions to execute commands as root.

---

## Root Session Established

Log Evidence:

```text
sudo: pam_unix(sudo:session): session opened for user root(uid=0)
```

and

```text
su[7173]: pam_unix(su-l:session): session opened for user root(uid=0)
```

### Analysis

Privilege escalation was successful.

The attacker account obtained full administrative access.

---

## Session Termination

Log Evidence:

```text
su[7173]: pam_unix(su-l:session): session closed for user root
```

```text
su[7165]: pam_unix(su:session): session closed for user attacker
```

### Analysis

Both root and attacker sessions were properly terminated.

---

# Timeline

| Time     | Event                        |
| -------- | ---------------------------- |
| 11:39:59 | Account attacker created     |
| 11:41:35 | Password assigned            |
| 11:42:04 | Added to sudo group          |
| 12:08:51 | Failed authentication        |
| 12:09:03 | Successful login as attacker |
| 12:10:08 | Privilege escalation to root |
| 12:11:30 | Root session closed          |
| 12:11:33 | Attacker session closed      |

---

# MITRE ATT&CK Mapping

### Create Account

T1136

Creation of a new local user account.

### Account Manipulation

T1098

Modification of account permissions by adding the account to the sudo group.

### Valid Accounts

T1078

Use of legitimate credentials to gain access.

### Abuse Elevation Control Mechanism

T1548

Privilege escalation through sudo.

---

# Indicators of Compromise (IOCs)

### User Account

```text
attacker
```

### UID

```text
1002
```

### Privileged Group Membership

```text
sudo
```

### Root Escalation Command

```bash
sudo su -
```

---

# Risk Assessment

Severity: High

The account obtained administrative privileges and successfully escalated to root.

An attacker with root access can:

* Modify system files
* Create persistence mechanisms
* Access sensitive credentials
* Disable security controls
* Execute arbitrary commands

---

# Conclusion

The investigation identified the creation of a new local account named attacker, assignment of credentials, privilege escalation through sudo group membership, and successful acquisition of root-level access.

The activity demonstrates a complete attack chain involving account creation, privilege assignment, valid account abuse, and privilege escalation. Immediate containment actions would include disabling the account, removing privileged access, and reviewing all actions performed during the root session.
