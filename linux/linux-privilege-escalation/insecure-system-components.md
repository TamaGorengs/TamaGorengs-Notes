# Insecure System Components

### Abusing Setuid Binaries and Capabilities

Check for SUID

```
find / -perm -u=s -type f 2>/dev/null
```

Check capabilities

```
/usr/sbin/getcap -r / 2>/dev/null
```

### Abusing Sudo

```
sudo -l
```

### Abusing Kernel

```
cat /etc/issue
uname -r
arch
```
