# Incident Summary

## Incident Overview

A suspicious network communication session was investigated using a provided PCAP file and Wireshark.

The investigation focused on identifying unusual traffic patterns, analyzing DNS-related communication, examining TCP traffic over port 53, and inspecting the contents of the TCP session.

The analysis revealed a suspicious remote shell-like communication channel in which command-line activity and directory listing information were transmitted between two hosts.

---

## Investigation Objective

The investigation was performed to:

- Analyze the provided network capture
- Identify suspicious DNS and TCP communication
- Examine traffic involving TCP port 53
- Identify the communicating hosts
- Inspect packet payloads
- Reconstruct the TCP conversation
- Identify command-line activity within the network stream
- Map observed behavior to the MITRE ATT&CK framework
- Determine whether the traffic represented suspicious activity

---

## Evidence Source

| Component | Details |
|---|---|
| Evidence Type | Network Packet Capture |
| PCAP File | `dns-remoteshell.pcap` |
| Analysis Tool | Wireshark |
| Primary Protocols | DNS / TCP |
| Suspicious Port | TCP/53 |
| Analysis Method | Packet and TCP Stream Analysis |
| Environment | Controlled Security Investigation Lab |

---

## Initial Traffic Analysis

The PCAP was opened in Wireshark and initially examined using protocol-based display filters.

DNS traffic was identified between the observed hosts, including DNS queries and responses.

The investigation then focused on traffic involving **TCP port 53**, which revealed communication that required further investigation.

The use of TCP port 53 was particularly notable because the traffic contained TCP payload data that did not resemble normal DNS communication.

---

## Suspicious TCP Communication

Traffic between the observed hosts included TCP communication using:

- Source port: `53`
- Destination port: `1396`

Multiple packets contained TCP payload data and retransmissions.

The traffic was investigated further by following the TCP stream associated with the communication.

---

## TCP Stream Analysis

The reconstructed TCP stream revealed readable command-line activity.

The stream contained:

- Windows command prompt output
- `dir` command execution
- Directory listing information
- Windows system information
- File and directory names
- A command prompt session terminating with `exit`

The presence of an interactive Windows command shell within traffic using TCP port 53 was considered highly suspicious.

---

## Key Findings

The investigation established the following:

1. DNS traffic was present within the PCAP.
2. TCP communication using port `53` was identified.
3. The TCP traffic contained application data that did not resemble normal DNS traffic.
4. Following the TCP stream revealed an interactive Windows command-shell session.
5. The stream contained `dir` command output and directory information.
6. The observed activity demonstrated remote command execution and file/directory discovery behavior.
7. The activity was mapped to relevant MITRE ATT&CK techniques.

---

## MITRE ATT&CK Mapping

The observed behavior was mapped to the following techniques:

| Technique | ID | Observed Activity |
|---|---|---|
| Command and Scripting Interpreter: Windows Command Shell | T1059.003 | Windows command-line activity observed in TCP stream |
| File and Directory Discovery | T1083 | `dir` command and directory listing observed |
| Application Layer Protocol: DNS | T1071.004 | Suspicious communication observed over DNS-related port 53 |

> **Note:** The TCP/53 traffic was treated as suspicious network communication rather than normal DNS activity because the reconstructed stream contained Windows command-shell data.

---

## Investigation Outcome

The network activity was classified as **suspicious remote shell-like communication** within the analyzed PCAP.

The investigation demonstrated how a SOC analyst can move from high-level packet inspection to protocol filtering, TCP stream reconstruction, payload analysis, and behavioral classification.

The evidence was derived entirely from the provided PCAP and was analyzed as part of a controlled security investigation.

---

## Investigation Flow

```text
PCAP Collection
      ↓
Wireshark Analysis
      ↓
Protocol Filtering
      ↓
DNS / TCP Traffic Identification
      ↓
TCP Port 53 Investigation
      ↓
TCP Stream Reconstruction
      ↓
Payload Analysis
      ↓
Command Identification
      ↓
MITRE ATT&CK Mapping
      ↓
SOC Analyst Assessment
