# Creating firewall using iptables and pfsense

This article is shown for configuring custom firewall using iptables & pfense.

## Introduction to Firewalls

A **firewall** is a security system that monitors and controls incoming and outgoing network traffic based on predetermined security rules. Its primary purpose is to create a barrier between a trusted internal network and untrusted external networks, such as the internet, to protect against unauthorized access, cyberattacks, and data breaches.

## How Firewalls Work

### 1. Traffic Filtering
Firewalls inspect the data packets traveling to and from our network and decide whether to allow or block them based on a set of security rules.

### 2. Types of Firewalls

- **Packet Filtering Firewalls**: Check each packet that passes through the network for information such as IP addresses, packet type, port numbers, etc., and block or allow them based on the rule set.
- **Stateful Inspection Firewalls**: Keep track of active connections and make decisions based on the state of these connections as well as the packet data.
- **Proxy Firewalls**: Act as an intermediary between your network and external servers, examining data at the application level.
- **Next-Generation Firewalls (NGFWs)**: Include more advanced features like deep packet inspection, intrusion prevention systems (IPS), and application awareness.

### 3. Rule-Based Operation
Administrators define rules such as "allow traffic on port 80 (HTTP)" or "block traffic from a specific IP address." The firewall enforces these rules to manage traffic flow.

### 4. Monitoring and Alerts
Firewalls can log traffic details and alert administrators about suspicious activities, enabling them to take proactive measures.

By controlling data flow based on rules, firewalls help prevent unauthorized access, malware, and other security threats from compromising network integrity.


---
## Firewall we'll use
Here we'll be talking about software based firewalls only. We can use either `iptables(for linux)` or `pfsense(for FreeBSD-Based Systems)` to create firewall. iptables is a packet filter and pfSense is a NGFW) There are many other tools also for firewall.Like-

| **Operating System**       | **Custom Firewall Tools**                                                                 |
|----------------------------|-------------------------------------------------------------------------------------------|
| **Linux**                  | iptables, nftables, Firewalld, UFW, CSF, Shorewall, IPFire, pfSense                       |
| **FreeBSD**                | pf, ipfw, pfSense                                                                          |
| **macOS**                  | pf, Network Extension Framework, Little Snitch                                            |
| **Windows**                | Windows Filtering Platform (WFP), Windows Firewall, ZoneAlarm, Comodo Firewall, GlassWire |
| **OpenBSD**                | pf, ipfilter                                                                               |
| **Cloud**                  | AWS Security Groups, Azure NSGs, Google Cloud Firewall, Cloudflare                        |
| **Virtualization**         | Docker (iptables), VMware ESXi, Proxmox VE                                                 |

Depending on different criterias different tools are best. But overall for general use pfSense and for advance use iptables seems to be best. Because- 
- pfSense provides a web-based GUI, making it easy to manage without deep technical knowledge, yet it supports advanced features like VPN, load balancing, intrusion detection, etc. It’s a solid choice for a home or small office firewall.
- iptables allows for deep customization and fine-grained control over packet filtering, NAT, and connection tracking. It requires knowledge of the Linux networking stack and rule syntax, but it’s extremely powerful.

**Note:** Both of them works on different OS. iptables is a command-line tool for configuring the Netfilter framework in Linux. `Netfilter` is a set of hooks within the Linux kernel that allow for `packet filtering`, `NAT`, and `other networking operations`.pfSense is a free, open-source firewall and router software based on FreeBSD. It is typically used for network security and is often deployed on dedicated hardware or virtual machines to manage network traffic. You can use virtual machine to use FreeBSD to install pfSense. FreeBSD is a Unix-like, open-source operating system known for its performance, security features, and advanced networking capabilities. 

## Creating firewall using pfsense:
- Once you've written your article, commit the changes and push them to the repository. 
  - Use GitHub Desktop or your terminal for this, depending on your preference.






## Creating firewall using iptables:
As mentioned, iptables allows for deep customization and fine-grained control over `packet filtering`, `NAT`, and `connection tracking`.From the name of iptables we can assume that it is a collection of set of tables.
[Link to NAT](#subfield-1)

# Tables in iptables

In **iptables**, there are four main tables that organize different sets of rules for processing packets. Each table serves a specific purpose and is associated with certain *chains* for handling packet processing:

### 1. **Filter Table**
- **Purpose**: This is the default table and is used for general packet filtering.
- **Chains**:
  - `INPUT`: Processes packets destined for the local system.
  - `OUTPUT`: Processes packets generated by the local system.
  - `FORWARD`: Processes packets routed through the system (not locally destined).

### 2. **NAT (Network Address Translation) Table**
- **Purpose**: Handles packet modifications for network address translation.
- **Chains**:
  - `PREROUTING`: Alters packets as soon as they arrive (before routing).
  - `OUTPUT`: Alters locally generated packets before routing.
  - `POSTROUTING`: Alters packets as they are about to leave the system.

### 3. **Mangle Table**
- **Purpose**: Used for specialized packet alterations, such as changing the TOS (Type of Service) or TTL (Time to Live) fields.
- **Chains**:
  - `PREROUTING`: Alters incoming packets before routing.
  - `INPUT`: Alters packets before they reach a local process.
  - `FORWARD`: Alters packets being forwarded through the system.
  - `OUTPUT`: Alters packets before they are sent out.
  - `POSTROUTING`: Alters packets as they are about to leave.

### 4. **Raw Table**
- **Purpose**: Used for configuring packets that should be exempt from connection tracking. This can improve performance in specific cases.
- **Chains**:
  - `PREROUTING`: Processes incoming packets before they hit connection tracking.
  - `OUTPUT`: Processes locally generated packets before connection tracking.

Each table serves a distinct role and interacts with specific chains to define how packets are processed and handled by the system.



---
Data is transferred through the OSI model in a layered process:

1. **Application Layer**: The sender’s application generates the data (e.g., an email).It provides services like file transfers, email, web browsing, and network management by enabling communication between software applications over a network. It ensures data is presented in a user-friendly format and supports protocols like HTTP, FTP, DNS, and SMTP for various internet and network functions.
2. **Presentation Layer**: Data is formatted, encrypted, or compressed.Provided by the correspondant app, not by the os. 
3. **Session Layer**: A session is established and maintained for communication.Authentication,Authorization,Session restoration,webinar. Provided by the correspondant app, not by the os. 
4. **Transport Layer**: Data is broken into smaller segments, and error-checking (e.g., TCP) is applied. End to End(Port to Port delivery), Reliability(for inorder delivery TCP and for unorder devlivery UDP is used), error control(checksum is used),congession control, Flow control(destination sends the window size first and according to that size of the segmentation size is assumed), MUX & DEMUX is used to maintain instraction of many applications (many to one TO one to many).
5. **Network Layer**: Routing information (IP addresses) is added for delivery across different networks.Host to  Host(Source to destination), Logical decision to send(IP contains Network and Host), Routing(Uses different router which decides the shortest paths using RIP/OSPF algorithms), Fragmantation(divide data into small portions), Congession control(gives feedback if there's any problem in any node) 
6. **Data Link Layer**: Frames are created with MAC addresses for local delivery.Hop to Hop/Node to Node delivery, Flow control(node to node, checks the size and speed of data accordind to recievers capacity), error control(node to node), access control, Mac address, frames.
7. **Physical Layer**: The actual transmission of raw bits occurs over physical media (cables, wireless). bit to signal, signal to bit happens here.




### Application layer protocols along with their port numbers & transfer protocols:
| **Protocol**        | **Port Number** | **Transport Protocol** | **Use**                                                                 |
|----------------------|-----------------|-------------------------|-------------------------------------------------------------------------|
| HTTP                | 80              | TCP                     | Used for transferring web pages and resources over the internet.       |
| HTTPS               | 443             | TCP                     | Secure version of HTTP using SSL/TLS for encrypted communication.       |
| FTP (Data)          | 20              | TCP                     | Transfers data files between systems.                                   |
| FTP (Control)       | 21              | TCP                     | Manages the connection and control commands for FTP.                   |
| SSH                 | 22              | TCP                     | Provides secure remote login and command execution.                     |
| Telnet              | 23              | TCP                     | Used for remote command-line access (insecure, largely deprecated).     |
| SMTP                | 25              | TCP                     | Sends email between servers and from clients to servers.                |
| DNS                 | 53              | TCP/UDP                 | Resolves domain names to IP addresses and vice versa.                   |
| POP3                | 110             | TCP                     | Retrieves emails from a mail server (downloads and removes from server).|
| IMAP                | 143             | TCP                     | Retrieves emails, allowing synchronization across multiple devices.     |
| LDAP                | 389             | TCP/UDP                 | Accesses and manages directory services like user information.          |
| LDAPS               | 636             | TCP                     | Secure version of LDAP using SSL/TLS for encrypted communication.       |
| DHCP (Server)       | 67              | UDP                     | Assigns IP addresses to devices on a network dynamically.               |
| DHCP (Client)       | 68              | UDP                     | Receives dynamic IP configuration from the server.                      |
| TFTP                | 69              | UDP                     | Transfers files, often used in network booting or configuration tasks.  |
| SNMP                | 161             | UDP                     | Manages and monitors devices on a network (e.g., routers, switches).    |
| SNMP (Trap)         | 162             | UDP                     | Receives alerts or notifications from managed devices.                  |
| RDP                 | 3389            | TCP                     | Provides remote desktop access to Windows systems.                      |
| SIP                 | 5060            | TCP/UDP                 | Manages signaling for voice over IP (VoIP) and multimedia sessions.     |
| SIP (Secure)        | 5061            | TCP                     | Secure version of SIP using encryption for VoIP communications.         |



## Subfield 1
# Network Address Translation (NAT)

**Network Address Translation (NAT)** is a technique used in computer networking to modify the IP address information in the header of data packets as they pass through a router or firewall. The main purpose of NAT is to enable multiple devices on a local network to share a single public IP address when accessing resources on the internet.

## How NAT Works:

When a device on a local network (like a computer, smartphone, or server) wants to communicate with a device on the internet, the device sends packets to the router or firewall. The router, which typically has a private IP address (used only within the local network), performs the following steps:

### Source Address Translation:
- The router replaces the private IP address of the sending device with its own public IP address before forwarding the packet to the internet.
- The router keeps track of this translation in a **NAT table** so that responses can be correctly routed back to the right device.

### Destination Address Translation:
- When a response from the internet comes back to the router, the router checks the NAT table, identifies the corresponding private IP address, and forwards the response to the correct device on the local network.

## Types of NAT:

### 1. Static NAT (1:1 NAT)
In static NAT, a single private IP address is mapped to a single public IP address. This means that the mapping is consistent and never changes.  
This is useful for cases where a device inside a local network needs to be reachable from the outside world with a fixed public IP (e.g., web servers, FTP servers).  
**Example**: A private IP `192.168.1.2` is mapped to a public IP `203.0.113.5`.

### 2. Dynamic NAT
Dynamic NAT works similarly to static NAT but uses a pool of public IP addresses. When a device on the local network needs to access the internet, it is assigned a public IP address from the pool dynamically.  
This allows multiple devices to share a smaller number of public IP addresses.

### 3. Port Address Translation (PAT) or "NAT Overloading"
PAT is the most commonly used form of NAT. It allows multiple devices on a local network to share a single public IP address by differentiating the traffic based on port numbers.  
When a device sends a packet to the internet, PAT keeps track of the source port number along with the public IP address. When a response comes back, the router uses the source port number to identify which device the response is for.  
This is commonly referred to as "overloading" because it enables many devices to share a single IP address.

**Example**:  
Local network devices with private IPs (`192.168.1.2`, `192.168.1.3`) access the internet using a single public IP address (`203.0.113.5`) but with different source port numbers (e.g., port 1024 for `192.168.1.2` and port 1025 for `192.168.1.3`).

## Advantages of NAT:
- **IP Address Conservation**: NAT helps conserve public IP addresses by allowing multiple devices to share a single public IP. This is especially important given the limited availability of IPv4 addresses.
- **Security**: NAT can act as a basic form of security by hiding the internal private network structure from the outside world. External devices cannot directly initiate communication with devices behind a NAT router unless specifically configured (via port forwarding).
- **Simplified Network Management**: Using NAT, you don’t need a public IP address for every device in your local network, making it easier to manage and reducing the need for a large block of public IP addresses.

## Disadvantages of NAT:
- **Performance Overhead**: The router must maintain a translation table and process each packet, which adds some processing overhead. In very high-traffic networks, this can become a bottleneck.
- **Problems with Certain Applications**: Some applications (e.g., VoIP, certain online games, peer-to-peer applications) may have trouble with NAT because they rely on direct peer-to-peer connections, which can be blocked or altered by NAT. This can sometimes require specific configurations, such as port forwarding or DMZ setups.
- **End-to-End Connectivity Issues**: NAT can break the end-to-end principle of the internet, which assumes that devices can directly communicate with each other using their public IPs.

## Example of NAT in Action:
Imagine a home network with three devices:

- **Device A**: Private IP `192.168.1.2`
- **Device B**: Private IP `192.168.1.3`
- **Device C**: Private IP `192.168.1.4`

When these devices want to access the internet, the router (with public IP `203.0.113.5`) performs NAT.

1. **Device A** sends a packet to `www.example.com`. The router changes the source address from `192.168.1.2` to `203.0.113.5` and assigns a unique port (e.g., `1024`).
2. **Device B** sends a packet to the same destination. The router changes the source address from `192.168.1.3` to `203.0.113.5` but assigns a different port (e.g., `1025`).

When the response comes back to the router at `203.0.113.5`, the router uses the port numbers to direct the responses to the correct internal device.
