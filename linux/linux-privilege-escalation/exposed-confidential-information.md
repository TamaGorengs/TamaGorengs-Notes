# Exposed Confidential Information

Look for user environment

```
env
```

Enumerate Process with pass

```
watch -n 1 "ps -aux | grep pass"
```

Use TCPDump to capture traffic (require root permission)

```
sudo tcpdump -i lo -A | grep "pass"
```
