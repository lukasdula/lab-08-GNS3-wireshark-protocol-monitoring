# **1 - Network Setup and Service Configuration**
<br><br>

## **1.1 Introduction**

This chapter describes the basic setup of a small LAN used for packet monitoring. The router is given its initial IP configuration and is prepared to provide DHCP, DNS, and HTTP services. Both clients are set to DHCP mode so they receive their network settings from the router. Xubuntu is prepared with a simple installation of Wireshark. After these steps, the network is stable, reachable, and ready for capturing and analysing packets and protocols in the next parts of the lab.

![](images/Pasted%20image%2020251113033345.png)

<br><br>
## **1.2 Topology**

| Device         | Interface | Connected to -> | Peer Interface | IP Address                         | Subnet Mask   | Gateway      |
| -------------- | --------- | --------------- | -------------- | ---------------------------------- | ------------- | ------------ |
| R1 (Router)    | Gi0/1     | SW1             | Gi0/1 (SW1)    | 192.168.50.1                       | 255.255.255.0 | N/A          |
| Windows-Client | Ethernet0 | SW1             | Gi0/2 (SW1)    | DHCP (192.168.50.10–192.168.50.20) | 255.255.255.0 | 192.168.50.1 |
| Xubuntu-Client | Gi0/0     | SW1             | Gi0/3 (SW1)    | DHCP (192.168.50.10–192.168.50.20) | 255.255.255.0 | 192.168.50.1 |

<br>

## **1.3 Steps**

1. Configure the router interface to enable basic Layer 3 connectivity in the LAN.
    
2. Activate the router services required for DHCP, HTTP, and DNS operation.
    
3. Set both clients to automatic network configuration mode.
    
4. Verify that the clients receive their settings from the router.
    
5. Install Wireshark on Xubuntu and confirm that the interface is available for capture.
    
6. Test basic client connectivity to generate initial ARP and ICMP traffic.

<br><br>
## **1.4 Router Services - DHCP Configuration**


This step configures DHCP service on R1 so both clients automatically receive their IP settings. The router assigns addresses from the LAN subnet and provides the default gateway and DNS information.

## **Router (R1) - DHCP Configuration**

IP configuration for DHCP uses a single excluded address for the router itself. The DHCP pool **LOCAL-POOL** assigns dynamic addresses to all LAN clients.

```plaintext
enable
configure terminal
ip dhcp excluded-address 192.168.50.1 192.168.50.9
ip dhcp excluded-address 192.168.50.21 192.168.50.254
ip dhcp pool LOCAL-POOL
network 192.168.50.0 255.255.255.0
default-router 192.168.50.1
dns-server 192.168.50.1
end
write memory
```
![](images/Pasted%20image%2020251113012432.png)

### **Results**

DHCP service on R1 now provides a small dynamic range of addresses suitable for this lab. Both clients successfully receive their IP settings from the defined pool. Dynamic range is limited to eleven available addresses for simplicity.

### Diagnostics

This diagnostic command verifies the DHCP pool status and shows the number of used and available addresses.

  ```
show ip dhcp pool
  ```
![](images/Pasted%20image%2020251113012738.png)

<br><br>

## **1.5 Router Services - HTTP Configuration**


This step enables the HTTP service on R1 so clients in the LAN can generate web traffic for packet monitoring. The service provides a simple test page that allows Wireshark to capture HTTP requests and responses.

#### **Router (R1) - HTTP Service Activation**

HTTP service is enabled globally on the router to allow basic web access for the lab.

```plaintext
enable
configure terminal
ip http server
end
write memory
```
![](images/Pasted%20image%2020251113013218.png)
### **Results**

HTTP service on R1 becomes active and responds to client requests. Clients can access the router's web page to generate HTTP traffic for analysis.



<br><br>
## **1.6 Router Services - DNS Configuration**


This step configures a simple DNS service on R1 so that clients can generate DNS queries for packet analysis. The router provides a basic hostname-to-IP mapping used for testing in Wireshark.

### R1 - DNS Service Setup

DNS service is enabled globally, and a test hostname is mapped to the router’s LAN IP address.

```plaintext
enable
configure terminal
ip dns server
ip host test.lab 192.168.50.1
end
write memory
```
![](images/Pasted%20image%2020251113022309.png)
### Results

DNS service on R1 responds to client queries for the configured hostname. Clients can resolve the test domain name, allowing Wireshark to capture DNS request and response packets.
<br><br>
## **1.7 Xubuntu DHCP, HTTP and DNS Verification**


This section verifies that the Xubuntu client successfully receives an IP address from the DHCP pool on R1. The network adapter must be configured to use automatic DHCP so the client can request and obtain its settings dynamically.

### **Xubuntu – DHCP Configuration Method**

The network connection is set to automatic mode so the client uses DHCP.

```plaintext
method automatic DHCP
```
![](images/Pasted%20image%2020251113014641.png)


### **Xubuntu – Assigned IP Address**

The terminal output shows the dynamic IPv4 address provided by the router.

```plaintext
ip a
```
![](images/Pasted%20image%2020251113014615.png)


### Results

The Xubuntu client receives an IP address from the configured DHCP range, confirming that DHCP service on R1 is functioning correctly and that the client is properly connected to the LAN.



### **Xubuntu HTTP Verification****


This section verifies that the Xubuntu client can reach the HTTP service running on R1. 
<br><br>
#### R1 – HTTP Authentication Setup

A local user account is created on R1 to allow browser access when HTTP authentication is required.

```plaintext
conf t
username admin privilege 15 secret cisco
ip http authentication local
end
write memory
```
![](images/Pasted%20image%2020251113015708.png)


#### **Xubuntu – Accessing the Router HTTP Page**

The browser is directed to the router's LAN address to test HTTP connectivity.

```plaintext
http://192.168.50.1
```
![](images/Pasted%20image%2020251113015459.png)

### Results

The Xubuntu client successfully loads the router's default Cisco HTTP page, confirming that HTTP service on R1 is active and reachable from the LAN.
<br><br>
## **1.8 Windows Client DNS Verification****


The Windows client confirms DHCP functionality by successfully receiving an IP address from the router. In this lab, the client is assigned **192.168.50.11**, indicating that the DHCP pool on R1 is operating correctly. The client also loads the router’s web interface both through its IP address and through the DNS hostname _test.lab_, confirming proper operation of both HTTP and DNS services in the network.

```
http://192.168.50.1
http://test.lab
```
![](images/Pasted%20image%2020251113023555.png)


<br><br>

## **1.9 Xubuntu Client – Wireshark Installation**

Wireshark is installed on the Xubuntu client to capture and analyze network traffic generated in the lab. The client uses the default package manager to install the application, ensuring it is ready for packet monitoring.

```
sudo apt update
sudo apt install wireshark -y
```
![](images/Pasted%20image%2020251113025633.png)



<br><br>

## **1.10 Full Connectivity Verification – Router, Xubuntu and Windows**

This step verifies clear end-to-end connectivity across the lab. Each device sends ICMP echo requests to the others to confirm that ARP and ICMP traffic are functioning before packet capture.

### From Xubuntu → Router (R1)

```
ping 192.168.50.1
```
![](images/Pasted%20image%2020251113031454.png)

### From Xubuntu → Windows

```
ping 192.168.50.11
```
![](images/Pasted%20image%2020251113031515.png)

### From Windows → Router (R1)

```
ping 192.168.50.1
```
![](images/Pasted%20image%2020251113030402.png)

### From Windows → Xubuntu

```
ping 192.168.50.10
```
![](images/Pasted%20image%2020251113031542.png)

### From Router (R1) → Both Clients

```
ping 192.168.50.10
ping 192.168.50.11
```
![](images/Pasted%20image%2020251113031611.png)

### Results

All connectivity tests succeed. The pings confirm that every device in the network communicates correctly, and the entire topology operates as expected before packet capture begins.

<br><br>

## **1.11 Conclusion**

This chapter establishes the complete foundational setup of the lab environment. The router is configured with its essential services, the clients receive their network settings through DHCP, and both HTTP and DNS are fully reachable from Xubuntu and Windows. Basic connectivity tests confirm that all devices communicate correctly across the LAN. With the network fully operational and verified, the environment is now ready for the next chapter focused on packet monitoring and protocol analysis in Wireshark.

---

**Next part:** [Packet Capture and Protocol Observation](02-packet-capture-and-protocol-observation.md)
