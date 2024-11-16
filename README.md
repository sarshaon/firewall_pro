# Creating firewall using iptables and pfsense

This article is shown for configuring custom firewall using iptables & pfense.

## Introduction

We can use either iptables(for linux) or pfsense(for FreeBSD-Based Systems) to create firewall. There are many other tools also for firewall.Like-
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






### Creating firewall using iptables:
As mentioned, iptables allows for deep customization and fine-grained control over `packet filtering`, `NAT`, and `connection tracking`.
[Link to NAT](#subfield-1)


















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
