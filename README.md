# **Wireshark Protocol Monitoring**

## **Introduction and Objectives**

This lab demonstrates practical protocol monitoring in a small network using Wireshark. It combines four connected chapters: initial network setup, full packet observation, DHCP troubleshooting, and advanced protocol filtering. Together, they form a complete workflow that shows how packets appear, how services respond, and how each protocol behaves during normal and failed communication.


![](images/Pasted%20image%2020251116000431.png)


**The objectives are:**

- Set up the LAN with DHCP, DNS, and HTTP services on the router.
    
- Observe packets in real time without filters to see how protocols communicate.
    
- Troubleshoot DHCP failure on Xubuntu caused by switch misconfiguration.
    
- Apply protocol-specific filters to analyse ARP, DNS, ICMP, UDP, TCP, and HTTP.
    
- Save captures for documentation and later analysis.
    

## **Lab Structure**

1. [Network Setup and Service Configuration](01-network-setup-and-service-configuration.md)
    
2. [Packet Capture and Protocol Observation](02-packet-capture-and-protocol-observation.md)
    
3. [DHCP Troubleshooting](03-dhcp-troubleshooting.md)
    
4. [Traffic Filtering and Analysis](04-traffic-filtering-and-analysis.md)
    

## **Key Features**

- Complete LAN configuration with DHCP, DNS, and HTTP on the router.
    
- Full packet capture showing natural ARP, DNS, ICMP, UDP, TCP, and HTTP traffic.
    
- Identification and resolution of DHCP failure caused by a SPAN-configured switchport.
    
- Protocol-specific filtering in Wireshark for clear ARP, DNS, ICMP, UDP, and TCP/HTTP analysis.
    
- Documentation workflow using saved packet captures in .pcapng format.
    

## **Author’s Note**

This was my first deeper experience with real packet monitoring. Seeing every protocol appear live in Wireshark helped me understand how devices talk to each other and how services react when something goes wrong. Troubleshooting the DHCP issue and then analysing each protocol separately gave me a much clearer picture of how a small network operates in practice. It felt like a big step forward in moving from basic configuration to real packet-level understanding.


--- 

© 2025 – Lukas Dula | Home Network Lab & Portfolio
