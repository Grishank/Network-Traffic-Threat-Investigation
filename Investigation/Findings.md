# Investigation Findings

## Executive Summary

A suspicious network communication session was investigated using the `dns-remoteshell.pcap` packet capture and Wireshark.

The investigation began with analysis of DNS traffic and progressed to examination of TCP communication involving port `53`.

The TCP session contained readable Windows command-shell activity. Following the TCP stream revealed command-line interaction and directory listing information, indicating suspicious remote command execution and file/directory discovery behavior.

The observed activity was mapped to relevant MITRE ATT&CK techniques and assessed as suspicious network behavior within the context of the provided capture.

---

## Findings

### 1. DNS Traffic Observed

The initial analysis identified multiple DNS queries and responses within the capture.

Observed traffic included:

- DNS queries
- Reverse DNS/PTR query
- DNS responses
- Communication between the observed hosts

DNS traffic by itself was not considered malicious. It provided the initial context for further packet analysis.

**Evidence:**
- Wireshark DNS display filter
- Packets `2`, `4`, `7`, `8`, `9`
- PCAP: `dns-remoteshell.pcap`

---

### 2. Suspicious TCP Communication Over Port 53

Further analysis identified TCP communication involving destination/source port `53`.

The observed session included:

- TCP SYN/SYN-ACK/ACK activity
- TCP payload data
- Retransmissions
- TCP reset activity
- Communication between the observed hosts

The presence of application data within a TCP session using port `53` was unusual and warranted deeper investigation.

**Evidence:**
- TCP port `53` display filter
- Packets `14–38`
- TCP payload observed in multiple packets

---

### 3. TCP Stream Reconstruction

The suspicious TCP session was reconstructed using Wireshark's:

**Follow → TCP Stream**

The reconstructed stream revealed readable Windows command-shell content.

The stream included:

- Windows command prompt information
- Command execution
- Directory listing output
- Windows system information
- File and directory names
- Session termination using `exit`

This provided application-level evidence that could not be determined from packet headers alone.

**Evidence:**
- Wireshark Follow TCP Stream
- TCP stream associated with the suspicious session

---

### 4. Windows Command Shell Activity

The reconstructed TCP stream contained Windows command-line activity.

The following command was observed:

```text
dir
```

The command generated a directory listing containing files and directories from the remote Windows system.

This activity is consistent with **File and Directory Discovery** behavior.

**MITRE ATT&CK:** `T1083 — File and Directory Discovery`

**Evidence:**
- `dir` command visible in reconstructed TCP stream
- Directory listing output
- TCP payload analysis

---

### 5. Command Shell Session

The TCP stream contained evidence of an interactive Windows command-shell session.

The stream began with Windows command prompt information and contained command execution followed by:

```text
exit
```

The observed command-line behavior is consistent with the use of the Windows command shell.

**MITRE ATT&CK:** `T1059.003 — Windows Command Shell`

**Evidence:**
- Windows command prompt output
- Command-line activity within TCP stream
- `exit` command

---

### 6. Suspicious Use of Port 53

The communication used TCP port `53` while carrying readable command-shell data.

Port `53` is commonly associated with DNS services. However, the reconstructed TCP stream did not contain normal DNS query/response content. Instead, it contained Windows command-shell activity.

This mismatch between the expected protocol/service and the observed payload made the communication suspicious.

In a real SOC environment, this type of behavior could indicate:

- Protocol abuse
- Covert communication
- Unauthorized remote access
- Command-and-control activity

Additional endpoint and network telemetry would be required to determine the exact nature of the activity.

---

## MITRE ATT&CK Mapping

| Technique | ID | Observed Activity |
|---|---|---|
| Command and Scripting Interpreter: Windows Command Shell | `T1059.003` | Windows command-shell activity observed in the TCP stream |
| File and Directory Discovery | `T1083` | `dir` command and directory listing observed |
| Application Layer Protocol: DNS | `T1071.004` | Suspicious communication associated with DNS-related port `53` |

> **Assessment:** The presence of TCP port `53` alone does not prove the use of DNS as an application-layer command-and-control protocol. The mapping to `T1071.004` is therefore treated as contextual and should be validated with additional protocol analysis.

---

## Evidence Summary

| Evidence | Observation | Significance |
|---|---|---|
| DNS traffic | Multiple DNS queries and responses | Established initial network activity |
| TCP port `53` | TCP session using port `53` | Unusual communication requiring investigation |
| TCP payload | Readable application data | Allowed deeper content analysis |
| Follow TCP Stream | Windows command-shell content | Strong evidence of interactive command activity |
| `dir` command | Directory listing generated | Indicates file/directory discovery |
| `exit` command | Shell session termination | Indicates completion of the observed command session |

---

## Analyst Assessment

The analyzed traffic contains multiple indicators of suspicious network activity.

The most significant finding was the presence of readable Windows command-shell activity within a TCP session involving port `53`.

The `dir` command demonstrated file and directory discovery activity, while the overall TCP stream provided evidence of command-line interaction between the communicating hosts.

A real SOC investigation should not rely on the PCAP alone. Additional investigation would include:

- Identifying the systems associated with the observed IP addresses
- Reviewing endpoint process telemetry
- Checking Windows Security and Sysmon logs
- Investigating DNS server logs
- Reviewing firewall and proxy logs
- Searching for related network connections
- Checking for persistence or additional commands
- Determining the source and destination of the connection

---

## Severity Assessment

**Suggested Severity: Medium to High**

The activity warrants investigation because:

- TCP port `53` was used for non-standard application data
- Readable command-shell activity was present
- Remote directory information was accessed
- The traffic could represent unauthorized remote access or protocol abuse

The final severity in a real SOC environment would depend on asset criticality, source/destination reputation, endpoint telemetry, and confirmation of whether the activity was authorized.

---

## Investigation Conclusion

The investigation successfully demonstrated how network packet analysis can be used to move from low-level traffic inspection to application-level threat identification.

The investigation workflow was:

**PCAP Analysis → Protocol Filtering → TCP Port Analysis → Stream Reconstruction → Payload Analysis → Command Identification → MITRE Mapping → Analyst Assessment**

The observed traffic was classified as **suspicious remote shell-like communication within the analyzed PCAP**.

Because the activity was analyzed from a provided capture in a controlled investigation context, it should not be interpreted as evidence of a real compromise without additional supporting telemetry.
