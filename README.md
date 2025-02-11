# network-troubleshooting

# Network Security and Troubleshooting Guide

## Introduction
This guide provides a **beginner-to-advanced** approach to **network security, troubleshooting, performance optimization, and firewall testing** using Netcat, TCPdump, Wireshark, and nslookup. It includes real-world **scenarios**, **step-by-step explanations**, and **best practices** to help both new and experienced system administrators and security engineers.

---
## Table of Contents
1. [Understanding Network Security and Tools](#understanding-network-security-and-tools)
2. [Network Security Investigation](#network-security-investigation)
3. [Network Performance Monitoring](#network-performance-monitoring)
4. [Firewall Testing and Hardening](#firewall-testing-and-hardening)
5. [Best Practices](#best-practices)
6. [Tools Used](#tools-used)

---

## Understanding Network Security and Tools
Before diving into hands-on exercises, it's important to understand **why** we use certain tools and **how** they help with network security.

- **What is Network Security?**
  - Network security involves protecting data, applications, and infrastructure from unauthorized access, threats, and attacks.
- **Why is Network Security Important?**
  - Prevents unauthorized data access
  - Protects confidential information
  - Ensures availability of critical services
- **How do these tools help?**
  - **Netcat**: Used for network connection testing, port scanning, and sending/receiving data.
  - **TCPdump**: Captures and analyzes network packets in real time.
  - **Wireshark**: Provides deep network traffic inspection and visualization.
  - **nslookup**: Resolves domain names to IP addresses and checks DNS configurations.

---

## Network Security Investigation

### Scenario: Investigating a Security Breach
**Problem:** You suspect unauthorized network activity and need to investigate traffic logs and determine potential security threats.

### **Step 1: Collect System Logs**
Why? System logs contain records of all authentication attempts, failed logins, and possible breaches.

Check for unauthorized access attempts:
```sh
sudo cat /var/log/auth.log | grep "Failed password"
```
Check for successful unauthorized logins:
```sh
sudo cat /var/log/auth.log | grep "Accepted password"
```
Block suspicious IPs immediately:
```sh
sudo iptables -A INPUT -s <IP_ADDRESS> -j DROP
```

### **Step 2: Capture Live Traffic with TCPdump**
Why? Capturing real-time packets can help detect suspicious activities like brute-force login attempts or data exfiltration.

Monitor SSH connections:
```sh
sudo tcpdump -i eth0 port 22
```
Capture packets from a specific source:
```sh
sudo tcpdump -i eth0 src 192.168.1.200 -w suspicious.pcap
```

### **Step 3: Analyze Suspicious Traffic in Wireshark**
Why? Wireshark allows for deep packet inspection, making it easier to visualize and detect abnormal traffic patterns.

Open `.pcap` in Wireshark and apply filters:
- Detect brute-force attempts:
  ```plaintext
  ssh
  tcp.flags.syn == 1 && tcp.flags.ack == 0
  ```
- Detect data exfiltration:
  ```plaintext
  ip.src == 192.168.1.100 && ip.dst != internal_network
  ```

### **Step 4: Investigate DNS Spoofing (nslookup & dig)**
Why? Attackers may redirect DNS queries to malicious servers to intercept sensitive data.

Compare DNS responses from different servers:
```sh
nslookup example.com 8.8.8.8
nslookup example.com 1.1.1.1
```
Check unauthorized DNS changes:
```sh
dig example.com +trace
```
Fix DNS security issues:
```sh
dig example.com +dnssec
```

---

## Network Performance Monitoring

### Scenario: Diagnosing Network Latency and Packet Loss
**Problem:** Users report slow network performance. You need to identify the cause and optimize network traffic.

### **Step 1: Measure Latency with Netcat & Ping**
Test connection response time:
```sh
time nc -zv 192.168.1.1 80
```
Compare with ICMP ping latency:
```sh
ping -c 5 192.168.1.1
```
Optimize TCP settings:
```sh
sudo sysctl -w net.ipv4.tcp_window_scaling=1
```

### **Step 2: Identify Packet Loss with TCPdump**
Monitor retransmissions:
```sh
sudo tcpdump -i eth0 'tcp[13] & 0x4!=0'
```
Check retransmitted segments:
```sh
sudo netstat -s | grep "segments retransmitted"
```

### **Step 3: Diagnose Bandwidth Usage in Wireshark**
Filter high-traffic sources:
```plaintext
ip.src == 192.168.1.100
```
Detect high-usage websites:
```plaintext
http.request
```

---

## Firewall Testing and Hardening

### Scenario: Assessing Firewall Rules and Securing the Network
**Problem:** You need to confirm if the firewall is blocking unauthorized traffic.

### **Step 1: Test Open Ports with Netcat**
Scan for open ports:
```sh
nc -zv 192.168.1.100 1-65535
```
Check if a firewall is blocking SSH:
```sh
nc -v 192.168.1.100 22
```
Block unwanted open ports:
```sh
sudo ufw deny 23/tcp
sudo ufw deny 3389/tcp
sudo ufw enable
```

### **Step 2: Harden the Firewall**
Enable logging:
```sh
sudo ufw logging on
```
Limit SSH access to trusted IPs:
```sh
sudo ufw allow from 192.168.1.0/24 to any port 22
```

---

## Tools Used
### **Netcat**
- Used for scanning, testing, and connecting to remote ports.
- Example usage:
  ```sh
  nc -zv 192.168.1.100 80
  ```

### **TCPdump**
- Packet capturing tool for analyzing network traffic.
- Example usage:
  ```sh
  sudo tcpdump -i eth0 port 22
  ```

### **Wireshark**
- GUI-based network protocol analyzer for deep packet inspection.
- Example filters:
  ```plaintext
  tcp.flags.syn == 1 && tcp.flags.ack == 0
  ```

### **nslookup**
- DNS lookup tool for querying domain name servers.
- Example usage:
  ```sh
  nslookup google.com
  ```

---

## Conclusion
This guide provides a detailed step-by-step approach to **network security monitoring and troubleshooting**. By understanding **why** and **how** these tools work, you can better secure and optimize your network infrastructure.

For contributions and improvements, feel free to open a pull request on GitHub!

---

## Author
Cloud & DevOps Engineer - Specialized in Kubernetes, Terraform, CI/CD, and Security.

