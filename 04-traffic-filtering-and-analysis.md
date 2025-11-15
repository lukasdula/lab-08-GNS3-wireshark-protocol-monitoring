# **4 - Traffic Filtering and analysis**


## **4.1 Introduction**

This chapter explains how Wireshark displays and analyses each protocol when filters are used to isolate specific traffic. ARP, DNS, ICMP, UDP, TCP, and HTTP are viewed separately, so their packets appear in a clear and simple view. Each protocol is examined to show its main fields, how the communication works, and how the request and response packets look during real network communication. The chapter also shows how to save selected packets as documentation. The goal is to build a deeper understanding of these protocols, which prepares the user for creating more advanced networks with proper monitoring in future labs and projects..


![](images/Pasted%20image%2020251115234354.png)


### **Protocol Overview**

| Protocol | Full Name                         | Meaning / Simple Explanation                                                           |
| -------- | --------------------------------- | -------------------------------------------------------------------------------------- |
| **ARP**  | Address Resolution Protocol       | Maps an IP address to a MAC address so devices can communicate inside the LAN.         |
| **DNS**  | Domain Name System                | Translates domain names (e.g., test.lab) into IP addresses.                            |
| **ICMP** | Internet Control Message Protocol | Used for connectivity testing (ping), error messages, and basic diagnostics.           |
| **UDP**  | User Datagram Protocol            | Fast, lightweight transport without acknowledgments. Used by DNS and similar services. |
| **TCP**  | Transmission Control Protocol     | Reliable transport with handshake, acknowledgments, and ordered delivery.              |
| **HTTP** | Hypertext Transfer Protocol       | Web protocol for client–server communication (GET requests and responses).             |


#### **Addressing Table**

| **Device**     | **IP Address** |
| -------------- | -------------- |
| Xubuntu-client | 192.168.50.10  |
| Windows-client | 192.168.50.11  |
| Router (R1)    | 192.168.50.1   |


## **4.2 Objectives**

This chapter focuses on analysing protocol behaviour using Wireshark’s decoded protocol view and packet summary information.  
For each protocol, a display filter is applied and key structural fields are examined.

**Objectives**

- Understand how to apply protocol-specific display filters.
    
- Analyse the decoded protocol structure in the middle Wireshark pane.
    
- Interpret key packet summary fields: **Time, Source, Destination, Protocol, Length, Info**.
    
- Identify the essential protocol fields that describe the request, response, and flow behaviour of ARP, DNS, ICMP, UDP, TCP, and HTTP traffic.
    
- Save and export protocol documentation to demonstrate clear packet interpretation, provide evidence of correct analysis, and support reproducibility in professional troubleshooting.



## **4.3 Steps**

1. **Start Capture on ens3**
    
2. **Apply ARP Filter and Analyse ARP Traffic**
    
3. **Apply DNS Filter and Analyse DNS Traffic**
    
4. **Apply ICMP Filter and Analyse ICMP Traffic**
    
5. **Apply UDP Filter and Analyse UDP Traffic**
    
6. **Apply TCP Filter and Analyse TCP Traffic**
    
7. **Apply HTTP Filter and Analyse HTTP Traffic**
    
8. **Save Capture and Export Protocol Outputs**


<br>
# 4.4 ARP Analysis


This section focuses on observing ARP traffic filtered in Wireshark. The ARP display filter isolates only ARP frames, providing a clear overview of how devices announce themselves and resolve MAC addresses inside the local network.

```
arp
```
![](images/Pasted%20image%2020251115205410.png)

### **Packet Summary Overview**

The following table summarises the key fields visible in the ARP packet list and their meaning:

| **Field**       | **Meaning**                                                                                                    |
| --------------- | -------------------------------------------------------------------------------------------------------------- |
| **Time**        | Indicates when the ARP frame was captured and helps track the timing of ARP activity.                          |
| **Source**      | MAC address of the device sending the ARP Request, Probe, or Announcement.                                     |
| **Destination** | Usually Broadcast, as ARP Requests are sent to all devices in the LAN. May show a unicast MAC when responding. |
| **Protocol**    | Identifies the frame as ARP, confirming that the filter is correct.                                            |
| **Length**      | Shows the size of the ARP frame, which is typically small and consistent.                                      |
| **Info**        | Describes the ARP action such as "Who has…", "Tell…", Probe, or Announcement.                                  |

#### **Results**

The ARP filter shows normal address resolution activity in the LAN. Many ARP frames appear because devices send routine Requests, Probes, and Announcements, and all broadcasts are visible in the capture. Only hosts that actively send ARP traffic appear in the list, which is why 192.168.50.10 is not shown at that moment. The captured frames confirm that ARP is working correctly in the network.

### **Detailed ARP Packet Analysis**

The following table summarises the essential fields observed in the selected ARP frame (Frame 47):


![](images/Pasted%20image%2020251115205841.png)

| **Field**           | **Description**                                                            |
| ------------------- | -------------------------------------------------------------------------- |
| **Source MAC**      | Device that sent the ARP Request.                                          |
| **Destination MAC** | Broadcast (ff:ff:ff:ff:ff:ff), meaning the request is sent to all devices. |
| **ARP Opcode**      | Indicates the operation type; here it is a Request.                        |
| **Sender IP**       | IP address of the device asking for MAC resolution (192.168.50.11).        |
| **Sender MAC**      | MAC address of the requesting device.                                      |
| **Target IP**       | IP address whose MAC the host is trying to discover (192.168.50.1).        |

#### **Results**

The selected ARP packet demonstrates the standard ARP discovery process: a host broadcasts an ARP Request to learn the MAC address of the target IP. Only the essential Ethernet and ARP header fields are required to identify the device that initiated the request, the address being queried, and the broadcast nature of the frame. This confirms correct ARP functionality and provides a clear example of how ARP discovery appears inside Wireshark.



## **4.5 DNS Analysis**

This section focuses on observing DNS traffic filtered in Wireshark. The DNS display filter isolates only DNS frames, providing a clear view of how name resolution occurs between the client and the DNS server. The capture includes queries generated while opening **test.lab** in the Firefox browser on Xubuntu.

```
dns
```
![](images/Pasted%20image%2020251115211303.png)

### **Packet Summary Overview**

The following table summarises the key fields visible in the DNS packet list and their meaning:

| Field           | Meaning                                                                                                                                                                                                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Time**        | Indicates when each DNS frame was captured and helps track the timing of name‑resolution events.                                                                                                                                                                   |
| **Source**      | IP address of the client sending DNS queries (192.168.50.10).                                                                                                                                                                                                      |
| **Destination** | IP address of the DNS server receiving the queries (192.168.50.1).                                                                                                                                                                                                 |
| **Protocol**    | Identifies the frame as DNS.                                                                                                                                                                                                                                       |
| **Length**      | Shows the size of the DNS frame. DNS packets are larger than ARP because they contain domain names, query types, and additional DNS fields.                                                                                                                        |
| **Info**        | Displays the DNS request or response type (A for IPv4, AAAA for IPv6) and the queried domain name (test.lab, ntp.ubuntu.com, google.com, etc.). AAAA queries are used to check whether the domain has an IPv6 address, even if the local network uses only IPv4.\| |

#### **Results**

The DNS filter shows active name‑resolution behaviour between the Xubuntu client and the DNS server. The captured frames include standard A and AAAA queries for test.lab and other domains, generated when accessing resources through the Firefox browser. The traffic confirms normal DNS operation, where the client sends multiple queries to resolve domain names and the DNS server responds accordingly.

### **Detailed DNS Packet Analysis**

This section provides a focused breakdown of the selected DNS frame (Frame 33). Only the essential protocol fields relevant to understanding how the DNS query is constructed and delivered are included, keeping the analysis clear and practical.


![](images/Pasted%20image%2020251115213203.png)


|Field|Description|
|---|---|
|Ethernet Source|MAC address of the Xubuntu client sending the DNS query. Identifies the sender at Layer 2.|
|Ethernet Destination|MAC address of the router/DNS server. The query is unicast‑forwarded directly to the default gateway.|
|IPv4 Source Address|192.168.50.10 – the host that initiated the DNS query for test.lab.|
|IPv4 Destination Address|192.168.50.1 – the local DNS server expected to answer.|
|IPv4 TTL|64 – common value for local LAN traffic, confirming the frame stays inside one network.|
|UDP Source Port|37144 – randomly generated ephemeral port for this specific DNS query; helps distinguish multiple queries.|
|UDP Destination Port|53 – standard DNS server port used for all DNS requests.|
|DNS Transaction ID|0x452e – unique identifier that matches the query with its response (visible in Frame 35).|
|DNS Query|test.lab – the domain name requested by the client.|
|Query Type|A – the client requests the IPv4 address of test.lab.|
|Additional Info|This frame shows the full path: client → DNS query → unicast → UDP → IPv4 → L2 delivery.|

#### **Results**

The DNS frame shows a standard name-resolution request. The client sends an IPv4 (A) query for **test.lab** to the local DNS server, using valid Ethernet, IPv4, UDP, and DNS fields. The structure reflects normal DNS request behavior in a local network.




## **4.6 ICMP Analysis**


This section focuses on ICMP traffic generated during a **ping test from a Windows client (192.168.50.11) to an Xubuntu client (192.168.50.10)**. The capture displays a sequence of Echo Requests and Echo Replies, showing how ICMP operates across Ethernet, IPv4, and ICMP layers in a local LAN environment.

```
icmp
```
![](images/Pasted%20image%2020251115214405.png)

### **Packet Summary overview**

|Field|Description|
|---|---|
|Time|Timestamp of each ICMP frame, useful for measuring latency between request and reply.|
|Source|The host sending the ICMP message. In this test, primarily the Windows client (192.168.50.11) sends Echo Requests.|
|Destination|The host receiving the message, here the Xubuntu client (192.168.50.10).|
|Protocol|Indicates that the captured frame belongs to ICMP.|
|Length|Shows ICMP frame size (typically around 74 bytes).|
|Info|Displays whether the frame is an Echo Request or Echo Reply, including ID and sequence number.|

### **Detailed Packet Analysis**

This section provides a clear and simplified explanation of the most important fields inside the selected ICMP frame (Frame 17). Only the values relevant to understanding the ping operation between the Windows and Xubuntu clients are included.


![](images/Pasted%20image%2020251115214501.png)


|Field|Description|
|---|---|
|Ethernet Source|MAC address of the Windows client sending the Echo Request.|
|Ethernet Destination|MAC address of the Xubuntu client receiving the request.|
|IPv4 Source Address|192.168.50.11 – Windows client initiating the ping.|
|IPv4 Destination Address|192.168.50.10 – Xubuntu client responding to the ping.|
|IPv4 TTL|128 – typical TTL value originating from Windows hosts.|
|ICMP Type|8 – Echo Request used by ping.|
|ICMP Code|0 – standard code for Echo Request.|
|ICMP Identifier|1 – identifies this ping session.|
|ICMP Sequence Number|256 – tracks message ordering in the ping sequence.|
|Response Frame|Frame 18 – the direct Echo Reply to this request.|

#### **Results**

The ICMP capture reflects standard ping behavior between the **Windows client (192.168.50.11)** and the **Xubuntu client (192.168.50.10)**. Each Echo Request is followed by a corresponding Echo Reply, with proper identifiers and sequence numbers. All protocol layers behave as expected in a simple LAN connectivity test.




## **4.7 UDP Packet Analysis**

This section demonstrates how DNS behaves when a **non‑existent domain name is queried**. By intentionally entering an invalid domain (**test.labb**) in the Xubuntu browser, we can observe how the client repeatedly sends DNS queries over UDP without receiving any response. This scenario provides a clear view of typical "no‑response" DNS behavior and how UDP‑based DNS retries appear inside Wireshark.


![](images/Pasted%20image%2020251115220519.png)

#### **Packet Summary Overview**

| Field       | Description                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------------------- |
| Time        | Timestamp of each DNS-over-UDP frame, showing how frequently the client retries the query.                    |
| Source      | The Xubuntu client sending DNS queries (192.168.50.10).                                                       |
| Destination | The DNS server that should respond (192.168.50.1), but provides no reply in this scenario.                    |
| Protocol    | Indicates that the transport protocol is UDP.                                                                 |
| Length      | Size of each UDP DNS frame. DNS queries remain small and consistent even during retries.                      |
| Info        | Displays query type (A, AAAA, HTTPS) and the requested domain (**test.labb**) with no accompanying responses. |

## **Detailed Analysis**

This section provides a clear breakdown of the selected UDP DNS frame (Frame 211). Only the important fields relevant to the failed DNS lookup are included.


![](images/Pasted%20image%2020251115220546.png)

|Field|Description|
|---|---|
|Ethernet Source|MAC address of the Xubuntu client generating the DNS query.|
|Ethernet Destination|MAC address of the DNS server/router receiving the request.|
|IPv4 Source Address|192.168.50.10 – the client attempting to resolve the invalid domain.|
|IPv4 Destination Address|192.168.50.1 – the DNS server expected to reply.|
|IPv4 TTL|64 – typical value indicating local LAN communication.|
|UDP Source Port|39797 – ephemeral port automatically selected for this DNS query.|
|UDP Destination Port|53 – standard DNS server port.|
|DNS Transaction ID|0xf9a3 – identifier used to match query to a response (none received).|
|DNS Query|test.labb – invalid domain sent by the client.|
|Answer RRs|0 – no response from the DNS server.|
|Authority RRs|0 – no authoritative information provided.|
|Additional RRs|0 – no additional DNS data returned.|

#### **Results**

The capture shows typical **no‑response DNS behavior**. The client repeatedly sends DNS queries for the invalid domain **test.labb**, but the DNS server provides no replies. All layers (Ethernet, IPv4, UDP, DNS) display valid packet structure, yet the absence of any response records confirms that the domain does not exist or the server cannot resolve it. This demonstrates how Wireshark captures and visualizes failed DNS resolution attempts over UDP.



## 4.8 **TCP and HTTP Traffic**

The list below shows all captured TCP and HTTP packets during a client request from 192.168.50.10 (Xubuntu client) to 192.168.50.1 (router providing both DNS and HTTP services).  
Using the filter tcp, Wireshark displays both lower-layer TCP segments and higher-layer HTTP messages, allowing combined analysis of the handshake and the HTTP request flow between the Xubuntu client and the router-based DNS/HTTP server.

![](images/Pasted%20image%2020251115223753.png)


### **Packet Summary Overview**

|Field|Description|
|---|---|
|Time|Timestamp of each TCP/HTTP packet. Helps determine the order of SYN, ACK, and data transmission.|
|Source|Host that sends the packet (client 192.168.50.10 or server 192.168.50.1).|
|Destination|Target host receiving the packet.|
|Protocol|Identifies TCP or HTTP.|
|Length|Packet size. TCP control packets are small; HTTP data packets are much larger.|
|Info|Shows TCP control flags (SYN starts a connection, ACK confirms received data, FIN closes a connection) and HTTP actions such as GET, along with sequence and acknowledgment numbers.|

The summary shows the TCP handshake (SYN → SYN/ACK → ACK) followed by HTTP-related packets such as GET and subsequent ACKs. This confirms that the Xubuntu client successfully establishes a TCP session with the router, sends an HTTP request, and the communication proceeds normally with valid sequence and acknowledgment numbers.

## Results

The summary confirms that a full TCP connection is established and HTTP communication begins successfully. The flow includes handshake packets, HTTP GET, and subsequent ACKs indicating normal transmission behavior.


### **Detailed Analysis – Packet 1512 (TCP SYN-ACK)**


This packet represents the second step of the TCP three-way handshake.  
The Xubuntu client first sends a SYN to initiate the connection.  
The server at **192.168.50.1** responds with this **SYN-ACK**, confirming that it is ready to establish the session and synchronize sequence numbers.  
This packet demonstrates the moment the server accepts the connection attempt and prepares for the final acknowledgment from the client.

Immediately after this SYN-ACK, the client sends an **ACK**, completing the handshake.  
Once the connection is fully established, the client issues an **HTTP GET** request, and after the communication finishes, a **FIN** is sent to properly close the TCP session.

![](images/Pasted%20image%2020251115224306.png)

#### **Field Summary Table**

| **Field**             | **Value**       | **Description**                                                                     |
| --------------------- | --------------- | ----------------------------------------------------------------------------------- |
| Source IP             | 192.168.50.1    | The server/router hosting HTTP on port 80.                                          |
| Destination IP        | 192.168.50.10   | Xubuntu client initiating the connection.                                           |
| Source Port           | 80              | HTTP port on the server.                                                            |
| Destination Port      | 47778           | Ephemeral client port used for this TCP session.                                    |
| Protocol              | TCP             | Transport protocol used for the handshake and HTTP.                                 |
| Flags                 | SYN, ACK (0x12) | Confirms the handshake. SYN expresses readiness, ACK acknowledges the client's SYN. |
| Sequence Number       | 0               | Initial sequence number sent by the server.                                         |
| Acknowledgment Number | 1               | Acknowledges the client's initial sequence number.                                  |
| Window Size           | 4128            | Amount of data the server can receive before requiring an acknowledgment.           |
| Header Length         | 24 bytes        | TCP header size including options.                                                  |
| TCP Option – MSS      | 1460 bytes      | Maximum Segment Size appropriate for Ethernet MTU.                                  |
| Segment Length        | 0               | No application data is carried in this packet.                                      |
| TTL                   | 255             | Typical value for LAN devices; no routing hops involved.                            |

#### **Results**

This SYN-ACK packet confirms that the server is prepared to establish a TCP session on port 80.  
The sequence and acknowledgment numbers show proper synchronization between both sides.

Following this packet, the expected TCP flow continues:  
the client replies with **ACK**, the session transitions into the data phase with an **HTTP GET**, and finally a **FIN** packet is exchanged to gracefully terminate the connection.

### **Complete TCP/HTTP Exchange Sequence**

| Line          | What Happened                            | Meaning                                                                                                                                            |
| ------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1511**      | **SYN**                                  | The client (Xubuntu) initiates the TCP connection to the server (192.168.50.1).                                                                    |
| **1512**      | **SYN-ACK**                              | The server responds, confirming readiness to establish the connection.                                                                             |
| **1513**      | **ACK**                                  | The client acknowledges the SYN-ACK → **TCP handshake is fully complete.**                                                                         |
| **1514**      | **HTTP GET / HTTP/1.1**                  | The client sends an HTTP request to the server over the newly established TCP session.                                                             |
| **1515–1518** | **ACK + HTTP processing frames**         | Server prepares the HTTP response (status 200 or 401 depending on context).                                                                        |
| **1519**      | **FIN, ACK**                             | The client indicates it wants to close the connection after sending the request.                                                                   |
| **1589+**     | **ACK + TCP segments (reassembled PDU)** | The server is still sending parts of the HTTP response. These include multiple TCP segments that Wireshark reassembles into the full HTTP payload. |
| **1603–1605** | **FIN → ACK**                            | The server begins closing its side of the connection.                                                                                              |
| **1605**      | **FIN-ACK → ACK**                        | Final exchange confirming that both sides have acknowledged the closure → **TCP session is fully terminated.**                                     |

## Saving and Documenting the Capture

### Purpose

This step demonstrates how to stop the capture, save the HTTP + TCP traffic, and explains why saving captures is important in real network operations.

### Stopping the Capture

1. Allow the capture to run until the relevant TCP and HTTP packets appear.
    
2. Click **Stop** (the red square icon) in the Wireshark toolbar **to stop capturing packets**.
    

### Saving the Capture

1. Go to **File → Save As…**.
    
2. Select a location in your project folder.
    
3. Save the file in the **.pcapng** format (recommended for modern Wireshark analysis).
    
4. Use a descriptive filename, for example:
    
    http_tcp_handshake_testlab.pcapng
    

### Practical Meaning

Saving captures allows network engineers to preserve evidence of the observed traffic, review handshake behavior, and re‑analyse issues later. It also enables sharing the capture with colleagues or attaching it to documentation when troubleshooting HTTP, TCP handshakes, or service errors. Captures provide clear visibility into packet‑level behavior and help verify whether the communication flow is correct.



## **4.9 Conclusion**

This chapter provides a structured view of how core protocols behave when filtered and analysed in Wireshark. ARP reveals local address-resolution activity, DNS shows standard and failed name-lookup patterns, and ICMP presents clear request-reply connectivity. UDP behaviour demonstrates repeated DNS retries without responses, while TCP and HTTP display a complete handshake and application exchange. Together, these captures build a consistent understanding of protocol structure, timing, and communication flow required for reliable network monitoring.


---

Next part: 
