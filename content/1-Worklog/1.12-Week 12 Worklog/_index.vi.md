---
title: "Nhật ký tuần 12"
weight: 12
chapter: false
pre: "<b> 1.12 </b>"
---

## Mục Tiêu Tuần 12

- Học về Linux Networking: TCP/IP, DNS, Firewall configuration
- Tìm hiểu System Design: Database patterns, Replication, Sharding
- Thực hành với iptables và network troubleshooting
- Tham gia AWS Cloud Mastery Series #3

## Nhiệm Vụ Thực Hiện Trong Tuần

| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày kết thúc | Tài liệu tham khảo |
|---|---|---|---|---|
| 1-2 | Linux Networking basics: ifconfig, ip, netstat, ss | 24/11/2025 | 25/11/2025 | Linux Network Admin |
| 2-3 | Firewall configuration: iptables, ufw, firewalld | 25/11/2025 | 26/11/2025 | Security Guide |
| 3-4 | System Design: Database Replication & Sharding | 26/11/2025 | 27/11/2025 | System Design Interview |
| 4-5 | CAP Theorem và Consistency patterns | 27/11/2025 | 28/11/2025 | Distributed Systems |
| 5 | Tham gia AWS Cloud Mastery Series #3 | 28/11/2025 | 28/11/2025 | AWS FCJ |

## Kiến Thức Đã Học

### Linux Networking

**Network Configuration:**
- Xem network interfaces với `ip addr`, `ifconfig`
- Configure static IP và DHCP
- Routing table với `ip route`, `route`
- DNS configuration trong `/etc/resolv.conf`

**Network Troubleshooting:**
- Test connectivity với `ping`, `traceroute`
- Check ports với `netstat`, `ss`, `lsof`
- Packet analysis với `tcpdump`
- Network performance với `iperf`, `netcat`

**Firewall Management:**
- iptables rules: INPUT, OUTPUT, FORWARD chains
- Basic firewall với `ufw` (Uncomplicated Firewall)
- Service-based firewall với `firewalld`
- Port forwarding và NAT

### System Design: Database Patterns

**Database Replication:**
- Master-Slave replication: Write to master, read from slaves
- Master-Master replication: Multiple write nodes
- Benefits: High availability, read scalability
- Challenges: Replication lag, consistency

**Database Sharding:**
- Horizontal partitioning: Split data across multiple databases
- Sharding strategies: Range-based, Hash-based, Directory-based
- Benefits: Improved performance, scalability
- Challenges: Complex queries, rebalancing

**CAP Theorem:**
- Consistency: All nodes see same data
- Availability: System always responds
- Partition Tolerance: System works despite network failures
- Trade-off: Can only guarantee 2 out of 3

**Consistency Patterns:**
- Strong Consistency: Immediate consistency across all nodes
- Eventual Consistency: Eventually all nodes will be consistent
- Weak Consistency: No guarantee of consistency

## Thực Hành

### Network Monitoring Script
```bash
#!/bin/bash
# Monitor network connections

echo "=== Active Connections ==="
ss -tuln | grep LISTEN

echo -e "\n=== Network Statistics ==="
netstat -i

echo -e "\n=== Bandwidth Usage ==="
ifconfig | grep "RX packets\|TX packets"
```

### Firewall Setup Script
```bash
#!/bin/bash
# Basic firewall configuration

# Flush existing rules
iptables -F

# Default policies
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow loopback
iptables -A INPUT -i lo -j ACCEPT

# Allow established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow HTTP/HTTPS
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

echo "Firewall configured"
```

### System Design: Simple Load Balancer Concept
```python
# Conceptual load balancer implementation
class LoadBalancer:
    def __init__(self, servers):
        self.servers = servers
        self.current = 0
    
    def round_robin(self):
        """Round-robin algorithm"""
        server = self.servers[self.current]
        self.current = (self.current + 1) % len(self.servers)
        return server
    
    def least_connections(self, connections):
        """Least connections algorithm"""
        return min(self.servers, key=lambda s: connections[s])
```

## Kết Quả Đạt Được

- ✅ Hiểu về Linux networking và troubleshooting
- ✅ Cấu hình được firewall với iptables
- ✅ Nắm vững database replication và sharding patterns
- ✅ Hiểu CAP theorem và consistency trade-offs
- ✅ Tham gia AWS Cloud Mastery Series #3

## Kế Hoạch Tuần Tiếp Theo

- Học về Message Queues và Event-Driven Architecture
- Thực hành với Docker và container orchestration
- Tìm hiểu về Microservices architecture patterns
