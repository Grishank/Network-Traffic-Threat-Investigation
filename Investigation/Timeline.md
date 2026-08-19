# Investigation Timeline

This timeline documents the major network events observed during the analysis of the `dns-remoteshell.pcap` capture.

> **Note:** Times below are capture-relative timestamps displayed by Wireshark and are not wall-clock UTC timestamps.

---

## Network Investigation Timeline

| Time | Event | Evidence | Analysis |
|---:|---|---|---|
| `0.017208` | DNS query observed | Packet 2 | DNS traffic was identified between the observed hosts. |
| `0.019881` | Reverse DNS query observed | Packet 4 | A PTR query was observed as part of the DNS communication. |
| `2.629154` | DNS query observed | Packet 7 | Additional DNS communication was identified in the capture. |
| `2.646936` | DNS query observed | Packet 8 | Continued DNS activity was observed between the hosts. |
| `2.648555` | DNS response observed | Packet 9 | A DNS response was returned to the requesting host. |
| `25.493358` | TCP connection initiated | Packet 14 | A TCP SYN was observed from source port `1396` to destination port `53`. |
| `25.494894` | TCP retransmission observed | Packet 15 | The connection attempt included retransmitted TCP traffic. |
| `25.496543` | TCP response observed | Packet 17 | A SYN/ACK response was observed from port `53`. |
| `25.497466` | TCP ACK observed | Packet 18 | The TCP connection was established. |
| `25.555046` | TCP payload observed | Packet 21 | TCP payload data was transmitted over the established connection. |
| `25.556102` | TCP retransmission with payload | Packet 22 | Additional payload was observed and later examined through TCP stream reconstruction. |
| `25.698332` | TCP response observed | Packet 23 | The remote host acknowledged the transmitted data. |
| `25.716239` | TCP retransmission observed | Packet 24 | Additional retransmitted traffic was identified. |
| `27.850155` | TCP payload observed | Packet 25 | Further application data was transmitted through the connection. |
| `27.867122` | TCP retransmission observed | Packet 26 | Additional retransmitted payload was identified. |
| `27.885345` | TCP payload observed | Packet 27 | Continued application-layer data was observed. |
| `28.018045` | TCP payload observed | Packet 29 | Additional data was exchanged between the hosts. |
| `28.021399` | TCP payload observed | Packet 30 | Further communication continued over the same TCP session. |
| `28.202157` | TCP payload observed | Packet 31 | Additional application data was transmitted. |
| `28.218544` | TCP payload observed | Packet 34 | Continued communication was observed. |
| `30.783984` | TCP payload observed | Packet 35 | Further data was transmitted through the session. |
| `30.800470` | TCP reset observed | Packet 36 | The connection began terminating with TCP reset activity. |
| `30.804470` | TCP reset observed | Packet 37 | The remote side also returned a TCP reset. |

---

## TCP Stream Investigation

The TCP session was reconstructed using Wireshark's **Follow → TCP Stream** functionality.

The reconstructed stream contained readable Windows command-shell activity.

Observed content included:

- Windows command prompt information
- `dir` command execution
- Directory listing output
- Windows system information
- File and directory names
- `exit` command

The presence of an interactive command-shell session within the analyzed TCP communication was considered suspicious.

---

## Key Timeline Observations

### 1. DNS Activity

The capture initially showed several DNS queries and responses.

This established DNS-related communication within the capture before the investigation shifted toward the suspicious TCP session.

### 2. TCP Communication Over Port 53

At approximately `25.49` seconds into the capture, TCP communication involving port `53` was observed.

The traffic included TCP payload data rather than behaving like a typical DNS exchange.

This justified further investigation of the TCP conversation.

### 3. Payload Analysis

The TCP payload was examined using Wireshark.

Following the TCP stream revealed readable command-line information, including the execution of:

```text
dir
```

The command produced directory listing information from the remote Windows system.

### 4. Command Shell Termination

The reconstructed stream also showed:

```text
exit
```

indicating termination of the observed command-shell session.

---

## Investigation Flow

```text
DNS Traffic Observed
        ↓
TCP Port 53 Identified
        ↓
TCP Session Established
        ↓
TCP Payload Observed
        ↓
Follow TCP Stream
        ↓
Windows Command Shell Identified
        ↓
"dir" Command Observed
        ↓
Directory Listing Identified
        ↓
"exit" Command Observed
        ↓
Suspicious Remote Shell Activity
```

---

## Investigation Conclusion

The timeline demonstrates how the investigation progressed from general network traffic analysis to identification of a suspicious TCP session and finally to application-level command analysis.

The most significant finding was the presence of readable Windows command-shell activity within the TCP stream associated with port `53`.

This behavior would warrant further investigation in a real SOC environment, including analysis of the communicating hosts, related network traffic, endpoint telemetry, and the origin of the connection.
