# Linux Log Analysis: Failed SSH Login Investigation

## Objective

To investigate failed SSH authentication attempts recorded in Ubuntu's authentication logs and determine the source, target account, and nature of the activity.

## Log Source

* File: `/var/log/auth.log`
* Service: SSH (`sshd`)
* Host: `lordson-VirtualBox`

## Event Details

### Sample Log Entry

```text
2026-06-03T16:31:25.648390+05:30 lordson-VirtualBox sshd[3870]: Failed password for invalid user fakeuser from 127.0.0.1 port 43714 ssh2
```

### Extracted Information

| Field      | Value                 |
| ---------- | --------------------- |
| Timestamp  | 2026-06-03 16:31:25   |
| Hostname   | lordson-VirtualBox    |
| Process    | sshd                  |
| Event Type | Failed Authentication |
| Username   | fakeuser              |
| Source IP  | 127.0.0.1             |
| Protocol   | SSH2                  |

## Investigation Findings

Multiple failed SSH authentication attempts were observed against the non-existent account `fakeuser`. The attempts originated from IP address `127.0.0.1` (localhost), indicating that the activity was generated from the local machine.

A total of **9 failed login attempts** were recorded within approximately **90 seconds**. The repeated authentication failures indicate behavior consistent with password guessing or brute-force activity.

Since the source IP was localhost and the activity was intentionally generated in a lab environment, the event was determined to be a controlled simulation rather than a malicious attack.

## Indicators of Compromise (IOCs)

| IOC Type    | Value              |
| ----------- | ------------------ |
| Source IP   | 127.0.0.1          |
| Target User | fakeuser           |
| Service     | SSH                |
| Host        | lordson-VirtualBox |

## MITRE ATT&CK Mapping

| Technique   | ID    |
| ----------- | ----- |
| Brute Force | T1110 |

## Analysis

The SSH daemon (`sshd`) rejected authentication attempts because the specified user account did not exist on the system. Repeated failed authentication events are commonly monitored by Security Operations Centers (SOCs) as they may indicate:

* Credential stuffing attempts
* Password spraying
* Brute-force attacks
* Username enumeration

In production environments, similar activity originating from external IP addresses should be investigated to determine whether unauthorized access attempts are occurring.

## Recommendations

1. Monitor authentication logs for repeated failed login attempts.
2. Implement account lockout or rate-limiting controls where appropriate.
3. Restrict SSH access to trusted IP addresses.
4. Use strong passwords and multi-factor authentication (MFA).
5. Configure alerting for excessive failed authentication events.

## Conclusion

The investigation identified multiple failed SSH login attempts targeting a non-existent user account. The activity was successfully detected through analysis of Linux authentication logs and demonstrated the process of identifying source IPs, target accounts, authentication failures, and potential brute-force indicators commonly investigated by SOC analysts.
