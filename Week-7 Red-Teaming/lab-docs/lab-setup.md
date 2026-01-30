# Lab Setup Document
This document will go through the proper setup for the lab for the W7 Lab. With instructions on setting up VirtualBox and installing the images

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
**Target-Linux** - Image has been uploaded in the lab files for this week. You will see target-linux.ova

2. Import the Kali Virtual machine Image
**Launch Virtual Box**
![IMAGE HERE]()

**Click the "Open" button and select the appicable virtual machine image**
![IMAGE HERE]()

**The image has now been imported and should look like this**

3. Import the Target Linux virtual machine image
**Launch Virtual Box**
![IMAGE HERE]()

**Click the "Import" button and select the appicable virtual machine image**
![IMAGE HERE]()

**The image has now been imported and should look like this**
![IMAGE HERE]()

---

3. Networking
- Click the "preferences" for each virtual machine and make sure it is either bridged networking or host only. This will ensure you can contact the machines. "NAT" will result in the machine being able to access the internet but issues when routing between virtual machines. _The target linux image requires this setting to be changed before it will properly boot. make sure you've looked at it._

Thats it! Setting up VMs are super simple and have the ability to do backups. I highly recomend at the start of this to take a backup image of the current machine, you can do this in the "Snapshots" tab that will make a backup that you can restore to, so break, hack, and then just bring it back to the original state!
