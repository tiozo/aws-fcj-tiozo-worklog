---
title: "Nhật ký tuần 11"
weight: 11
chapter: false
pre: "<b> 1.11 </b>"
---

## Mục Tiêu Tuần 11

- Học về Linux System Administration: User & Group Management, File Permissions, Process Management
- Tìm hiểu về System Design cơ bản: Scalability, Load Balancing, Caching
- Thực hành với Linux commands và shell scripting
- Tham gia AWS Cloud Mastery Series #2

## Nhiệm Vụ Thực Hiện Trong Tuần

| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày kết thúc | Tài liệu tham khảo |
|---|---|---|---|---|
| 1-2 | Học Linux User Management: useradd, usermod, passwd, groups | 17/11/2025 | 18/11/2025 | Linux Documentation |
| 2-3 | Thực hành File Permissions: chmod, chown, umask, ACLs | 18/11/2025 | 19/11/2025 | Linux Admin Guide |
| 3-4 | Process Management: ps, top, htop, kill, systemctl | 19/11/2025 | 20/11/2025 | System Admin Handbook |
| 4-5 | System Design basics: Horizontal vs Vertical Scaling | 20/11/2025 | 21/11/2025 | System Design Primer |
| 5 | Tham gia AWS Cloud Mastery Series #2 | 21/11/2025 | 21/11/2025 | AWS FCJ |

## Kiến Thức Đã Học

### Linux System Administration

**User & Group Management:**
- Tạo và quản lý users với `useradd`, `usermod`, `userdel`
- Quản lý groups với `groupadd`, `groupmod`, `gpasswd`
- Hiểu về `/etc/passwd`, `/etc/shadow`, `/etc/group`
- Phân quyền sudo và sudoers file

**File Permissions:**
- Hiểu permission model: read (r), write (w), execute (x)
- Sử dụng `chmod` với numeric và symbolic modes
- Ownership với `chown` và `chgrp`
- Special permissions: SUID, SGID, Sticky bit
- Access Control Lists (ACLs) với `setfacl` và `getfacl`

**Process Management:**
- Xem processes với `ps`, `top`, `htop`
- Background/foreground jobs với `&`, `fg`, `bg`
- Kill processes với `kill`, `killall`, `pkill`
- Service management với `systemctl`
- Monitoring với `vmstat`, `iostat`, `free`

### System Design Fundamentals

**Scalability Concepts:**
- Vertical Scaling (Scale Up): Tăng resources của single server
- Horizontal Scaling (Scale Out): Thêm nhiều servers
- Trade-offs giữa hai approaches

**Load Balancing:**
- Phân phối traffic đều giữa các servers
- Algorithms: Round Robin, Least Connections, IP Hash
- Health checks và failover

**Caching:**
- Giảm database load và latency
- Cache levels: Browser, CDN, Application, Database
- Cache invalidation strategies

## Thực Hành

### Shell Script: User Management Automation
```bash
#!/bin/bash
# Script tự động tạo users từ file

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

## Kết Quả Đạt Được

- ✅ Nắm vững Linux user và permission management
- ✅ Hiểu process lifecycle và service management
- ✅ Hiểu các khái niệm System Design cơ bản
- ✅ Viết được shell scripts để automate tasks
- ✅ Tham gia AWS Cloud Mastery Series #2

## Kế Hoạch Tuần Tiếp Theo

- Tiếp tục học System Design: Database design, CAP theorem
- Học về Networking trong Linux
- Thực hành với Docker và containerization
