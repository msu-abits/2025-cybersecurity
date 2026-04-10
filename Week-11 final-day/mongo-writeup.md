# Mongobleed Write-Up

[Mongobleed Lab](https://app.hackthebox.com/sherlocks/MangoBleed?tab=play_sherlock)

## Scenario
You were contacted early this morning to handle a high‑priority incident involving a suspected compromised server. The host, mongodbsync, is a secondary MongoDB server. According to the administrator, it's maintained once a month, and they recently became aware of a vulnerability referred to as MongoBleed. As a precaution, the administrator has provided you with root-level access to facilitate your investigation.

You have already collected a triage acquisition from the server using UAC. Perform a rapid triage analysis of the collected artifacts to determine whether the system has been compromised, identify any attacker activity (initial access, persistence, privilege escalation, lateral movement, or data access/exfiltration), and summarize your findings with an initial incident assessment and recommended next steps.

## Step 1: Gathering information
Going into this you might not be too familiar with "mongobleed" So, lets find out what its about! and answer the first two tasks.

<img width="700" height="500" alt="image" src="https://github.com/user-attachments/assets/93352e7a-f9ed-427a-8e9c-08d174735da8" />

Without trusting the AI at the top, we can see a CVE number. This will likely be super helpful in finding non-advertisment data about this vulnerabiltiy:

`CVE-2025-14847`

Coincidentally this is the answer to: 

`Task 1: What is the CVE ID designated to the MongoDB vulnerability explained in the scenario?`

Now we know the CVE lets see what we can find about this vulnerability. a google of the number gets us to [NIST's listing of the vulerability](https://nvd.nist.gov/vuln/detail/CVE-2025-14847)

And its description states:
> Mismatched length fields in Zlib compressed protocol headers may allow a read of uninitialized heap memory by an unauthenticated client. This issue affects all MongoDB Server v7.0 prior to 7.0.28 versions, MongoDB Server v8.0 versions prior to 8.0.17, MongoDB Server v8.2 versions prior to 8.2.3, MongoDB Server v6.0 versions prior to 6.0.27, MongoDB Server v5.0 versions prior to 5.0.32, MongoDB Server v4.4 versions prior to 4.4.30, MongoDB Server v4.2 versions greater than or equal to 4.2.0, MongoDB Server v4.0 versions greater than or equal to 4.0.0, and MongoDB Server v3.6 versions greater than or equal to 3.6.0.

That is an awful lot of confusing veriage. its hard to understand what this truely means. Now the fun part is, we have AI to help us break this down!

<img width="663" height="688" alt="image" src="https://github.com/user-attachments/assets/a4bf5562-c632-42d8-9d61-2b694fc4fb91" />

Great, it appears we got a better explanation of this vulnerability.

It sends a request to the server that is compressed, however, the requestor can supply the length of the request. So even though your request will be 100 bytes, you could request the server to send 99999 bytes back. This would end up leaking additional information form the server you would not normally be able to see.
Armed wtih this information, we can now actually work on the rest!

`Task 2: What is the version of MongoDB installed on the server that the CVE exploited?`
Look back at [https://nvd.nist.gov/vuln/detail/CVE-2025-14847](https://nvd.nist.gov/vuln/detail/CVE-2025-14847) you will find it there.

`Task 3: Analyze the MongoDB logs to identify the attacker’s remote IP address used to exploit the CVE.`
Now we finally should download and unzip the files we were provided of the database server, we can hopefully identify this attacker. keep in mind these are encrypted and require the password. you can copy it from that page.

Then look into the [root]/var/logs/mongodb/mongod.log This will show us entries of the server. Lets exercise some of the searching in linux we have been chatting about this week
`cd [root]/var/logs/mongodb/`
`cat mongod.log`
This is a lot of text, lets try and filter it down to see the categories. we will use grep to search for specific text!
`cat mongod.log | grep "CONTROL"`
This actually doesn't look useful in our case, lets try the NETWORK one instead
`cat mongod.log | grep "NETWORK"`
Greaet this looks a lot more useful.
`cat mongod.log | grep "Connection accepted"`
Now we have filtered only accepted connections. Lets take a look and see who is requesting data from our database!
`cat mongod.log | grep "Connection accepted" | cut -d "," -f6 | cut -d : -f2 | uniq -c`
All from the single address 65.0.76.43 it appears. over 37630 connections. That is pretty sus!

Putting in the addres, thats the answer!

### Alternative solution
https://github.com/Neo23x0/mongobleed-detector

`Task 4 Based on the MongoDB logs, determine the exact date and time the attacker’s exploitation activity
began (the earliest confirmed malicious event`
We alredy found the logs, all we have to do is grab the earliest entry with head!
`cat mongod.log | grep "65.0.76.43" | head`
You can confirm this date!

`Task 6: The attacker gained remote access after a series of brute‑force attempts. The attack likely exposed
sensitive information, which enabled them to gain remote access. Based on the logs, when did the
attacker successfully gain interactive hands-on remote access?`
- This sounds like ssh logs! Lets exercse yet again more searching

` cd ../`
` cat auth.log | grep "65.0.76.43"`
Yikes! they weren't lying they got access! You can find at the bottom the acceppted login requested.

`task 7: Identify the exact command line the attacker used to execute an in‑memory script as part of their
privilege‑escalation attempt`
We need to look at command history here! Lets find this user they logged into and see what they ran
`cd ../../home/mongoadmin`
`cat .bash_history`
There is a script download! Uh oh!

`Task 8 - The attacker was interested in a specific directory and also opened a Python web server, likely for
exfiltration purposes. Which directory was the target?`
Same file.


Thats a wrap!
