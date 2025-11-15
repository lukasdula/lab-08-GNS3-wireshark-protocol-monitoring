# 2 - Packet Capture and Protocol Observation

## **2.1 Introduction**

Wireshark captures every packet that moves across the interface and shows it in real time. Each line represents one packet with the time it appears, the source, the destination, the protocol, and simple information about what the packet does. In this chapter, the capture runs without filters, so the user can see natural traffic such as ARP, DNS, ICMP, UDP, TCP, and HTTP. Basic actions like pinging between clients, pinging the router, or opening the test.lab page generate packets, and Wireshark displays them in the order they arrive. This introduction helps the user understand how common protocols appear during normal communication and how the network behaves when devices send or receive data.


![](images/Pasted%20image%2020251114034407.png)



### Protocol Overview

| Protocol | Full Name                         | Meaning / Simple Explanation                                                           |
| -------- | --------------------------------- | -------------------------------------------------------------------------------------- |
| **ARP**  | Address Resolution Protocol       | Maps an IP address to a MAC address so devices can communicate inside the LAN.         |
| **DNS**  | Domain Name System                | Translates domain names (e.g., test.lab) into IP addresses.                            |
| **ICMP** | Internet Control Message Protocol | Used for connectivity testing (ping), error messages, and basic diagnostics.           |
| **UDP**  | User Datagram Protocol            | Fast, lightweight transport without acknowledgments. Used by DNS and similar services. |
| **TCP**  | Transmission Control Protocol     | Reliable transport with handshake, acknowledgments, and ordered delivery.              |
| **HTTP** | Hypertext Transfer Protocol       | Web protocol for client–server communication (GET requests and responses).             |



### **Addressing Table**

| Device         | IP Address    |
| -------------- | ------------- |
| Xubuntu-client | 192.168.50.10 |
| Windows-client | 192.168.50.11 |
| Router (R1)    | 192.168.50.1  |




## **2.2 Steps**

1. Start packet capture on the Xubuntu interface without filters.
    
2. See how ARP packets appear when devices communicate on the local network.
    
3. See how DNS packets appear when a domain name is requested.
    
4. See how ICMP packets appear when a device is reached with ping.
    
5. See how UDP packets appear appear when a webpage is opened.
    
6. See how TCP and HTTP packets appear when a webpage is opened.
    



## **2.3 Full Packet View**

Wireshark shows all captured packets in the main list at the top. Each line represents one packet with its time, source, destination, protocol, and basic information. The lower left panel displays the structured details of the selected packet, such as Ethernet, IP, or TCP fields. The lower right panel shows the raw packet data in hexadecimal and ASCII form. Together, these views provide a complete picture of how the packet looks on every layer.


 ```
 wireshark
 ```
![](images/Pasted%20image%2020251114005124.png)

After starting Wireshark, a list of available network interfaces appears. Each interface represents a network path that the system can capture from. In this environment, the active interface is named ens3, and it is used to monitor all traffic coming from and going to the virtual machine.

 ```
 ens3
 ```
![](images/Pasted%20image%2020251114005143.png)


**Notes:**

> **Loopback (lo)** – internal interface used by the system to communicate with itself.  
> It does not send or receive any real network traffic, so Wireshark shows only local system packets.

> **Any** – virtual interface that merges all other interfaces into one view.  
> It mixes multiple traffic sources together and produces noise, so it is not used for controlled packet capture.



### **Main list**

**The main list displays all captured packets in real time.**

![](images/Pasted%20image%2020251114030452.png)




## **2.4 ARP Capture**

ARP identifies the MAC address of a device inside the local network when the IP address is known. ARP packets appear only when a device does not have the required MAC address stored in its ARP cache. After the cache is filled, repeated pings no longer create new ARP requests until the cache expires.

| ARP Type    | Description                                          |
| ----------- | ---------------------------------------------------- |
| ARP Request | "Who has ?" — a device asks who owns an IP address.  |
| ARP Reply   | " is at " — the target device responds with its MAC. |

#### Ping Xubuntu to Windows

```
ping 192.168.50.11
```
![](images/Pasted%20image%2020251114011133.png)

#### Ping Windows to Xubuntu

```
ping 192.168.50.10
```
![](images/Pasted%20image%2020251114011148.png)

#### **ICMP Traffic

Ping packets between Xubuntu and Windows are visible here. These ICMP exchanges confirm that both devices communicate correctly. The initial ping also triggered ARP requests and replies, which allowed the devices to learn each other's MAC addresses before the ICMP traffic could flow.

![](images/Pasted%20image%2020251114011910.png)

### **ARP Response**

ARP replies appear immediately after the request. They confirm the MAC address of the destination device and allow the communication to continue across the local network. You will see the request and reply sequence on the main packet list when the ping begins.

![](images/Pasted%20image%2020251114011535.png)


>**Notes.:** ARP does not depend on ping. It appears automatically whenever a device needs the MAC address of another device. Ping can trigger ARP if the MAC address is unknown, but ARP also appears during normal network activity such as DNS, DHCP, or opening web pages.




## **2.5 DNS Capture (Xubuntu)**

This section explains how DNS traffic is captured when a webpage is opened from the Xubuntu browser. DNS converts a hostname into an IP address, and Wireshark allows you to see both the query and the response directly.

|DNS Phase|Description|
|---|---|
|DNS Query|Xubuntu asks for the IP address of the domain name.|
|DNS Response|The router returns the resolved IP address.|

#### Triggering DNS from Xubuntu

```
Open browser and navigate to:  http://test.lab
```
![](images/Pasted%20image%2020251114020020.png)


### **DNS Frames in Wireshark**

After opening the test.lab page, DNS packets immediately appear in the capture. You will see a query for the domain name and a matching response. Add your screenshot below.


![](images/Pasted%20image%2020251114020123.png)


> **Notes.:** Xubuntu generates the DNS request as soon as the hostname is used in the browser.  
> The router acts as the DNS responder.  
> DNS packets are easy to identify in the Protocol column (DNS).




## **2.6 ICMP Capture (Windows-client to Xubuntu-client)**

This section shows how ICMP traffic appears when the Windows client checks connectivity with the Xubuntu client.  
This produces clean and direct ICMP traffic that Wireshark on Xubuntu can capture without additional configuration.

## ICMP Overview

|ICMP Phase|Description|
|---|---|
|Echo Request|Windows-client sends an ICMP ping to Xubuntu-client.|
|Echo Reply|Xubuntu-client responds with an ICMP reply.|

#### Triggering ICMP from Windows

```
ping 192.168.50.10
```
![](images/Pasted%20image%2020251114030252.png)


### ICMP Frames in Wireshark

Wireshark immediately displays the ICMP request and reply frames.  
The Windows-client sends Echo Request packets to 192.168.50.10 and receives Echo Reply packets back.  
The Protocol column marks these packets as ICMP, and the Info field shows “Echo request” and “Echo reply”.

![](images/Pasted%20image%2020251114030234.png)



>**Notes.:** Xubuntu captures ICMP easily when it is directly involved in the communication.  
If ICMP traffic flows only between Windows-client and the router, it does not appear in the capture because Xubuntu is on a different switch port.  
A SPAN (port mirroring) configuration can be used to mirror other ports to Xubuntu, but this often introduces unnecessary ARP and broadcast traffic, which makes the capture harder to read. For clarity, SPAN is not used in this chapter.




## **2.7 UDP Capture**

This section shows how UDP traffic appears when Xubuntu resolves a domain name. DNS uses UDP by default, so opening a webpage triggers a DNS Query and DNS Response exchange that is visible in Wireshark.

## UDP Overview

| UDP Phase    | Description                                      |
| ------------ | ------------------------------------------------ |
| DNS Query    | Xubuntu sends a UDP request for the domain name. |
| DNS Response | The router replies with the resolved IP address. |


## Triggering UDP from Xubuntu


Open browser and navigate to:  

```
http://test.lab
```
![](images/Pasted%20image%2020251114020020.png)

## UDP Frames in Wireshark

Wireshark displays a DNS Query from Xubuntu to the router on UDP port 53, followed by a DNS Response returning the resolved address. Both packets appear as UDP in the Protocol column with “Standard query” and “Standard query response” in the Info field.

![](images/Pasted%20image%2020251114031326.png)




## **2.8 TCP and HTTP Capture (Webpage Request)**

This section shows how TCP and HTTP traffic appear when Xubuntu opens a webpage directly using the router’s IP address. This removes DNS from the process and produces a clean TCP and HTTP exchange.

## TCP/HTTP Overview

|Phase|Description|
|---|---|
|TCP Handshake|Xubuntu starts the connection with SYN, the router replies with SYN/ACK, and Xubuntu completes the handshake with ACK.|
|HTTP Request|Xubuntu sends an HTTP GET request to the router.|
|HTTP Response|The router answers with an HTTP status message.|
|Connection Close|Both devices exchange FIN/ACK packets to close the session.|

## Triggering TCP and HTTP from Xubuntu

```
Open browser and navigate to:  http://192.168.50.1
```
![](images/Pasted%20image%2020251114033126.png)


Wireshark shows the TCP three-way handshake, followed by an HTTP GET request from Xubuntu and an HTTP response from the router. Additional TCP ACK and FIN packets appear as the connection transfers data and later closes.

### **TCP/HTTP Capture**

![](images/Pasted%20image%2020251114033247.png)



## **2.9 Conclusion** 

This chapter shows how essential network protocols appear in live packet capture. Xubuntu records natural traffic without filters, allowing clear observation of ARP discovery, DNS resolution over UDP, ICMP connectivity tests, TCP session setup, and the HTTP request–response exchange. Together, these captures form a practical baseline of how devices communicate on the wire and how protocol layers interact during normal network operation. This baseline prepares the user for deeper filtering and detailed analysis in the next chapter.


---

**Next part:** [DHCP Troubleshooting](03-dhcp-troubleshooting.md)
