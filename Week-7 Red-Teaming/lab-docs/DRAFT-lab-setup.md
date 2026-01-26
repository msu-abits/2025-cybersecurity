# Lab Setup Document
This document will go through the steps required to setup the lab for the next 2 days. 

## Requirements:
- Computer with [Virtual Box](https://www.virtualbox.org/) installed.

## Network Overview
| Machine Hostname | OS/Version   | user:password |
|------------------|--------------|---------------|
| Kali             | Kali Linux   | kali:kali     |
| target-linux     | Ubuntu 24.04 | ubuntu:ubuntu |

---

## Setup Instructions

0. Install Virtual box
https://www.virtualbox.org/ OR

**Windows:**
```powershell
winget install -e Oracle.VirtualBox
```

**Linux:**
```bash
apt install VirtualBox
```

1. Download the applicable virtual machine images
**KALI** - Image can be found at (https://www.kali.org/get-kali/#kali-virtual-machines) - Make sure to select the "VirtualBox" image. Unzip the downloaded file
**Target-Linux** - Image has been uploaded in the lab files for this week. you will use the .ova image and import it into virtual box to create the virtual machine. 

2. Import the Kali Virtual machine Image
**Launch Virtual Box**
![IMAGE HERE]()

**Click the "Add" button and select the appicable virtual machine image**
![IMAGE HERE]()

**The image has now been imported and should look like this**

3. Import the Target Linux virtual machine image
**Launch Virtual Box**
![IMAGE HERE]()

**Click the "Add" button and select the appicable virtual machine image**
![IMAGE HERE]()

**The image has now been imported and should look like this**
![IMAGE HERE]()

---

3. Testing and verification
- Start both of the machines using the "Start" command and allow them to open their windows. if they succesfully open and show a login screen, the lab has been properly setup!