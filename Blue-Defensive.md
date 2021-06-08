# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further


### Network Topology

The following machines were identified on the network:
- Kali
  - **Operating System**: Debian Kali
  - **Purpose**: The Penetration Tester
  - **IP Address**: 192.168.1.90
- Target 1
  - **Operating System**: Debian Linux
  - **Purpose**: The WordPress Host
  - **IP Address**: 192.168.1.100
- Target 2
  - **Operating System**: Debian Linux
  - **Purpose**: Optional Attack Target
  - **IP Address**: 192.168.1.115
- Capstone
  - **Operating System**: Linux
  - **Purpose**: Virtual Machine
  - **IP Address**: 192.168.1.105
- Elk
  - **Operating System**: Ubuntu
  - **Purpose**: Elasticsearch / Kibana / Logstash
  - **IP Address**: 192.168.1.100


### Network Map

![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/NetworkMap.png)

---

### Description of Targets

The target of this attack was: `Target 1 192.168.1.110`

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors
Alert 1 is implemented as follows:
  - **Metric**: `http.response.status_code`
  - **Threshold**: `IS ABOVE 400 FOR THE LAST 5 minutes`
  - **Vulnerability Mitigated**: `By creating an alert, the security team can identify attacks & block the IP, change the password and close port 22`
  - **Reliability**: `No, this alert typically does not generate many false positives. This alert is highly reliable in identifying brute force attacks.`


![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/b-1-http.png)

---

#### HTTP Request Size Monitor
Alert 2 is implemented as follows:
  - **Metric**: `http.request.bytes`
  - **Threshold**: `IS ABOVE 3500 FOR THE LAST 1 minute`
  - **Vulnerability Mitigated**: `By controlling the number of http request size through a filter, it helps protect against DDOS attacks.`
  - **Reliability**: `The alert could throw false positives. It is a medium when it comes to reliability. The possibility of larger non-malicious HTTP requests or legitimate HTTP traffice is still there.`


![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/b-2-size.png)

---

#### CPU Usage Monitor
Alert 3 is implemented as follows:
  - **Metric**: `system.process.cpu.total.pct`
  - **Threshold**: `IS ABOVE 0.5 FOR THE LAST 5 minutes`
  - **Vulnerability Mitigated**: `By controlling the CPU usage at 50% this alert will trigger a memory dump of stored information.`
  - **Reliability**: `This is a highly reliable alert. Even if there is not any malicious programs running, this can help determine where to improve CPU usage.`


![](https://github.com/jamesdewhirst/FinalProject/blob/main/Images/b-3-CPU.png)

---

### Suggestions for Going Further

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Excessive HTTP Errors
  - **Patch**:
```
    WordPress Hardening
    - Implement regular updates to WordPress
      - WordPress Core
      - PHP Version
      - Plugins
    - Install Security Plugin(s)
      - Ex. Wordfence (adds security functionality)
    - Disable unused WordPress features and settings such as:
      - WordPress XML-RPC (on by default)
      - WordPress REST API (on by defalt)
    - Block requests to `/?author=` by confuguring web server settings
    - Remove WordPress logins from being publicly accessible specifically:
      - `/wp-admin`
      - `/wp-login.php`
```

- **Why It Works**: ``
- Vulnerability 2
  - **Patch**: ``
  - **Why It Works**: ``
- Vulnerability 3
  - **Patch**: ``
  - **Why It Works**: ``
