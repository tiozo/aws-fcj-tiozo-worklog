---
title: "Week 11 Worklog"
weight: 11
chapter: false
pre: "<b> 1.11 </b>"
---

## Week 11 Objectives

- Learn Linux System Administration: User & Group Management, File Permissions, Process Management
- Study System Design fundamentals: Scalability, Load Balancing, Caching
- Practice Linux commands and shell scripting
- Participate in AWS Cloud Mastery Series #2

## Tasks to be Carried Out This Week

| Day | Tasks | Start Date | End Date | References |
|---|---|---|---|---|
| 1-2 | Learn Linux User Management: useradd, usermod, passwd, groups | 17/11/2025 | 18/11/2025 | Linux Documentation |
| 2-3 | Practice File Permissions: chmod, chown, umask, ACLs | 18/11/2025 | 19/11/2025 | Linux Admin Guide |
| 3-4 | Process Management: ps, top, htop, kill, systemctl | 19/11/2025 | 20/11/2025 | System Admin Handbook |
| 4-5 | System Design basics: Horizontal vs Vertical Scaling | 20/11/2025 | 21/11/2025 | System Design Primer |
| 5 | Join AWS Cloud Mastery Series #2 | 21/11/2025 | 21/11/2025 | AWS FCJ |

## Knowledge Acquired

### Linux System Administration

**User & Group Management:**
- Create and manage users with `useradd`, `usermod`, `userdel`
- Manage groups with `groupadd`, `groupmod`, `gpasswd`
- Understand `/etc/passwd`, `/etc/shadow`, `/etc/group`
- Configure sudo privileges and sudoers file

**File Permissions:**
- Understand permission model: read (r), write (w), execute (x)
- Use `chmod` with numeric and symbolic modes
- Ownership with `chown` and `chgrp`
- Special permissions: SUID, SGID, Sticky bit
- Access Control Lists (ACLs) with `setfacl` and `getfacl`

**Process Management:**
- View processes with `ps`, `top`, `htop`
- Background/foreground jobs with `&`, `fg`, `bg`
- Kill processes with `kill`, `killall`, `pkill`
- Service management with `systemctl`
- Monitoring with `vmstat`, `iostat`, `free`

### System Design Fundamentals

**Scalability Concepts:**
- Vertical Scaling (Scale Up): Increase resources of single server
- Horizontal Scaling (Scale Out): Add more servers
- Trade-offs between approaches

**Load Balancing:**
- Distribute traffic evenly across servers
- Algorithms: Round Robin, Least Connections, IP Hash
- Health checks and failover

**Caching:**
- Reduce database load and latency
- Cache levels: Browser, CDN, Application, Database
- Cache invalidation strategies

## Practical Work

### Shell Script: User Management Automation
```bash
#!/bin/bash
# Automate user creation from file

while IFS=, read -r username fullname; do
    useradd -m -c "$fullname" "$username"
    echo "$username:defaultpass" | chpasswd
    echo "Created user: $username"
done < users.txt
```

### System Monitoring Script
```bash
#!/bin/bash
# Monitor system resources

echo "=== System Resources ==="
echo "CPU Usage:"
top -bn1 | grep "Cpu(s)" | awk '{print $2}'

echo "Memory Usage:"
free -h | grep Mem | awk '{print $3 "/" $2}'

echo "Disk Usage:"
df -h / | tail -1 | awk '{print $5}'
```

## Achievements

- ✅ Mastered Linux user and permission management
- ✅ Understood process lifecycle and service management
- ✅ Learned System Design fundamentals
- ✅ Created shell scripts for task automation
- ✅ Participated in AWS Cloud Mastery Series #2

## Next Week Plan

- Continue System Design: Database design, CAP theorem
- Learn Linux Networking
- Practice with Docker and containerization
