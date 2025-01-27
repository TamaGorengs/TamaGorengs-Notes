# LinuxPrivEsc Enumeration

## Network

Check open ports

```bash
netstat -ano
ss -lt
```

## File and Directory

Find specific strings in file

```
grep -rnw '/path/to/somewhere/' -e 'pattern'
```

Find file name

```
find / -name "filename"
```

## Privilege Escalation

### Manual Enumeration

check for group that can be abuse

```
id
```

Check if can write or read, or maybe even find hashes

```
/etc/passwd
```

```
hostname
```

Check version for kernel exploit

```
cat /etc/issue
cat /etc/os-release
uname -a
```

Check process running that can be abused

```
ps aux
```

Check for network configuration like internal ip

```
ip a
ifconfig / ipconfig
routel
route
```

Look for active network

```
netstat ano
ss -anp
```

Check firewall rules

```
cat /etc/iptables/rules.v4
```

Check for schedule task

```
ls -lah /etc/cron*
crontab -l
sudo crontab -l (could be there schedule task for sudo)
```

Check application installed

```
dpkg -l
```

Look for writable directories

```
find / -writable -type d 2>/dev/null
```

Look for unmounted drives

```
cat /etc/fstab
mount
lsblk
lsmod
/sbin/modinfo libata
```

Checked for SUID Binaries

```
find / -perm -u=s -type f 2>/dev/null
```

### Automated Enumeration

LinPEAS
