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
## Introduction
This lab uses virtual machine images to work with the "Virtual Box" Hyprvisor, a Hyprvisor is a kind of software utilized to emulate entire computers allowing for easy containement and testing of different software. While it can be simple to initally setup it usually requires constant maintenece

---

## Setup Instructions

0. Install Virtual box Hyprvisor
[Virtual Box Website https://www.virtualbox.org/](https://www.virtualbox.org/)

<img width="904" height="445" alt="image" src="https://github.com/user-attachments/assets/dda17a59-8cce-455d-b163-716cd3be778e" />

Alternatively, you can use one of these commands to install the platform

**Windows:**
```powershell
winget install -e Oracle.VirtualBox
```

**Linux:**
```bash
apt install VirtualBox
```

You will have to make sure your computer is compatible with virtualization, most modern CPU's do have ethe instructions required but will sometimes require explicit enabling in the bios. See [This microsoft support article regarding enabling virtualization](https://support.microsoft.com/en-us/windows/enable-virtualization-on-windows-c5578302-6e43-4b4b-a449-8ced115f58e1)

1. Download the applicable virtual machine images
**KALI** - Image can be found at [kali.org](https://www.kali.org/get-kali/#kali-virtual-machines) - Make sure to select the "VirtualBox" image.

---
**Nagivate to kali.org**
<img width="1913" height="910" alt="image" src="https://github.com/user-attachments/assets/6de500e4-80d0-42ca-89ec-ae0489d86804" />

---
**Choose the "Virtual Machines option**
<img width="1327" height="645" alt="image" src="https://github.com/user-attachments/assets/2ea2323e-6cd8-4ce9-841c-b8d15e9774d0" />

---

**Select the "Virtual Box" download image**
<img width="1141" height="551" alt="image" src="https://github.com/user-attachments/assets/c60a5fe1-0ebe-4012-9f3d-3e2f743f9300" />

**After downloading navigate to the download directory (typically in your downloads folder) and right click it and hit "Extract all" otherwise the machine will face issues later down the line**

---

**Target-Linux** - This image can be obtained on my [one drive account. Due to the size of the image](https://livemissouristate-my.sharepoint.com/:u:/g/personal/to329s_login_missouristate_edu/IQAVgazYLMDbQbAhKF52e-QfAb5shRsjRSrnlhTumnTYQF4?e=JoyK4c) 

This does not require extraction since it is just a single image

---

3. Import the Kali Virtual machine Image
**Launch Virtual Box**
<img width="1914" height="1026" alt="image" src="https://github.com/user-attachments/assets/5ef22b95-9f2e-4fce-83b6-e4e83a2202e6" />


**Click the "Open" button and select the appicable virtual machine image**
<img width="475" height="375" alt="image" src="https://github.com/user-attachments/assets/422f08ff-898a-457c-b574-7f1384864f34" />


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
- Click the "preferences" OR "settings" for each virtual machine
<img width="568" height="148" alt="image" src="https://github.com/user-attachments/assets/642a4863-5705-4ac4-a448-3fe3c1321960" />

- if not already in place hit the "advanced" or "Expert" settings, this will allow us to make multiple networking connection points

Make adapter 1 "NAT"
<img width="947" height="1038" alt="image" src="https://github.com/user-attachments/assets/5ab56c76-8b43-4dd8-8849-350261f2ff6e" />

Make adapter 2 "Internal Network"
<img width="948" height="1032" alt="image" src="https://github.com/user-attachments/assets/3fe6817e-800c-4512-bbed-e22d5c5c4af5" />

This will ensure you can contact the machines. "NAT" will result in the machine being able to access the internet but issues when routing between virtual machines. _The target linux image requires this setting to be changed before it will properly boot. make sure you've looked at it._ if you encounter an error on first launching this is where it will be.

Thats it! Setting up VMs are super simple and have the ability to do backups. I highly recomend at the start of this to take a backup image of the current machine, you can do this in the "Snapshots" tab that will make a backup that you can restore to, so break, hack, and then just bring it back to the original state!
