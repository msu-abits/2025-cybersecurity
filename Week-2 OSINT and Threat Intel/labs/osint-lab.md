# Examining Red team OSINT Usage
This is a short lab that looks at how a red teamer can use and find knowledge on a target.
You should view the slides for context on images referenced in this document.

## How do Hackers use OSINT?
Hackers use osint to help them get the larger picture of their target or even potentially find publically exposed information about a company. This can answer questions like: "Who works at the company?", "What kind of software do they use?", etc

The first example in the presentation uses a picture stating a security analyst from missouri state university. However, the skills section of this individual has given too much information about their company when trying to advertise their skills. 
This in-advertant information disclosure has allowed us to find out that they have recently shrunk their security team and are dealing with hardware that is severely out of date. 
As an attacker, this would allow them to understand where they should be targeting in their scans and where key entry points may lie within their organization. These skills might also be abused in a "Whaling" attack otherwise refered to as targeted spearphishing.

The next slide provides a screenshot of Google's Company page, showing more information about where different individuals of a company resides. This is important for any red teamer to see for in-advertant disclosures and potential phishing targets.

***

## Alright then, how is this done?

1. Finding accounts based off a single username.
For our example, we have identified a specific target that we are wishing to attempt to locate accounts for. There exists a tool for this specific purpose. WhatsMyName Web allows an indivual to search 697 websites online for a specific username.
The website works by taking the username and placing it in the url. Like how msu-abits can be found at github.com/msu-abits/ you can find accounts on many other platforms with this method.

You may use: "msuabits" to find our instagram!

https://www.whatsmyname.app/

---

## Video: Social Media Osint - Scary geo-location
![Social media Osint Video](https://www.youtube.com/watch?v=HORzekIiZZ0)

---

## Defenders + Osint
OSINT in defender context commonly refers to using external sources to assist with investigations. This might be a website that provides community-driven information about a malware file, to simple forms of cybersecurity professionals. 
The most common of these tools would be Virus Total. Virus Total allows the analysis of a file against many anti-virus engines and will publicly post it's potential to be malware. Its a very good tool in finding community context surrounding a file.

Paste this hash into: https://www.virustotal.com/gui/
```
0e0d4494780c9010ece88f39f65bfbfcb13236e1652f7fe41e9c84a5b16583a5​
```

There are also tools like shodan, see this video for a use of the tool in finding open security cameras to the internet: 
![Shodan Demo](https://www.youtube.com/watch?v=ty2cUeiAcdY)

---
