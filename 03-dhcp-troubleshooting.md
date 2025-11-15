# **3 - Troubleshooting DHCP Failure on Xubuntu Client**

**Introduce:**

This section identifies why the Xubuntu client did not receive an IPv4 address via DHCP and documents each diagnostic step used to locate and resolve the issue.

## **3.1 R1 -> Verification of DHCP Server**

A quick check confirms whether the router’s DHCP service is active and ready to assign addresses.

```plaintext
show ip dhcp pool
show ip dhcp binding
```
![](images/Pasted%20image%2020251115193124.png)

A brief review of the DHCP pool indicates that R1 is assigning addresses correctly. The bindings also show that the Xubuntu client previously received an IPv4 lease, and the Windows client currently obtains an address without issues. This confirms that the DHCP server on R1 operates correctly.



## **3.2 Xubuntu-Client - DHCP Renewal Attempt**

An attempt is made to renew the DHCP lease to verify whether the client can obtain an address from the router.

```plaintext
sudo dhclient -r ens3
sudo dhclient ens3
```
![](images/Pasted%20image%2020251115193242.png)

The client attempts to renew its DHCP lease using the `dhclient` utility. The command is not available on the Xubuntu image used in this lab, which confirms that the DHCP renewal cannot be triggered manually at this stage. Because no IPv4 address is received, troubleshooting continues with check network in Xubuntu-client.



## **3.3 Xubuntu-Client - Interface Method Check**

This step verifies that the network interface is configured to request an IPv4 address using DHCP.

```plaintext
nmcli device show ens3
```
![](images/Pasted%20image%2020251115193817.png)
The output shows that ENS3 is detected but remains disconnected, meaning no active link is established. Without a valid Layer-2 connection, the client cannot send DHCP requests, and no IPv4 address is assigned. This confirms the issue is not with the DHCP server but with the network path between the client and the switch.

---

## **3.4 SW1 - Initial Interface Status**

This step provides a quick overview of the switch interfaces to confirm whether the client-side port is active and ready for communication.

````plaintext
show ip interface brief
````
![](images/Pasted%20image%2020251115193920.png)

The output shows that Gi0/3 is administratively up but the protocol state remains down, indicating that the interface does not form an active link. This confirms that the issue is not caused by DHCP but by the Layer-2 connectivity on this specific port. The following diagnostic steps focus on identifying what is misconfigured on Gi0/3.

---

## **3.5 SW1 - CDP Verification**

CDP is used to confirm whether the expected physical connections exist between devices.

```plaintext
show cdp neighbors
```
![](images/Pasted%20image%2020251115194141.png)

CDP confirms which ports are connected to R1. Gi0/3 does not report a neighbor, suggesting the Xubuntu client is either disconnected or misconfigured.

---

## **3.6 SW1 - Discovery of Monitoring Mode**

The switch shows that Gi0/3 is in monitoring mode, indicating the port is configured as a SPAN destination and does not forward normal traffic.

```plaintext
show interface status
```
![](images/Pasted%20image%2020251115194202.png)

Gi0/3 is listed as **monitoring**, meaning it is configured as a SPAN destination port. SPAN ports do not forward normal traffic, including DHCP.

---

## **3.7 SW1 – Resetting a SPAN-Affected Interface**

This step restores a switchport that was previously used as a SPAN destination. A simple default reset brings the port up, but the active SPAN session must also be removed before normal forwarding is restored.

```plaintext
default interface gi0/3
```
![](images/Pasted%20image%2020251115194252.png)

This resets the interface configuration, but the port may still remain in monitoring mode.

```plaintext
no monitor session 1
```
![](images/Pasted%20image%2020251115195103.png)

This command removes the active SPAN session and fully clears the monitoring state.

```plaintext
interface gi0/3
switchport mode access
switchport access vlan 1
no shutdown
```
![](images/Pasted%20image%2020251115195141.png)

These commands return the port to a normal access mode within VLAN 1.

### Result

The interface leaves monitoring mode and resumes normal Layer-2 forwarding, allowing the connected Xubuntu client to establish connectivity and receive an IPv4 addr

---

## **3.8 Xubuntu-Client – Final DHCP Verification**

After correcting the switch configuration and removing the active SPAN session, the client’s interface is checked to confirm whether DHCP is functioning.

```plaintext
ip a
```
![](images/Pasted%20image%2020251115195608.png)

This command verifies that the interface receives a valid IPv4 address. Once the port is restored to normal Layer-2 forwarding, the Xubuntu client **gets** an address from the DHCP server.

---

## **3.9 Result**

Gi0/3 transitions to **up/up**, and the Xubuntu client successfully receives an IPv4 address from R1’s DHCP server. Connectivity tests confirm that DHCP now works normally.

After resolving the issue, work can continue with chapter 4: **04-traffic-filtering-and-analysis**.


---


**Next part:**