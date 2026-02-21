## Session 1: Introduction to DevOps, Linux & Virtualization


## Learning Objectives

By the end of this session, you will be able to:

- ✓ Define DevOps and explain its importance
- ✓ Understand the Software Development Life Cycle
- ✓ Explain the DevOps lifecycle and its phases
- ✓ Install package managers (Chocolatey/Homebrew)
- ✓ Understand virtualization concepts
- ✓ Set up CentOS and Ubuntu virtual machines
- ✓ Connect to VMs using SSH

---

## Part 1: Introduction to DevOps

### What is DevOps?

**DevOps** is a set of practices, cultural philosophies, and tools that combines software development (Dev) and IT operations (Ops). It aims to shorten the software development lifecycle while delivering features, fixes, and updates frequently and reliably.

> **Key Definition**: DevOps = Development + Operations working together to deliver software faster and more reliably through automation, collaboration, and continuous improvement.

### The DevOps Bridge

```
┌─────────────────────────────────────────────┐
│         DEVELOPMENT TEAM                     │
│  • Write Code                                │
│  • Build Features                            │
│  • Test Software                             │
└────────────────────┬────────────────────────┘
                     │
          ╔══════════╩══════════╗
          ║      DevOps         ║
          ║  ← Collaboration → ║
          ║    & Automation     ║
          ╚══════════╤══════════╝
                     │
┌────────────────────┴────────────────────────┐
│         OPERATIONS TEAM                      │
│  • Deploy Software                           │
│  • Manage Servers                            │
│  • Monitor Systems                           │
└─────────────────────────────────────────────┘
```

### Why DevOps?

DevOps emerged to solve the problems of traditional software development, where development and operations teams worked in **silos** (separate, isolated groups).

### Benefits of DevOps

| Traditional Approach ✗ | DevOps Approach ✓ |
|---|---|
| Siloed teams (Dev vs Ops) | Collaborative teams |
| Manual deployments | Automated deployments |
| Slow releases (months/years) | Fast releases (days/hours) |
| Blame culture | Shared responsibility |
| Late bug detection | Early continuous testing |
| Reactive problem-solving | Proactive monitoring |

### Key Benefits Delivered

-  **Faster time to market** - Deploy updates in hours, not months
-  **Improved collaboration** - Teams working together instead of in silos
-  **Higher quality software** - Fewer bugs through continuous testing
-  **Faster problem resolution** - Quick identification and fixes
-  **Reduced deployment failures** - Automated processes reduce errors
-  **Better scalability and flexibility** - Scale infrastructure as needed
-  **Increased customer satisfaction** - Users get features faster

---

## Part 2: Software Development Life Cycle (SDLC)

The **SDLC** is a structured process for planning, creating, testing, and deploying software systems. It provides a framework that development teams follow to produce high-quality software.

### The SDLC Cycle

```
         ┌─────────┐
         │ PLANNING│
         └────┬────┘
              │
         ┌────▼────┐
         │ ANALYSIS│
         └────┬────┘
              │
         ┌────▼───┐
         │ DESIGN │
         └────┬───┘
              │
         ┌────▼──────┐
         │ DEVELOP   │
         └────┬──────┘
              │
         ┌────▼────┐
         │  TEST   │
         └────┬────┘
              │
         ┌────▼──────┐
         │ DEPLOY    │
         └────┬──────┘
              │
         ┌────▼──────┐
         │ MAINTAIN  │
         └───────────┘
```

### SDLC Phases Explained

| Phase | Description |
|-------|-------------|
| **1. Planning** | Define project goals, scope, timeline, and resources needed |
| **2. Analysis** | Gather and analyze requirements from stakeholders |
| **3. Design** | Create system architecture and design specifications |
| **4. Development** | Write the actual code and build the software |
| **5. Testing** | Verify the software works correctly and meets requirements |
| **6. Deployment** | Release the software to production environment |
| **7. Maintenance** | Ongoing support, bug fixes, and updates |

---

## Part 3: DevOps Lifecycle

The **DevOps lifecycle** is a continuous loop that integrates development and operations. Unlike the traditional SDLC which is linear, DevOps emphasizes continuous integration, delivery, and feedback.

### The DevOps Infinity Loop

```
PLAN
 ↓
CODE → BUILD → TEST → RELEASE → DEPLOY → OPERATE → MONITOR
                                                      ↓
                    ← ← ← CONTINUOUS FEEDBACK ← ← ← ↑
```

### The 8 Phases of DevOps

| Phase | Description | Common Tools |
|-------|-------------|---|
| **Plan** | Define features and track progress | Jira, Trello, Azure Boards |
| **Code** | Write and review code | Git, GitHub, VS Code |
| **Build** | Compile code and create artifacts | Maven, Gradle, npm |
| **Test** | Automated testing | Selenium, JUnit, Jest |
| **Release** | Prepare for deployment | Jenkins, GitLab CI |
| **Deploy** | Push to production | Ansible, Terraform, Kubernetes |
| **Operate** | Manage infrastructure | Docker, Kubernetes, AWS |
| **Monitor** | Track performance and issues | Prometheus, Grafana, ELK |

> **Key Insight**: Unlike traditional SDLC which is linear, DevOps is a continuous loop. After monitoring, feedback goes back to planning, creating a cycle of continuous improvement.

---

## Part 4: Setting Up Your Development Environment

Before we dive into Linux and virtualization, we need to set up some essential tools on your computer. **Package managers** help you install, update, and manage software from the command line.

### For Windows Users: Installing Chocolatey

Chocolatey is a package manager for Windows, similar to apt on Linux or Homebrew on Mac.

#### Step 1: Open PowerShell as Administrator

- Right-click on the Start menu
- Select "Windows PowerShell (Admin)" or "Terminal (Admin)"

#### Step 2: Run the installation command

Copy and paste this entire command into PowerShell:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

#### Step 3: Verify the installation

```powershell
choco --version
```

#### Common Chocolatey Commands

| Command | Description |
|---------|-------------|
| `choco install <package>` | Install a package |
| `choco upgrade <package>` | Upgrade a package |
| `choco uninstall <package>` | Remove a package |
| `choco list --local-only` | List installed packages |
| `choco search <package>` | Search for packages |

### For MacOS Users: Installing Homebrew

Homebrew is the most popular package manager for macOS.

#### Step 1: Open Terminal

Press Cmd + Space, type "Terminal", and press Enter

#### Step 2: Run the installation command

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### Step 3: Add Homebrew to PATH (follow terminal instructions)

The installer will provide any additional setup needed.

#### Step 4: Verify the installation

```bash
brew --version
```

#### Common Homebrew Commands

| Command | Description |
|---------|-------------|
| `brew install <package>` | Install a package |
| `brew upgrade <package>` | Upgrade a package |
| `brew uninstall <package>` | Remove a package |
| `brew list` | List installed packages |
| `brew search <package>` | Search for packages |
| `brew update` | Update Homebrew itself |

### Installing Required Software

#### Windows (using Chocolatey):

```powershell
choco install virtualbox -y
choco install git -y
choco install vscode -y
choco install putty -y
```

#### MacOS (using Homebrew):

```bash
brew install --cask virtualbox
brew install git
brew install --cask visual-studio-code
```

---

## Part 5: Introduction to Linux

### What is Open Source?

**Open source** refers to software where the source code is freely available for anyone to view, modify, and distribute. The Free Software Foundation defines four essential freedoms:

1. 🎯 **Freedom 0**: The freedom to run the program for any purpose
2. 📚 **Freedom 1**: The freedom to study and modify the source code
3. 📤 **Freedom 2**: The freedom to redistribute the program
4. 🔨 **Freedom 3**: The freedom to create derivative programs

### Linux Origins Timeline

| Year | Milestone |
|------|-----------|
| **1984** | **GNU Project & Free Software Foundation** - Richard Stallman creates open source versions of UNIX utilities and the GPL license |
| **1991** | **Linus Torvalds Creates Linux Kernel** - A Finnish student creates an open source, UNIX-like kernel released under GPL |
| **Today** | **Linux Kernel + GNU Utilities = Complete OS** - Packaged for different audiences as "distributions" (distros) |

### Why Linux for DevOps?

| Open Source Advantages | DevOps-Specific Advantages |
|---|---|
| ✓ Free to use and modify | Most Servers Run Linux - ~96% of web servers |
| ✓ Community Support - Large helpful community | DevOps Tools - Most tools built for Linux |
| ✓ Heavily Customizable - Tailored for specific needs | Automation - Excellent scripting capabilities |
| ✓ Secure - Better security model | Stability - Runs for years without reboots |

### Linux Architecture

```
┌─────────────────────────────────────────┐
│     USER APPLICATIONS                    │
│  (vi, grep, cd, ls, browsers, etc.)     │
└────────────────┬────────────────────────┘
                 │
         ┌───────▼──────┐
         │    SHELL     │
         │  (Bash, Zsh) │
         └───────┬──────┘
                 │
         ┌───────▼──────────┐
         │     KERNEL       │
         │ (Core of the OS) │
         └───────┬──────────┘
                 │
         ┌───────▼──────────┐
         │     HARDWARE     │
         │ (CPU, RAM, etc.) │
         └──────────────────┘
```

### Linux Distributions (Distros)

A **distribution** is Linux kernel packaged with GNU utilities and other software for specific purposes.

| Popular Desktop Linux | Popular Server Linux |
|---|---|
| Ubuntu Desktop | Red Hat Enterprise Linux (RHEL) |
| Linux Mint | Ubuntu Server |
| Fedora | CentOS / Rocky Linux |
| Debian | SUSE Enterprise Linux |
| Arch Linux | Debian |

### RPM-based vs Debian-based Distributions

There are two main families of Linux distributions, each with different package management systems:

| Feature | RPM-based | Debian-based |
|---------|-----------|--------------|
| **Examples** | RHEL, CentOS, Fedora | Ubuntu, Debian, Mint |
| **Package Format** | `.rpm` | `.deb` |
| **Package Manager** | `yum` / `dnf` | `apt` / `apt-get` |
| **Install Example** | `yum install httpd` | `apt install apache2` |
| **Config Location** | `/etc/httpd/` | `/etc/apache2/` |

### Important Linux Directories

| Directory | Purpose |
|-----------|---------|
| `/root` | Home directory for root (admin) user |
| `/home/username` | Home directories for regular users |
| `/bin, /usr/bin` | User executable programs |
| `/sbin, /usr/sbin` | System executables (admin commands) |
| `/etc` | Configuration files |
| `/tmp` | Temporary files |
| `/boot` | Kernel and bootloader files |
| `/var` | Variable data (logs, databases, web files) |
| `/proc, /sys` | System information (virtual filesystems) |
| `/lib` | Shared libraries |

---

## Part 6: What is Virtualization?

### Definition

**Virtualization** is the technology that allows you to create multiple simulated environments (virtual machines) from a single physical hardware system. Each virtual machine acts as an independent computer with its own operating system.

### Virtualization Architecture

```
┌─────────────────────────────────────────────────┐
│            VIRTUAL MACHINE 1                     │
│          Guest OS: CentOS                        │
│   Apps: Web Server, Database                     │
└─────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────┐
│            VIRTUAL MACHINE 2                     │
│          Guest OS: Ubuntu                        │
│   Apps: Dev Environment                          │
└─────────────────────────────────────────────────┘

         ┌────────────────────────┐
         │  HYPERVISOR (VirtualBox)│
         │ Manages & allocates     │
         │ resources to VMs        │
         └────────────────────────┘
                     │
         ┌───────────▼──────────┐
         │  HOST OPERATING      │
         │  SYSTEM              │
         │(Windows/Mac/Linux)   │
         └───────────┬──────────┘
                     │
         ┌───────────▼──────────┐
         │ PHYSICAL HARDWARE    │
         │ (CPU, RAM, Storage,  │
         │  Network Card)       │
         └──────────────────────┘
```

### Benefits of Virtualization

| Benefit | Description |
|---------|-------------|
| **💰 Cost Savings** | Run multiple servers on one physical machine |
| **🔒 Isolation** | VMs are isolated - one crash doesn't affect others |
| **🧪 Easy Testing** | Create and destroy test environments quickly |
| **📸 Snapshots** | Save and restore VM states at any point |
| **🔄 Portability** | Move VMs between different physical hosts easily |
| **⚙️ Resource Efficiency** | Better utilization of hardware resources |

### Types of Hypervisors

| Type | Characteristics | Examples | Use Case |
|------|---|---|---|
| **Type 1 (Bare Metal)** | Runs directly on hardware, no host OS required | VMware ESXi, Hyper-V | Data centers |
| **Type 2 (Hosted)** | Runs on top of host OS, easier to set up | VirtualBox, VMware Workstation | Development/testing |

> **We Will Use**: Oracle VirtualBox - A free, open-source Type 2 hypervisor that runs on Windows, macOS, and Linux. Perfect for learning and development!

---

## Part 7: Setting Up Virtual Machines

### Prerequisites for Windows Users

#### Step 1: Enable Virtualization in BIOS

1. Restart your computer
2. Press the BIOS key during startup (usually F2, F10, F12, Delete, or Esc)
3. Navigate to Advanced or CPU Configuration
4. Enable one of the following (varies by manufacturer):
   - **Intel VT-x** (for Intel processors)
   - **AMD-V or SVM** (for AMD processors)
   - **Virtualization Technology**
5. Save and exit BIOS (usually F10)

#### Step 2: Disable Conflicting Windows Features

Open PowerShell as Administrator and run these commands:

```powershell
# Disable Hyper-V
dism.exe /Online /Disable-Feature:Microsoft-Hyper-V-All

# Disable Windows Hypervisor Platform
dism.exe /Online /Disable-Feature:HypervisorPlatform

# Disable Virtual Machine Platform
dism.exe /Online /Disable-Feature:VirtualMachinePlatform
```

**Restart your computer after running these commands.**

### Installing Oracle VirtualBox

#### Windows:

```powershell
choco install virtualbox -y
```

Or download from: https://www.virtualbox.org/wiki/Downloads

#### MacOS:

```bash
brew install --cask virtualbox
```

**Note for MacOS Users**: You may need to allow the VirtualBox kernel extension in System Preferences → Security & Privacy → General tab.

### Downloading ISO Files

Download these ISO files **before class** (they are large files):

| OS | Download URL | Size |
|---|---|---|
| **CentOS Stream** | https://mirror.stream.centos.org/9-stream/BaseOS/x86_64/iso/ | ~2 GB |
| **Ubuntu Server** | https://releases.ubuntu.com/jammy/ | ~1.5 GB |

---

## Part 8: Creating a CentOS Virtual Machine

### Step 1: Create New Virtual Machine

1. Open VirtualBox
2. Click "New" button
3. Configure as shown below:

| Setting | Value |
|---------|-------|
| **Name** | CentOS-DevOps |
| **Machine Folder** | Default or custom location |
| **Type** | Linux |
| **Version** | Red Hat (64-bit) |

> ⚠️ **Troubleshooting**: If you only see 32-bit options, virtualization is NOT enabled in your BIOS. Go back to Part 7 and complete the prerequisites.

### Step 2: Allocate Resources

| Resource | Recommended | Minimum |
|----------|---|---|
| **Memory (RAM)** | 2048 MB (2 GB) | 1024 MB |
| **Processors** | 2 CPUs | 1 CPU |
| **Hard Disk Size** | 20 GB | 15 GB |
| **Hard Disk Type** | VDI (VirtualBox Disk Image) | VDI |
| **Storage** | Dynamically allocated | Dynamically allocated |

### Step 3: Configure VM Settings

Before starting the VM, configure these additional settings:

#### Storage Settings (Attach ISO):

1. Select your VM and click "Settings"
2. Go to "Storage"
3. Click on "Empty" under Controller: IDE
4. Click the disk icon on the right side
5. Choose "Choose a disk file" and select your CentOS ISO

#### Network Settings:

1. Go to "Network"
2. **Adapter 1**: Keep as NAT (already selected)
3. **Adapter 2**: Enable Network Adapter ✓
   - Attached to: **Bridged Adapter**
   - Name: Select your active network adapter (WiFi or Ethernet)

> **Why Bridged Adapter?** Bridged networking gives your VM its own IP address on your network, allowing it to communicate with other devices and be accessed via SSH from your host machine.

### Step 4: Install CentOS

1. Start the VM by clicking "Start"
2. Select "Install CentOS Stream 9" (use arrow keys and Enter)
3. Choose your language and click Continue
4. On the Installation Summary screen, configure the following:

| Item | Action |
|------|--------|
| **Time & Date** | Select your timezone |
| **Software Selection** | Choose "Minimal Install" (for servers) |
| **Installation Destination** | Select the virtual hard disk, choose "Automatic" |
| **Network & Host Name** | Turn ON the network adapter (toggle switch) |
| **Root Password** | Set a strong password you'll remember |
| **User Creation** | Create a user with admin privileges |

5. Click "Begin Installation"
6. Wait for installation to complete (may take 10-20 minutes)
7. Click "Reboot System" when prompted

### Step 5: Post-Installation Setup

After reboot, log in with root or your created user.

#### Get the IP Address (IMPORTANT - write this down!):

```bash
ip addr show
# or
hostname -I
```

#### Install essential packages:

```bash
# Update the system
sudo dnf update -y

# Install useful tools
sudo dnf install -y vim wget curl net-tools
```

---

## Part 9: Creating an Ubuntu Virtual Machine

### Step 1: Create New Virtual Machine

| Setting | Value |
|---------|-------|
| **Name** | Ubuntu-DevOps |
| **Type** | Linux |
| **Version** | Ubuntu (64-bit) |
| **Memory** | 2048 MB |
| **Processors** | 2 CPUs |
| **Hard Disk** | 25 GB (dynamically allocated) |

### Step 2: Configure VM Settings

Same as CentOS:
- Attach Ubuntu ISO to storage
- Set Network Adapter 2 to Bridged Adapter

### Step 3: Install Ubuntu Server

1. Start the VM
2. Select your language
3. Choose "Ubuntu Server"
4. **Network configuration**: Usually automatic (DHCP)
5. **Proxy**: Leave blank (unless you need one)
6. **Mirror**: Use default
7. **Storage**: Use entire disk
8. **Profile setup**:
   - Your name: (e.g., DevOps Student)
   - Server name: `ubuntu-devops`
   - Username: (e.g., devops)
   - Password: (choose a strong password)
9. **Featured Server Snaps**: Skip (press Done)

> 🔑 **IMPORTANT**: On the SSH Setup screen, make sure to select "Install OpenSSH server" ✓. This is essential for remote access!

10. Wait for installation to complete
11. Select "Reboot Now"

### Step 4: Post-Installation Setup

Log in with your created username and password.

#### Get the IP Address:

```bash
ip addr show
# or
hostname -I
```

#### Update the system:

```bash
sudo apt update && sudo apt upgrade -y

# Install useful tools
sudo apt install -y vim wget curl net-tools
```

---

## Part 10: Connecting to VMs via SSH

**SSH** (Secure Shell) allows you to connect to your VMs remotely from your host machine. This is how you'll typically interact with Linux servers in real DevOps work.

### Connection Methods

#### From Windows (PowerShell or Command Prompt):

```powershell
ssh username@vm-ip-address

# Example:
ssh devops@192.168.1.105
```

#### From Windows (Using PuTTY):

1. Open PuTTY application
2. **Host Name**: Enter the VM's IP address
3. **Port**: 22
4. **Connection type**: SSH
5. Click "Open"

#### From MacOS/Linux Terminal:

```bash
ssh username@vm-ip-address

# Example:
ssh devops@192.168.1.105
```

### First Connection

> **Important Note**: The first time you connect, you'll see a message about the host's authenticity. Type `yes` to continue. This is normal and expected.

### Troubleshooting SSH Connections

| Problem | Solution |
|---------|----------|
| **Connection refused** | Ensure SSH server is running: `sudo systemctl start sshd` |
| **Network unreachable** | Check VM network is set to Bridged Adapter |
| **Permission denied** | Verify username and password are correct |
| **Host not found** | Verify the IP address with: `ip addr show` |

---

## Quick Reference Commands

### Essential Linux Commands

| Command | Description |
|---------|-------------|
| `pwd` | Print working directory (where am I?) |
| `ls` | List directory contents |
| `ls -la` | List all files including hidden, with details |
| `cd directory` | Change directory |
| `cd ..` | Go up one directory |
| `cd ~` | Go to home directory |
| `mkdir dirname` | Create a directory |
| `touch filename` | Create an empty file |
| `cat filename` | Display file contents |
| `cp source dest` | Copy file or directory |
| `mv source dest` | Move or rename file/directory |
| `rm filename` | Remove a file |
| `rm -rf dirname` | Remove directory and contents |
| `ip addr` | Show IP addresses |
| `clear` | Clear the terminal screen |
| `exit` | Exit the terminal/SSH session |

---

## Homework & Practice Tasks

### Task 1: Environment Setup

- [ ] Install Chocolatey (Windows) or Homebrew (MacOS)
- [ ] Install VirtualBox using the package manager
- [ ] Download CentOS and Ubuntu ISO files

### Task 2: Create Virtual Machines

- [ ] Set up one CentOS VM following the guide
- [ ] Set up one Ubuntu VM following the guide
- [ ] Ensure both VMs can access the internet
- [ ] Successfully SSH into both VMs from your host machine

### Task 3: Documentation

- [ ] Note down the IP addresses of both VMs
- [ ] Take screenshots of successful SSH connections
- [ ] Document any issues you encountered and how you resolved them

### Task 4: Research (Optional)

- [ ] List 5 DevOps tools and explain their purposes
- [ ] Research the difference between containers and virtual machines
- [ ] Find out what CI/CD stands for and its importance in DevOps

### Session 1 Completion Checklist

- [ ] I understand what DevOps is and why it matters
- [ ] I can explain the DevOps lifecycle phases
- [ ] I have installed a package manager on my system
- [ ] I have VirtualBox installed and working
- [ ] I have created a CentOS VM and can log in
- [ ] I have created an Ubuntu VM and can log in
- [ ] I can SSH into both VMs from my host machine
- [ ] I have noted the IP addresses of my VMs

---

## Summary

You've completed Session 1! You now have:

✅ A solid understanding of DevOps and its importance
✅ Two fully functional Linux virtual machines (CentOS and Ubuntu)
✅ SSH access to both VMs from your host machine
✅ The ability to set up development environments
✅ The foundation to start learning Linux administration

---

## Next Steps

In **Session 2**, we'll cover:
- Linux file system and navigation
- Essential Linux commands
- File permissions and ownership
- Working with text editors
- Package management
- And much more!

---

## Questions & Support

If you encounter any issues:

1. **Check the troubleshooting section** (Part 10)
2. **Review the prerequisites** (Part 7)
3. **Ask your instructor** or classmates
4. **Search online** - Most issues have solutions on StackOverflow or the VirtualBox forums

---

