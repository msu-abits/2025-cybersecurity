# Web Server and Authentication Log Analysis Guide

## Introduction

System logs are crucial for understanding server behavior, diagnosing issues, and detecting security threats. This guide covers two essential log types: **web server access logs** and **authentication logs**. Understanding these logs is fundamental for system administrators, security analysts, and DevOps engineers.

## 1. Web Server Access Log Analysis

### Log Format Structure

Web server access logs typically follow the **Common Log Format (CLF)** or **Extended Log Format**. Each line represents a single HTTP request.

**Standard Format:**
```
IP_Address - - [Timestamp] "HTTP_Method /path HTTP/Version" Status_Code Response_Size "Referer" "User_Agent"
```

### Field Breakdown

| Field | Example | Description |
|-------|---------|-------------|
| **IP Address** | `10.0.2.2` | Client IP making the request |
| **Identity** | `-` | Usually unused (RFC 1413 identity) |
| **User** | `-` | Authenticated username (if any) |
| **Timestamp** | `[25/Sep/2025:04:09:10 +0000]` | Date/time in square brackets |
| **HTTP Request** | `"GET / HTTP/1.1"` | Method, path, and protocol version |
| **Status Code** | `200` | HTTP response status |
| **Response Size** | `946` | Bytes sent (excluding headers) |
| **Referer** | `"http://localhost:8080/"` | Previous page URL |
| **User Agent** | `"Mozilla/5.0..."` | Browser/client information |

### Example Log Entry Analysis

```
10.0.2.2 - - [25/Sep/2025:04:09:10 +0000] "GET / HTTP/1.1" 200 946 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:143.0) Gecko/20100101 Firefox/143.0"
```

**Analysis:**
- **Client**: 10.0.2.2 (internal network)
- **Request**: GET request for homepage (/)
- **Success**: HTTP 200 status (successful)
- **Response**: 946 bytes sent
- **Browser**: Firefox 143.0 on Linux

### Key Insights to Extract

#### 1. Traffic Patterns
Look for:
- Peak usage times
- Popular endpoints (`/api/users`, `/products`)
- Geographic distribution of IPs

#### 2. Performance Issues
Monitor:
- **Slow endpoints**: Look for `/slow` requests
- **Large responses**: High byte counts
- **Error rates**: 4xx and 5xx status codes

#### 3. Security Concerns

**From the sample logs, notice these suspicious patterns:**

```
10.0.2.2 - - [25/Sep/2025:04:12:24 +0000] "GET /cmd?whoami HTTP/1.1" 404 490
10.0.2.2 - - [25/Sep/2025:04:12:38 +0000] "GET /cmd=?sudo%20apt%20install%20virus HTTP/1.1" 404 490
```

**Red Flags:**
- Command injection attempts (`/cmd?whoami`)
- Malicious URLs with system commands
- Multiple 403 (Forbidden) responses to `/admin`
- Unusual user agents or missing referers

#### 4. Bot Activity
```
10.0.2.2 - - [25/Sep/2025:04:09:10 +0000] "GET /favicon.ico HTTP/1.1" 404 489
10.0.2.2 - - [25/Sep/2025:04:12:06 +0000] "GET /robots.txt HTTP/1.1" 404 490
```

Common bot requests:
- `/robots.txt` - SEO crawlers
- `/favicon.ico` - Browser requests
- Missing standard files (404 errors)

## 2. Authentication Log Analysis

### Log Format Structure

Authentication logs (`auth.log` on Ubuntu/Debian, `secure` on RHEL/CentOS) track user authentication events, privilege escalation, and session management.

**Standard Format:**
```
Date Time Hostname Process[PID]: Message
```

### Field Breakdown

| Field | Example | Description |
|-------|---------|-------------|
| **Date** | `Sep 25 04:06:44` | Month, day, time |
| **Hostname** | `ubuntu-jammy` | Server hostname |
| **Process** | `sshd[801]` | Service name and process ID |
| **Message** | `Server listening on 0.0.0.0 port 22` | Event description |

### Key Event Types

#### 1. User Management
```
Sep 25 04:06:44 ubuntu-jammy useradd[671]: new user: name=ubuntu, UID=1001, GID=1001
Sep 25 04:06:44 ubuntu-jammy passwd[679]: password for 'ubuntu' changed by 'root'
```

**Look for:**
- New user creation
- Password changes
- Group modifications
- Account lockouts

#### 2. SSH Authentication
```
Sep 25 04:06:46 ubuntu-jammy sshd[806]: Accepted publickey for vagrant from 10.0.2.2 port 33738
Sep 25 04:07:23 ubuntu-jammy sshd[1201]: Connection reset by 10.0.2.2 port 42756 [preauth]
```

**Monitor:**
- **Successful logins**: `Accepted publickey/password`
- **Failed attempts**: `Failed password`, `Invalid user`
- **Connection issues**: `Connection reset [preauth]`
- **Brute force patterns**: Multiple failed attempts from same IP

#### 3. Privilege Escalation
```
Sep 25 04:06:49 ubuntu-jammy sudo: vagrant : PWD=/home/vagrant ; USER=root ; COMMAND=/usr/bin/bash -l
```

**Critical information:**
- **User**: Who ran sudo
- **Command**: What command was executed
- **Directory**: Where the command was run (PWD)
- **Target user**: Usually root

#### 4. Session Management
```
Sep 25 04:06:46 ubuntu-jammy systemd-logind[705]: New session 1 of user vagrant
Sep 25 04:06:50 ubuntu-jammy systemd-logind[705]: Removed session 1
```

Track:
- Session creation and termination
- User login/logout patterns
- Session duration

### Security Analysis from Sample Logs

#### 1. Rapid Sudo Usage Pattern
```
Sep 25 04:06:51 ubuntu-jammy sudo: vagrant : PWD=/home/vagrant ; USER=root ; COMMAND=/usr/bin/bash -l
Sep 25 04:06:51 ubuntu-jammy sudo: pam_unix(sudo:session): session closed for user root
Sep 25 04:06:51 ubuntu-jammy sudo: vagrant : PWD=/home/vagrant ; USER=root ; COMMAND=/usr/bin/bash -l
```

**Analysis**: Multiple rapid sudo commands might indicate:
- Automated script execution
- System setup/configuration
- Potential compromise (if unexpected)

#### 2. Service Activities
```
Sep 25 04:08:06 webserver-logs groupadd[4792]: group added to /etc/group: name=ssl-cert, GID=121
Sep 25 04:09:01 webserver-logs CRON[12058]: pam_unix(cron:session): session opened for user root
```

**Normal operations:**
- Group creation for services
- Cron job execution
- Service startups

## 3. Practical Analysis Techniques

### Command Line Tools

#### Basic Log Inspection
```bash
# View recent access log entries
tail -f /var/log/apache2/access.log

# Count status codes
awk '{print $9}' access.log | sort | uniq -c | sort -rn

# Find top IPs
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10

# Look for suspicious patterns
grep -E "(cmd|exec|system|eval)" access.log
```

#### Authentication Log Analysis
```bash
# Failed SSH attempts
grep "Failed password" /var/log/auth.log

# Successful logins
grep "Accepted" /var/log/auth.log

# Sudo usage
grep "sudo:" /var/log/auth.log

# User creation/modification
grep -E "(useradd|usermod|passwd)" /var/log/auth.log
```

### Correlation Techniques

#### 1. Timeline Correlation
Match events across logs:
- Web attacks in access logs
- Corresponding auth failures in auth logs
- System changes after suspicious activity

#### 2. IP Address Tracking
- Cross-reference IPs between logs
- Identify persistent attackers
- Map attack patterns

#### 3. User Behavior Analysis
- Normal vs. abnormal login patterns
- Privilege escalation after suspicious web requests
- Off-hours activity

## 4. Common Security Indicators

### Web Server Red Flags
- **SQL Injection**: `'`, `UNION`, `SELECT` in URLs
- **XSS Attempts**: `<script>`, `javascript:` in parameters  
- **Directory Traversal**: `../`, `..%2F` patterns
- **Command Injection**: `cmd`, `exec`, `system` parameters
- **Brute Force**: Repeated 401/403 responses

### Authentication Red Flags
- **Multiple failed logins** from single IP
- **Impossible travel**: Logins from distant locations rapidly
- **Off-hours activity** outside normal business hours
- **Privilege escalation** without business justification
- **New user creation** during incidents

## 5. Best Practices for Log Analysis

### 1. Establish Baselines
- Normal traffic patterns
- Typical user behavior
- Standard error rates
- Common file requests

### 2. Automate Monitoring
- Set up alerts for suspicious patterns
- Monitor failed authentication attempts
- Track unusual traffic spikes
- Alert on privilege escalation

### 3. Retention and Storage
- Keep logs for sufficient time (90+ days)
- Centralize logs for correlation
- Compress old logs to save space
- Back up critical logs

### 4. Regular Review
- Daily review of security events
- Weekly traffic pattern analysis
- Monthly user access reviews
- Quarterly baseline updates

## 6. Tools and Resources

### Log Management Tools
- **ELK Stack** (Elasticsearch, Logstash, Kibana)
- **Splunk** for enterprise environments
- **Graylog** for centralized logging
- **Fluentd** for log forwarding

### Security Analysis Tools
- **OSSEC** for host intrusion detection
- **Fail2ban** for automated blocking
- **Logwatch** for daily summaries
- **GoAccess** for real-time web analytics

## Conclusion

Effective log analysis requires understanding both the technical format and the business context of your systems. Start with basic pattern recognition, gradually building more sophisticated analysis techniques. Remember that logs tell a story - your job is to read between the lines and identify when that story indicates a problem.

The key to successful log analysis is consistency, automation where possible, and maintaining awareness of your baseline normal behavior. With practice, you'll develop an intuition for spotting anomalies and potential security issues before they become major incidents.