# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

---

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap 192.168.1.110
```
![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/1-nmap.png)

This scan identifies the services below as potential points of entry:

**Target 1**
```
- Port 22/TCP   Open SSH
- Port 80/TCP   Open HTTP
- Port 111/TCP 	Open rcpbind
- Port 139/TCP 	Open netbios-ssn
- Port 445/TCP 	Open netbios-ssn
```

The following vulnerabilities were identified on each target:
**Target 1**
```
- Weak User Password
- WordPress - user enumeration and unsalted user password hash
- Escalation of privilege
```

---

### Exploitation

The Red Team was able to penetrate Target 1 and retrieve the following confidential data:

**Target 1**
**flag1** ```.b9bbcb33e11b80be759c4e844862482d```


- Target 1
  - `flag1.txt`: _TODO: Insert `flag1.txt` hash value_
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_
  - `flag2.txt`: _TODO: Insert `flag2.txt` hash value_
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_
