# Splunk VirtualBox VM Setup Guide

This guide will walk you through setting up Splunk on a virtual machine using VirtualBox, from installation to adding data.

## Prerequisites

- At least 8GB RAM on your host machine (4GB will be allocated to VM)
- At least 50GB free disk space
- Internet connection for downloads

## Step 1: Install VirtualBox

### Download and Install VirtualBox

1. Go to the official VirtualBox website: https://www.virtualbox.org/wiki/Downloads
2. Download VirtualBox for your operating system (Windows, macOS, or Linux)
4. Install VirtualBox using the downloaded installer


## Step 2: Download Ubuntu Server ISO

We'll use Ubuntu Server as it's lightweight and well-supported by Splunk.

1. Go to: https://ubuntu.com/download/server
2. Download the latest Ubuntu Server LTS ISO (typically 20.04 or 22.04 LTS)
3. Save the ISO file to a location you'll remember

## Step 3: Create Virtual Machine

### Create New VM

1. Open VirtualBox
2. Click "New" to create a new virtual machine
3. Fill in the details:
   - **Name**: Splunk-Server
   - **Type**: Linux
   - **Version**: Ubuntu (64-bit)
4. Click "Next"

### Configure Memory

1. Allocate **4096 MB (4GB)** of RAM minimum
3. Click "Next"

### Create Virtual Hard Disk

1. Select "Create a virtual hard disk now"
2. Choose **VDI (VirtualBox Disk Image)**
3. Select **Dynamically allocated**
4. Set size to **50GB minimum** (80GB recommended)
5. Click "Create"

### Configure VM Settings

1. Select your new VM and click "Settings"
2. **System → Processor**: Allocate 2-4 CPU cores
3. **Network → Adapter 1**: 
   - Enable Network Adapter
   - Attached to: **Bridged Adapter** (for external access)
4. **Storage**: 
   - Click the CD icon under Controller: IDE
   - Choose "Choose a disk file"
   - Select your Ubuntu Server ISO
5. Click "OK"

## Step 4: Install Ubuntu Server

### Boot and Install

1. Start the VM by clicking "Start"
2. Ubuntu installer will boot from the ISO
3. Follow the installation wizard:
   - Select language and keyboard layout
   - Choose "Install Ubuntu Server"
   - Configure network (use DHCP for simplicity)
   - Use entire disk for installation
   - Create a user account (remember these credentials!)
   - **Enable OpenSSH server** when prompted
   - Don't select any additional packages

### Post-Installation Setup

1. After installation, remove the ISO:
   - VM Settings → Storage → Remove CD from IDE controller
2. Reboot the VM
3. Log in with your created user account

### Update the system:
```bash
sudo apt update && sudo apt upgrade -y
```

### Install essential packages:
```bash
sudo apt install wget curl net-tools -y
```

## Step 5: Configure Network Access

### Bridged Network
If you used Bridged Adapter, find your VM's IP:
```bash
ip addr show
```
Note the IP address (usually starts with 192.168.x.x or 10.x.x.x)

Note this down, this is how you will access the splunk search.

## Step 6: Download and Install Splunk

### Create Splunk Account

1. Go to: https://www.splunk.com/en_us/download/splunk-enterprise.html
2. Create a free Splunk account
3. Download the **Linux .tgz file** for Splunk Enterprise

### Transfer Splunk to VM
After logging into your account go to: 
https://www.splunk.com/en_us/download/splunk-enterprise.html?locale=en_us

Find the "Linux" Installation packages. Select .deb and click "Copy wget link"

Paste the wget link into the virtual machine, allow it to download the file: 

### Install Splunk

1. Install splunk
Install the file that you downloaded
```bash
apt install ./splunk-10.0.0... 
``` 

2. Create admin credentials when prompted (remember these!)

3. Enable Splunk to start on boot:
```bash
sudo /opt/splunk/bin/splunk enable boot-start
```

## Step 7: Configure Splunk Web Access

### Verify Splunk is running:
```bash
sudo -u splunk /opt/splunk/bin/splunk status
```

### Access Splunk Web Interface

- **Bridged Network**: http://VM_IP_ADDRESS:8000
- Login with the admin credentials you created

## Step 8: Add Data to Splunk

### Sample Data Method 1: Upload Files

1. In Splunk Web, go to **Settings → Add Data**
2. Select **Upload** 
3. Upload sample log files (create some or download sample data)

### Sample Data Method 2: Monitor System Logs

1. Go to **Settings → Add Data → Monitor**
2. Select **Files & Directories**
3. Add paths like:
   - `/var/log/syslog` (system logs)
   - `/var/log/auth.log` (authentication logs)
   - `/var/log/apache2/` (if you install Apache)


### Sample Data Method 3: Install Sample Apps

1. In Splunk Web, go to **Apps → Find More Apps**
2. Search for and install:
   - **Splunk Tutorial**
   - **Sample Data**
   - **Eventgen** (for generating synthetic data)

## Step 9: Basic Splunk Configuration

### Create an Index

1. Go to **Settings → Indexes**
2. Click **New Index**
3. Name it (e.g., "test_data")
4. Use default settings and save

### Configure Data Input

1. **Settings → Data Inputs**
2. Select input type (Files & Directories, Network ports, etc.)
3. Configure source, source type, and destination index

### Basic Search Examples

Try these searches in the Search app:
```spl
# View all events
index=*

# View events from last hour
index=* earliest=-1h

# Count events by source
index=* | stats count by source

# View authentication events (if monitoring auth.log)
index=* source="/var/log/auth.log"
```

### Useful Commands

```bash
# Restart Splunk
sudo -u splunk /opt/splunk/bin/splunk restart

# Check Splunk status
sudo -u splunk /opt/splunk/bin/splunk status

# View Splunk logs
sudo tail -f /opt/splunk/var/log/splunk/splunkd.log

# Check VM IP
ip addr show

# Check open ports
netstat -tlnp
```

## Next Steps

1. **Learn Splunk**: Complete the built-in tutorial in Splunk Web
2. **Add Real Data**: Configure inputs for your actual log sources  
3. **Create Dashboards**: Build visualizations for your data
4. **Set Up Alerts**: Configure notifications for important events
5. **Install Apps**: Browse Splunkbase for additional functionality

## Important Links

- **VirtualBox**: https://www.virtualbox.org/
- **Ubuntu Server**: https://ubuntu.com/download/server
- **Splunk Enterprise**: https://www.splunk.com/en_us/download/splunk-enterprise.html
- **Splunk Documentation**: https://docs.splunk.com/
- **Splunkbase Apps**: https://splunkbase.splunk.com/

---
