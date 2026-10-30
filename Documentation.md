
## 1. Introduction to Wireshark

Wireshark is a free, open-source network protocol analyzer used to capture, inspect, and analyze network traffic in real time or from previously recorded capture files.

### Main Use Cases

| Application | Description |
|---|---|
| Network Troubleshooting | Identifying latency, packet loss, retransmissions, and connectivity issues. |
| Security Analysis | Investigating suspicious communications and potentially malicious activity. |
| Protocol Analysis | Understanding how network protocols operate and interact. |
| Incident Investigation | Examining captured traffic to reconstruct network events. |
| Performance Monitoring | Identifying bandwidth consumption and unusual traffic patterns. |

### Who Uses Wireshark?

- **Network Engineers:** Troubleshoot connectivity problems and misconfigured devices.
- **SOC Analysts:** Investigate security alerts and suspicious communications.
- **Penetration Testers:** Analyze network behavior during authorized security assessments.
- **System Administrators:** Monitor network communications and diagnose infrastructure issues.
- **Developers and Students:** Understand network protocols and application-level communication.

---

## 2. Installation and Setup

Wireshark is available for Windows, macOS, and Linux.

**Official website:** https://www.wireshark.org/

### Installation Notes

- **Windows:** Install Npcap when prompted. It enables packet capture from network interfaces.
- **Kali Linux:** Wireshark may already be installed or can be obtained through the distribution's package repositories.

---

## 3. Capturing Network Traffic

Follow these steps to capture network traffic:

1. Launch Wireshark.
2. Select the active network interface (Wi-Fi or Ethernet).
3. Start capturing using the blue shark-fin button.
4. Generate network activity, such as browsing a website or executing a ping command.
5. Stop the capture using the red square button.
6. Analyze the captured packets.

> **Note:** Wireshark captures traffic visible to the selected network interface, subject to network configuration and capture permissions.

---

## 4. Understanding the Wireshark Interface

The main interface consists of three sections:

| Component | Function |
|---|---|
| **Packet List** | Displays captured packets, timestamps, source and destination addresses, and protocols. |
| **Packet Details** | Displays the protocol layers and fields of the selected packet. |
| **Packet Bytes** | Displays raw packet data in hexadecimal and ASCII formats when readable. |

Selecting a packet allows you to inspect its protocol layers and examine the corresponding raw data.

---

## 5. Wireshark Filters

Wireshark provides two main filtering mechanisms.

### 5.1 Capture Filters

Capture filters are configured before starting a capture. They determine which packets are collected and saved.

**Example:**

```text
host 192.168.1.5
```

Captures traffic to or from the specified IP address.

### 5.2 Display Filters

Display filters are applied after packets have been captured. They control which packets are displayed without deleting the remaining captured traffic.

**Example:**

```text
ip.addr == 192.168.1.5
```

Displays packets sent to or received from the specified IP address.

### 5.3 Common Display Filters

| Filter | Purpose |
|---|---|
| `ip.addr == 192.168.4.3` | Traffic involving a specific IP address. |
| `tcp.port == 80` | TCP traffic involving port 80. |
| `http` | HTTP packets. |
| `dns` | DNS packets. |
| `icmp` | ICMP traffic, including ping messages. |
| `tcp.flags.syn == 1` | TCP packets with the SYN flag set. |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | TCP connection initiation packets. |
| `tcp.flags.reset == 1` | TCP reset packets. |
| `ftp` | FTP traffic. |
| `sip` | SIP signaling packets. |
| `rtp` | RTP media packets. |
| `tls.handshake.type == 1` | TLS Client Hello messages. |
| `dns.flags.response == 0` | DNS queries. |
| `tcp.dstport == 80` | TCP packets destined for port 80. |

---

## 6. Common Network Protocols

Understanding network protocols is essential for interpreting captured traffic.

| Protocol | Function |
|---|---|
| **TCP** | Reliable, connection-oriented data transmission using mechanisms such as the three-way handshake. |
| **DNS** | Resolves domain names into IP addresses. |
| **HTTP** | Transfers web content and application data. Unencrypted HTTP may expose transmitted information. |
| **ARP** | Resolves IPv4 addresses into MAC addresses on a local network. |
| **ICMP** | Carries diagnostic messages and network error information, including ping requests and replies. |
| **FTP** | Transfers files between a client and a server. Traditional FTP does not encrypt authentication credentials. |
| **SIP** | Establishes, manages, and terminates multimedia communication sessions, including VoIP calls. |
| **RTP** | Transports real-time audio and video data. |
| **TLS** | Provides encryption and authentication for network communications. |

---

## 7. Useful Wireshark Analysis Tools

Wireshark includes several features that simplify network investigations.

### 7.1 Follow TCP Stream

**Location:** `Analyze > Follow > TCP Stream`

- Reconstructs a TCP conversation.
- Displays exchanged data in a readable format.
- Useful for inspecting HTTP requests, responses, and unencrypted application communications.

### 7.2 Export Objects

**Location:** `File > Export Objects > HTTP`

- Extracts supported objects transferred through HTTP.
- Can be used to recover files and images from captured traffic.

### 7.3 Expert Information

**Location:** `Analyze > Expert Information`

- Highlights potential network problems and protocol anomalies.
- Helps identify retransmissions and connection-related issues.

### 7.4 I/O Graphs

**Location:** `Statistics > I/O Graphs`

- Visualizes packet activity over time.
- Helps identify traffic spikes and unusual network patterns.

---

## 8. Practical Network Investigations

This section documents hands-on investigations performed using Wireshark capture files.

### 8.1 HTTP Traffic and Intranet Investigation

**Objective:** Analyze HTTP communications and identify resources requested by clients.

**Investigation Steps:**

- Apply the `http` display filter.
- Examine HTTP GET requests to identify requested pages and files.
- Inspect HTTP responses and their associated status codes.
- Use Follow TCP Stream to reconstruct client-server communication.
- Examine response bodies to identify readable content and potentially exposed information.
- Investigate requested resources such as `/help.html`, `/index`, `/staff`, and `/download/reports.txt`.

<details>
<summary><b>Key Learning</b></summary>

HTTP packet analysis can reveal requested resources, server responses, and information transmitted without encryption.
</details>

### 8.2 HTTP Authentication Analysis

**Objective:** Investigate credentials transmitted through an HTTP login form.

**Investigation Steps:**

- Identify HTTP POST requests associated with authentication.
- Inspect the request method and destination URI.
- Expand HTTP form data to examine submitted parameters.
- Identify username and password fields when transmitted in readable form.
- Examine the server response to understand the authentication result.

<details>
<summary><b>Key Learning</b></summary>

Credentials submitted through unencrypted HTTP forms may be exposed to anyone capable of capturing the relevant traffic.
</details>

### 8.3 FTP Authentication and Brute-Force Investigation

**Objective:** Analyze repeated FTP authentication attempts and identify suspicious login behavior.

**Investigation Steps:**

- Apply the `ftp` display filter.
- Identify FTP authentication commands and server responses.
- Examine repeated login attempts involving the same username.
- Look for unsuccessful authentication responses, such as `530 Login incorrect`.
- Identify the FTP server by inspecting packet source and destination addresses.
- Compare authentication attempts to understand the activity pattern.

<details>
<summary><b>Key Learning</b></summary>

Numerous authentication attempts against the same account may indicate a brute-force attack. A failed login response alone does not establish whether a later attempt succeeded; successful authentication must be confirmed through the corresponding server response.
</details>

### 8.4 VoIP Communication Analysis

**Objective:** Investigate a voice call by analyzing signaling and media traffic.

**Investigation Steps:**

- Apply the `sip` filter to identify call signaling.
- Locate the SIP INVITE message, which initiates call setup.
- Inspect SIP headers and message details to identify the calling extension.
- Apply the `rtp` filter to identify packets carrying voice media.
- Inspect the RTP payload type to determine the media encoding.
- Use available RTP playback functionality to listen to captured audio when supported.

<details>
<summary><b>Key Learning</b></summary>

SIP manages call signaling, whereas RTP transports the actual voice data.
</details>

### 8.5 DNS Traffic Analysis

**Objective:** Examine DNS queries and measure DNS activity within a capture.

**Investigation Steps:**

- Apply the `dns` filter to isolate DNS traffic.
- Identify DNS queries and their corresponding responses.
- Inspect queried domain names and requested record types.
- Use `dns.flags.response == 0` to display DNS queries only.
- Examine retransmissions when counting DNS packets.
- Use `dns.qry.name == "example.com" && dns.flags.response == 0` to isolate queries for a specific domain.

<details>
<summary><b>Key Learning</b></summary>

DNS analysis helps identify domain resolution activity, communication patterns, and potentially suspicious queries.
</details>

### 8.6 TCP Traffic and Connection Analysis

**Objective:** Examine TCP communications, identify connection behavior, and analyze data exchanged between hosts.

**Investigation Steps:**

- Apply TCP filters to isolate connection-oriented traffic.
- Identify connection initiation packets using `tcp.flags.syn == 1 && tcp.flags.ack == 0`.
- Examine SYN-ACK packets to identify responses to connection requests.
- Inspect TCP RST packets to identify connection resets.
- Use the Conversations window to identify communicating hosts and compare transferred data.
- Examine source and destination ports to identify the services involved.

<details>
<summary><b>Key Learning</b></summary>

TCP flags and conversation statistics provide useful information about connection establishment, termination, and data exchange.
</details>

### 8.7 TCP Port Scan Investigation

**Objective:** Identify potential port-scanning activity through repeated TCP connection attempts.

**Investigation Steps:**

- Identify hosts generating repeated SYN packets.
- Compare source and destination IP addresses.
- Examine destination ports to identify targeted services.
- Count distinct destination ports contacted by a host.
- Inspect SYN-ACK responses to identify ports accepting connection attempts.
- Inspect RST responses to identify rejected or closed connection attempts.

<details>
<summary><b>Key Learning</b></summary>

A single source sending connection attempts to multiple destination ports in a short period may indicate port-scanning activity. The traffic pattern and responses should be examined before classifying the activity.
</details>

### 8.8 Network Conversations and Data Transfer

**Objective:** Identify communication patterns and determine which hosts exchange the largest amount of data.

**Investigation Steps:**

- Open `Statistics > Conversations`.
- Select the appropriate protocol tab, such as TCP or IPv4.
- Compare communicating IP addresses.
- Examine packet counts and transferred bytes.
- Identify the endpoints responsible for the largest data exchanges.
- Convert byte values into kilobytes when required.

<details>
<summary><b>Key Learning</b></summary>

Conversation statistics provide a practical overview of network communication volume and help identify high-bandwidth connections.
</details>

### 8.9 Intranet Resource and File Investigation

**Objective:** Investigate HTTP requests to locate specific resources and files within an internal web environment.

**Investigation Steps:**

- Filter HTTP requests.
- Examine GET requests and their requested paths.
- Identify resources such as HTML pages and downloadable text files.
- Inspect associated responses.
- Follow the relevant TCP stream to examine content exchanged between the client and server.

<details>
<summary><b>Key Learning</b></summary>

HTTP request paths and response contents can help reconstruct how users interact with internal web resources.
</details>

---

## 9. Wireshark in Cybersecurity

Wireshark is particularly useful for identifying and investigating suspicious network behavior.

### 9.1 Port Scanning

- A host sends connection attempts to multiple ports on another system.
- Repeated SYN packets can indicate scanning activity.
- Destination ports and response flags help characterize the behavior.

### 9.2 Cleartext Credential Exposure

- HTTP, FTP, and Telnet may transmit sensitive information without encryption.
- Captured traffic can expose usernames, passwords, and application data.
- Encrypted protocols such as HTTPS and SSH reduce this exposure.

### 9.3 Suspicious Network Communications

- Unexpected connections to unfamiliar external destinations.
- Repeated communications at regular intervals, potentially indicating beaconing.
- Unusually long or suspicious DNS queries.
- Unexpected traffic volume or unusual communication patterns.

> **Security Note:** Wireshark provides visibility into network traffic, but suspicious patterns must be investigated in context before being classified as malicious.

---

## 10. Skills Developed

Through these practical exercises, the following technical skills were developed:

- Network packet capture and inspection.
- TCP/IP protocol analysis.
- HTTP request and response investigation.
- Authentication traffic analysis.
- FTP login-attempt investigation.
- SIP and RTP analysis for VoIP communications.
- DNS query inspection.
- TCP flags and connection analysis.
- Identification of potential port-scanning activity.
- Network conversations and bandwidth analysis.
- Wireshark display-filter usage.
- Investigation of potentially exposed information and suspicious traffic.

---

## 11. Responsible Use

Network traffic analysis must be performed only on networks and systems that you own or have explicit authorization to inspect.

The investigations documented in this project are intended for educational purposes and authorized cybersecurity practice.

---

---

#---

## 12. NetworkWalks Internship – Labs & Assessments

As part of the **NetworkWalks Online Internship**, I successfully completed six practical Wireshark labs and passed both knowledge assessments.

### 12.1 Practical Labs

<badge color="success">6/6 Labs Completed</badge>

| Lab | Status |
|---|---|
| Lab 1 | ✅ Completed |
| Lab 2 | ✅ Completed |
| Lab 3 | ✅ Completed |
| Lab 4 | ✅ Completed |
| Lab 5 | ✅ Completed |
| Lab 6 | ✅ Completed |

<img width="601" height="669" alt="WhatsApp Image 2026-10-30 at 19 52 07" src="https://github.com/user-attachments/assets/d2fa974b-2b20-45e7-b1fe-1d3fb003989e" />

<img width="1016" height="1600" alt="WhatsApp Image 2026-10-30 at 19 49 38" src="https://github.com/user-attachments/assets/946a30d3-2419-4c3d-b5bf-dfc08953bfd7" />
<img width="1016" height="1600" alt="WhatsApp Image 2026-10-30 at 19 50 13" src="https://github.com/user-attachments/assets/36a4ffe9-0bf0-4acd-83c5-79ca40dda3eb" />
<img width="1016" height="1600" alt="WhatsApp Image 2026-10-30 at 19 50 21" src="https://github.com/user-attachments/assets/06aa9342-55c4-4ed0-9431-874ecb279316" />
<img width="1016" height="1600" alt="WhatsApp Image 2026-10-30 at 19 50 32" src="https://github.com/user-attachments/assets/308256ea-ba66-47e2-895e-b24a8d8608fc" />
<img width="1016" height="1600" alt="WhatsApp Image 2026-10-30 at 19 50 43" src="https://github.com/user-attachments/assets/7f5d83cd-1125-4802-a0f5-a80e9b1db983" />



### 12.2 Knowledge Assessments

<badge color="success">2/2 Quizzes Passed</badge>

| Assessment | Result |
|---|---|
| Easy-Level Quiz | ✅ Passed |
| Hard-Level Quiz | ✅ Passed |

### 12.3 Completion Evidence

The following screenshots provide evidence of the completed practical labs and successfully passed assessments.

**Practical Labs**

![Lab Completion Evidence](screenshots/labs-completion.png)

**Easy-Level Quiz – Passed**
<img width="803" height="330" alt="image" src="https://github.com/user-attachments/assets/a9f845ee-0892-4415-9809-bd58e7e95a24" />

![Easy Quiz](screenshots/easy-quiz.png)

**Hard-Level Quiz – Passed**
<img width="959" height="400" alt="image" src="https://github.com/user-attachments/assets/426f3d36-26f5-4fcf-b256-2c8f3890276d" />

![Hard Quiz](screenshots/hard-quiz.png)

---

<p align="center">
  **Successfully Completed – NetworkWalks Online Internship**
</p>

<p align="center">
  **End of Documentation**
</p>
