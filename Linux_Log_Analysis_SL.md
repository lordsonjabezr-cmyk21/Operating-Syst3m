# Linux Log Analysis: Successful SSH Login Investigation

## Objective

To investigate a successful SSH authentication event using Ubuntu authentication logs and identify the user account, source IP address, and session activity.

## Log Source

* File: `/var/log/auth.log`
* Service: SSH (`sshd`)
* Host: `lordson-VirtualBox`

## Event Details

### Sample Log Entry

Jun 04 10:43:27 lordson-VirtualBox sshd[3358]: Accepted password for lordson from 127.0.0.1 port 47174 ssh2

### Session Event

Jun 04 10:43:27 lordson-VirtualBox sshd[3358]: pam_unix(sshd:session): session opened for user lordson(uid=1000)

## Investigation Findings

A successful SSH authentication event was observed for user account `lordson`. The login originated from IP address `127.0.0.1` (localhost) and was processed by the OpenSSH daemon (`sshd`).

Following successful authentication, a user session was created and opened, confirming that access to the system was granted.

The activity was generated intentionally within a lab environment and represents normal authorized access.

## Evidence

| Field     | Value                     |
| --------- | ------------------------- |
| User      | lordson                   |
| Source IP | 127.0.0.1                 |
| Service   | SSH                       |
| Protocol  | SSH2                      |
| Result    | Successful Authentication |
| Session   | Opened                    |

## Analysis

The SSH daemon successfully validated the user's credentials and granted access to the system. The session creation event confirms that authentication completed successfully and a user shell was established.

No indicators of unauthorized access or malicious activity were observed during the investigation.

## Recommendations

1. Monitor successful SSH logins from unfamiliar IP addresses.
2. Review authentication activity outside normal business hours.
3. Enable multi-factor authentication where possible.
4. Generate alerts for unusual login patterns.

## Conclusion

The investigation confirmed a successful SSH login by a legitimate user account from the local host. Authentication and session creation events were successfully identified and correlated through Linux authentication logs.
