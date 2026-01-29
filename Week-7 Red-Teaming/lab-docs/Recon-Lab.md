# Red Teaming: Reconnisance Lab
This lab is intended to assist in taking the first step in the cyber kill chain to understand what an attackers perspecive looks like. This will be done using the lab that is setup in the lab-setup.md document written in this same directory.

## Objectives
- Why do we recon?
- Introduce NMAP

---
## Written Lesson

### Why do we Recon?
Reconnisance is the first stage of the Cyber Kill Chain, a set of steps developed by cybersecurity professionals to help security analysts get the bigger picture of a common attack chain. 

[Cyber Kill Chain Image Here]()

---
## PREWORK
See the lab-setup document to help you in setting up your lab. This will assume you have already installed and started both virtual machines,

> Kali Linux is commonly refered to as the "hackers linux" it takes the base operating system of debian (so it will seem close to ubuntu) but adds tons of hacking tools and resources pre-built in. It works great as a quick machine to deploy and test different hacking utilities.

1. Log into the Kali Virtual Machine

> After succesfully logging in and launching the terminal, we will proceed to the first step of locating the IP of this machine

2. Locate and log the IP of the Kali VM
In order to understand the environment our VM is in, we need to look at where we currently are and how we can reach our target. For our example, we will already have an idea on where the target is located and can do a more targeted scan. It is possible to do a wider scan of a network range, like 192.168.1.0/24 but we will focus on a single target to ensure our scans are quick.

```sh
ip addr
```

3. Gather the IP from the target machine
Switch over to the target vm logging in with the credentials of `msustudent:$tudent1` this should provide you the desktop, navigate to the terminal and type the same command as before.

```sh
ip addr
```

Take the ip (eg. 192.168.1.2) and write it down. you will need this for the future steps

4. Ensure you can reach the target
After establishing what IP our target is at, we might want to investigate if we can actually reach the server at the end. To do this, we will send a ping request to the host and ensure that we can contact it.

FROM KALI -> Target
```sh
ping <Target Linux IP address>
```

If you recieve a message back congrats! you can reach your VM.
If you do not recieve a message back from your VM see: [Virtual Box Networks Guide](https://www.nakivo.com/blog/virtualbox-network-setting-guide/) to find out more details about how networking with VMs work and ensure they both have access to the same internet.
- Best bet is to setup a "host only" OR "bridged" networking that will allow you to find the IP easily. "NAT" will _NOT_ work for this use case.

5. Run your first scan
NMAP is an important tool in any ethical hacker's toolset, it allows them to find out what might be hosted on the host. The tool will loop through network ports (services on the host) and request a message back, if it recieves the message it will be marked as open, if no response is made it is a closed port.

There are many different kinds of scans, from TCP SYN (Default), to TCP ACK, to even UDP scans (if you aren't aware of these kinds of terms now, you will learn more in networking class). We will keep mostly default settings as we don't need to stray very far, except for adding some discovery of services to the mix.

For more about NMAP see: https://www.geeksforgeeks.org/linux-unix/what-is-nmap-a-comprehensive-guide-for-network-mapping/ 

**NMAP HELP MENU (See here for switches)**
```sh
nmap -h
```

We will look at what these ports can tell us soon, for now lets get the scan started.
```sh
nmap <Target Linux IP address>
```

When the scan finishes we can see the following ports that are responding to us:
```markdown:
- 80: HTTP
- 22: SSH
- 23: Telnet
```

6. Finding the open services
However, we know they are open but don't know what is being hosted! NMAP can help with that by doing "service detection" that will attempt to gather information about the service that is being hosted on the system
```sh
nmap -o -sV <Target Linux IP address>
```
> -sV stands for the service scanning option avalible in NMAP
> -o does OS Discovery, it estimates based on the open ports what the server might be

We now know on port 22 is an Open SSH Server,
and on port 80 is an apache server. Telnet also speaks out to me here since I know of [CVE-2026-24061](https://nvd.nist.gov/vuln/detail/CVE-2026-24061)

> NMAP will scan the most common ports up to port 1024. However 65535 different ports exist and can be used for multiudes of different purposes

7. Gathering information about the web server
A common term given to information gathering in a web server is called "banner grabbing" its the act of taking the text returned first in a http response and trying to get data from it to see if we can find out if the server is running older versions of software or another vulnerability. to do this we will use: 

```sh
nc <target> 80
```
This will give you basic info by connecting you to a text line!

we can see this server is actually the homepage for our company we are engaging Blume Corportaion. It appears to be running apache.

8. Integrating OSINT
Now we have all the versions here is best to integrate OSINT and try to look into the versions installed. Google the version of the software mentioned. Google the version number and software name and include the key term "CVE"

Did you find any vulnerabilities? anything critical? Keep this in mind

---

## Conclusion
This concludes the lab on reconnisance. With us knowing about SSH. we will move on and use a shortened password list in an attempt to brute force the SSH on this server.
