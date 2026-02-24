
DevOps Engineering Track

**CLASS 3**

**Vagrant & Automated VM Provisioning**

*From Manual Setup to Infrastructure as Code*

Prerequisites: Class 1 (VM Setup) & Class 2 (Basic Linux Commands)

Estimated Duration: 3--4 hours

**Learning Objectives**

By the end of this class, you will be able to:

-   Install and configure Vagrant on both Windows and Mac (including
    Apple Silicon)

-   Use essential Vagrant commands to manage virtual machines

-   Create and configure Ubuntu VMs with custom networking, RAM, and CPU
    settings

-   Install WordPress manually on an Ubuntu VM

-   Understand Vagrant provisioning and automate the entire WordPress
    setup with a single command

+-----------------------------------------------------------------------+
| **⚠️ Recap**                                                          |
|                                                                       |
| In Class 1, we learned how to set up virtual machines manually. In    |
| Class 2, we explored basic Linux commands. Today, we bring both       |
| together --- using Vagrant to automate VM creation and provisioning.  |
+-----------------------------------------------------------------------+

**1. What Is Vagrant?**

Vagrant is an open-source tool created by HashiCorp that makes it easy
to create, configure, and manage virtual machines using simple
text-based configuration files. Instead of clicking through a VM setup
wizard every time, you write a file called a Vagrantfile that describes
exactly what kind of machine you want, and Vagrant builds it for you.

**Why Use Vagrant?**

-   Consistency --- Every team member gets the exact same development
    environment.

-   Speed --- Spin up a fully configured VM in minutes with a single
    command.

-   Reproducibility --- Destroy and rebuild your environment any time;
    your Vagrantfile is the blueprint.

-   Infrastructure as Code --- Your VM configuration lives in a file
    that can be version-controlled with Git.

**How Vagrant Works**

Vagrant sits on top of a virtualisation provider (such as VirtualBox,
VMware, or Hyper-V). You write a Vagrantfile, run a command, and Vagrant
talks to the provider to create and configure the VM for you. Think of
Vagrant as a friendly layer between you and the complicated
virtualisation software.

**2. Installing Vagrant on Windows**

There are two ways to install Vagrant on Windows: using the Chocolatey
package manager or by downloading the installer directly.

**Option A: Install Using Chocolatey**

Chocolatey is a package manager for Windows (similar to apt on Ubuntu).
If you already have Chocolatey installed, this is the quickest method.

1.  **Step 1:** Open PowerShell or Command Prompt as Administrator
    (right-click and select \"Run as administrator\").

2.  **Step 2:** Run the following command:

  -----------------------------------------------------------------------
  choco install vagrant

  -----------------------------------------------------------------------

3.  **Step 3:** Once the installation completes, close and reopen your
    terminal.

4.  **Step 4:** Verify the installation by running:

  -----------------------------------------------------------------------
  vagrant \--version

  -----------------------------------------------------------------------

You should see something like \"Vagrant 2.4.x\" confirming a successful
installation.

**Option B: Install Using the EXE Installer**

1.  **Step 1:** Go to the official Vagrant downloads page:
    https://developer.hashicorp.com/vagrant/install

2.  **Step 2:** Under the Windows tab, download the appropriate
    installer (64-bit for most modern systems).

3.  **Step 3:** Double-click the downloaded .msi file and follow the
    installation wizard.

4.  **Step 4:** Restart your computer when prompted.

5.  **Step 5:** Open a new terminal and verify with:

  -----------------------------------------------------------------------
  vagrant \--version

  -----------------------------------------------------------------------

**Install VirtualBox (Windows Provider)**

On Windows, Vagrant typically uses VirtualBox as its virtualisation
provider. You need to install it separately.

1.  **Step 1:** Download VirtualBox from:
    https://www.virtualbox.org/wiki/Downloads

2.  **Step 2:** Click \"Windows hosts\" to download the installer.

3.  **Step 3:** Run the installer and follow the prompts (default
    settings are fine).

4.  **Step 4:** Restart your computer if asked.

+-----------------------------------------------------------------------+
| **✅ Tip**                                                            |
|                                                                       |
| Make sure hardware virtualisation (VT-x / AMD-V) is enabled in your   |
| BIOS/UEFI settings. Without this, VirtualBox will not be able to run  |
| 64-bit VMs.                                                           |
+-----------------------------------------------------------------------+

**3. Installing Vagrant on Mac (M1/M2/M3/M4 Chips)**

Apple Silicon Macs (M1, M2, M3, M4) use ARM-based processors, so they
require a different virtualisation provider. Instead of VirtualBox
(which does not fully support ARM), we use VMware Fusion. Follow these
steps carefully.

**Step 1: Install Rosetta**

Rosetta allows your Mac to run software built for Intel processors. Open
Terminal and run:

  -----------------------------------------------------------------------
  /usr/sbin/softwareupdate \--install-rosetta \--agree-to-license

  -----------------------------------------------------------------------

**Step 2: Install Vagrant with Homebrew**

Homebrew is the package manager for macOS. In your Terminal, run:

  -----------------------------------------------------------------------
  brew install vagrant

  -----------------------------------------------------------------------

**Step 3: Create a Broadcom Account**

VMware is now owned by Broadcom. You need a free account to download
VMware Fusion.

-   Go to https://support.broadcom.com and click Register.

-   Complete the registration and verify your email.

**Step 4: Download and Install VMware Fusion**

1.  **1.** Log in to your Broadcom account.

2.  **2.** Click your name in the top-right corner and select VMware
    Cloud Foundation.

3.  **3.** Click My Downloads.

4.  **4.** Select VMware Fusion, then VMware Fusion 13 Pro for Personal
    Use, then version 13.6.

5.  **5.** Click the download icon, double-click the downloaded file,
    and follow the installer prompts.

**Step 5: Allow VMware Fusion in Security Settings**

1.  **1.** Open System Settings on your Mac.

2.  **2.** Navigate to Privacy & Security → Accessibility.

3.  **3.** Find VMware Fusion in the list and toggle the switch ON.

**Step 6: Install Vagrant VMware Provider**

Run these two commands in Terminal:

+-----------------------------------------------------------------------+
| brew install \--cask vagrant-vmware-utility                           |
|                                                                       |
| vagrant plugin install vagrant-vmware-desktop                         |
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| **✅ Checkpoint**                                                     |
|                                                                       |
| At this point, both Windows and Mac users should have Vagrant         |
| installed. Run \"vagrant \--version\" to confirm.                     |
+-----------------------------------------------------------------------+

**4. Essential Vagrant Commands**

Here are the Vagrant commands you will use most often. All commands are
run from the folder that contains your Vagrantfile.

  ---------------------------- ------------------------------------------
  **Command**                  **What It Does**

  vagrant init \<box\>         Initialise a new Vagrantfile with the
                               specified box (base image)

  vagrant up                   Create and start the VM defined in the
                               Vagrantfile

  vagrant ssh                  Connect to the running VM via SSH (you get
                               a terminal inside the VM)

  vagrant halt                 Gracefully shut down the VM (like pressing
                               the power button)

  vagrant reload               Restart the VM (equivalent to halt + up);
                               applies Vagrantfile changes

  vagrant reload \--provision  Restart the VM and re-run any provisioning
                               scripts

  vagrant destroy              Permanently delete the VM (the Vagrantfile
                               remains)

  vagrant status               Check whether the VM is running, stopped,
                               or not created

  vagrant global-status        Show all Vagrant VMs on your machine
                               across all projects

  vagrant suspend              Pause the VM (saves its current state to
                               disk)

  vagrant resume               Resume a suspended VM from where it left
                               off

  vagrant provision            Run the provisioning scripts again without
                               restarting the VM

  vagrant box list             List all downloaded Vagrant boxes on your
                               machine
  ---------------------------- ------------------------------------------

+-----------------------------------------------------------------------+
| **✅ Tip: The Vagrant Lifecycle**                                     |
|                                                                       |
| The typical workflow is: vagrant up (create) → vagrant ssh (use) →    |
| vagrant halt (stop) → vagrant destroy (delete). You can also use      |
| vagrant suspend/resume if you want to pause without shutting down.    |
+-----------------------------------------------------------------------+

**5. Creating an Ubuntu VM with Vagrant**

Now let us put Vagrant to work! We will create an Ubuntu 20.04 virtual
machine using the ubuntu/focal64 box.

**Step 1: Create a Project Folder**

Open your terminal and create a folder for this project:

**On Windows:**

+-----------------------------------------------------------------------+
| cd %USERPROFILE%\\Desktop                                             |
|                                                                       |
| mkdir vagrant-wordpress                                               |
|                                                                       |
| cd vagrant-wordpress                                                  |
+-----------------------------------------------------------------------+

**On Mac:**

+-----------------------------------------------------------------------+
| cd \~/Desktop                                                         |
|                                                                       |
| mkdir vagrant-wordpress                                               |
|                                                                       |
| cd vagrant-wordpress                                                  |
+-----------------------------------------------------------------------+

**Step 2: Initialise the Vagrantfile**

  -----------------------------------------------------------------------
  vagrant init ubuntu/focal64

  -----------------------------------------------------------------------

This creates a Vagrantfile in your current folder. It is a Ruby-based
configuration file that tells Vagrant what kind of VM to create.

**Step 3: Edit the Vagrantfile**

Open the Vagrantfile in a text editor and replace all of its content
with the following:

+-----------------------------------------------------------------------+
| Vagrant.configure(\"2\") do \|config\|                                |
|                                                                       |
| config.vm.box = \"ubuntu/focal64\"                                    |
|                                                                       |
| config.vm.network \"private_network\", ip: \"192.168.56.20\"          |
|                                                                       |
| config.vm.provider \"virtualbox\" do \|vb\|                           |
|                                                                       |
| vb.memory = \"1024\"                                                  |
|                                                                       |
| vb.cpus = 2                                                           |
|                                                                       |
| end                                                                   |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| **⚠️ Mac Users with Apple Silicon (M1/M2/M3/M4)**                     |
|                                                                       |
| If you are on a Mac with an Apple Silicon chip, use VMware Fusion as  |
| your provider instead. Replace the provider block above with the      |
| VMware version shown below.                                           |
+-----------------------------------------------------------------------+

Vagrantfile for Mac (Apple Silicon):

+-----------------------------------------------------------------------+
| Vagrant.configure(\"2\") do \|config\|                                |
|                                                                       |
| config.vm.box = \"spox/ubuntu-arm\"                                   |
|                                                                       |
| config.vm.box_version = \"1.0.0\"                                     |
|                                                                       |
| config.vm.network \"private_network\", ip: \"192.168.56.20\"          |
|                                                                       |
| config.vm.provider \"vmware_desktop\" do \|vmware\|                   |
|                                                                       |
| vmware.gui = true                                                     |
|                                                                       |
| vmware.allowlist_verified = true                                      |
|                                                                       |
| vmware.memory = \"1024\"                                              |
|                                                                       |
| vmware.cpus = 2                                                       |
|                                                                       |
| end                                                                   |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

**Step 4: Start the VM**

  -----------------------------------------------------------------------
  vagrant up

  -----------------------------------------------------------------------

This will download the Ubuntu box (the first time takes a few minutes),
create the VM, and start it. Once it finishes, connect to it:

  -----------------------------------------------------------------------
  vagrant ssh

  -----------------------------------------------------------------------

You are now inside your Ubuntu virtual machine! You should see a prompt
like vagrant@ubuntu-focal:\~\$. Try running a few Linux commands you
learned in Class 2.

**6. Understanding Vagrant Networking, RAM, and CPUs**

**6.1 Networking and IP Addresses**

Vagrant supports several networking modes. The two most common are:

**Private Network (Host-Only)**

This gives your VM a static IP address that only your host machine can
reach. It is perfect for local development and testing.

  -----------------------------------------------------------------------
  config.vm.network \"private_network\", ip: \"192.168.56.20\"

  -----------------------------------------------------------------------

With this setting, you can access services running on the VM (such as a
web server) by visiting 192.168.56.20 in your browser.

**Port Forwarding**

This maps a port on your host machine to a port on the VM. Useful when
you do not want to assign a static IP.

  -----------------------------------------------------------------------
  config.vm.network \"forwarded_port\", guest: 80, host: 8080

  -----------------------------------------------------------------------

With this, visiting http://localhost:8080 on your host would reach port
80 on the VM.

**Public Network (Bridged)**

This connects the VM directly to your local network, giving it an IP
address from your router (just like any other device on the network).

  -----------------------------------------------------------------------
  config.vm.network \"public_network\"

  -----------------------------------------------------------------------

**6.2 Configuring RAM (Memory)**

RAM determines how much memory your VM can use. The default is usually
512 MB or 1024 MB, but you can change it in the Vagrantfile:

**VirtualBox (Windows/Intel Mac):**

+-----------------------------------------------------------------------+
| config.vm.provider \"virtualbox\" do \|vb\|                           |
|                                                                       |
| vb.memory = \"2048\" \# 2 GB of RAM                                   |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

**VMware (Apple Silicon Mac):**

+-----------------------------------------------------------------------+
| config.vm.provider \"vmware_desktop\" do \|vmware\|                   |
|                                                                       |
| vmware.memory = \"2048\" \# 2 GB of RAM                               |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| **✅ How Much RAM?**                                                  |
|                                                                       |
| For a basic web server (like WordPress), 1024 MB (1 GB) is usually    |
| sufficient. For heavier workloads, use 2048 MB (2 GB) or more. Do not |
| allocate more than half of your host machine's total RAM.             |
+-----------------------------------------------------------------------+

**6.3 Configuring CPUs**

You can also control how many CPU cores the VM gets:

**VirtualBox:**

+-----------------------------------------------------------------------+
| config.vm.provider \"virtualbox\" do \|vb\|                           |
|                                                                       |
| vb.cpus = 2                                                           |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

**VMware:**

+-----------------------------------------------------------------------+
| config.vm.provider \"vmware_desktop\" do \|vmware\|                   |
|                                                                       |
| vmware.cpus = 2                                                       |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

After changing RAM or CPU settings, apply the changes by running:

  -----------------------------------------------------------------------
  vagrant reload

  -----------------------------------------------------------------------

**7. Installing WordPress on Your Ubuntu VM (Manual Method)**

Now that we have a running Ubuntu VM, let us install WordPress on it
step by step. This mirrors a real-world scenario where you set up a web
server from scratch. We will break this into small, beginner-friendly
steps.

+-----------------------------------------------------------------------+
| **⚠️ Before You Begin**                                               |
|                                                                       |
| Make sure your VM is running (vagrant up) and you are connected to it |
| (vagrant ssh). All the commands below should be run inside the VM.    |
+-----------------------------------------------------------------------+

**Step 1: Update the System and Install Dependencies**

WordPress needs a web server (Apache), a database (MySQL), and PHP. Let
us install everything at once:

+-----------------------------------------------------------------------+
| sudo apt update                                                       |
|                                                                       |
| sudo apt install -y apache2 ghostscript libapache2-mod-php \\         |
|                                                                       |
| mysql-server php php-bcmath php-curl php-imagick php-intl \\          |
|                                                                       |
| php-json php-mbstring php-mysql php-xml php-zip                       |
+-----------------------------------------------------------------------+

**What are we installing?**

-   apache2 --- The web server that will serve your WordPress site to
    browsers.

-   mysql-server --- The database server where WordPress stores all its
    content (posts, pages, settings).

-   php and php-\* packages --- PHP is the programming language
    WordPress is written in. The extra packages add functionality
    WordPress needs.

-   ghostscript --- Helps WordPress handle PDF files.

**Step 2: Download and Set Up WordPress Files**

We will download WordPress from the official source and place it where
Apache can serve it:

+-----------------------------------------------------------------------+
| sudo mkdir -p /srv/www                                                |
|                                                                       |
| sudo chown www-data: /srv/www                                         |
|                                                                       |
| curl https://wordpress.org/latest.tar.gz \| sudo -u www-data tar zx   |
| -C /srv/www                                                           |
+-----------------------------------------------------------------------+

**What is happening here?**

-   mkdir -p /srv/www --- Creates the directory where WordPress files
    will live.

-   chown www-data: /srv/www --- Makes the web server user (www-data)
    the owner, so Apache can read the files.

-   The curl command downloads the latest WordPress archive and extracts
    it directly into /srv/www/wordpress.

**Step 3: Configure Apache for WordPress**

We need to tell Apache where to find WordPress and how to serve it.
Create a new configuration file:

  -----------------------------------------------------------------------
  sudo nano /etc/apache2/sites-available/wordpress.conf

  -----------------------------------------------------------------------

Paste the following content into the file:

+-----------------------------------------------------------------------+
| \<VirtualHost \*:80\>                                                 |
|                                                                       |
| DocumentRoot /srv/www/wordpress                                       |
|                                                                       |
| \<Directory /srv/www/wordpress\>                                      |
|                                                                       |
| Options FollowSymLinks                                                |
|                                                                       |
| AllowOverride Limit Options FileInfo                                  |
|                                                                       |
| DirectoryIndex index.php                                              |
|                                                                       |
| Require all granted                                                   |
|                                                                       |
| \</Directory\>                                                        |
|                                                                       |
| \<Directory /srv/www/wordpress/wp-content\>                           |
|                                                                       |
| Options FollowSymLinks                                                |
|                                                                       |
| Require all granted                                                   |
|                                                                       |
| \</Directory\>                                                        |
|                                                                       |
| \</VirtualHost\>                                                      |
+-----------------------------------------------------------------------+

Save and exit (press Ctrl+X, then Y, then Enter).

Now enable the site and necessary modules:

+-----------------------------------------------------------------------+
| sudo a2ensite wordpress                                               |
|                                                                       |
| sudo a2enmod rewrite                                                  |
|                                                                       |
| sudo a2dissite 000-default                                            |
|                                                                       |
| sudo service apache2 reload                                           |
+-----------------------------------------------------------------------+

**What are these commands doing?**

-   a2ensite wordpress --- Enables our new WordPress site configuration.

-   a2enmod rewrite --- Enables URL rewriting (needed for pretty
    permalinks like /my-blog-post instead of /?p=123).

-   a2dissite 000-default --- Disables the default Apache \"It Works!\"
    page.

**Step 4: Create the Database**

WordPress needs a database to store content. Let us create one in MySQL:

  -----------------------------------------------------------------------
  sudo mysql -u root

  -----------------------------------------------------------------------

You are now in the MySQL prompt. Run these commands one by one:

+-----------------------------------------------------------------------+
| CREATE DATABASE wordpress;                                            |
|                                                                       |
| CREATE USER wordpress@localhost IDENTIFIED BY \'admin123\';           |
|                                                                       |
| GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER                   |
|                                                                       |
| ON wordpress.\*                                                       |
|                                                                       |
| TO wordpress@localhost;                                               |
|                                                                       |
| FLUSH PRIVILEGES;                                                     |
|                                                                       |
| quit;                                                                 |
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| **⚠️ Important: Change the Password!**                                |
|                                                                       |
| We are using \'admin123\' as an example password for class purposes.  |
| In a real production environment, always use a strong, unique         |
| password.                                                             |
+-----------------------------------------------------------------------+

**What did we just do?**

-   CREATE DATABASE wordpress --- Creates a new empty database called
    \"wordpress\".

-   CREATE USER --- Creates a database user with a password.

-   GRANT \... --- Gives that user permission to read, write, and manage
    only the wordpress database.

-   FLUSH PRIVILEGES --- Tells MySQL to apply the permission changes
    immediately.

Make sure MySQL is running:

  -----------------------------------------------------------------------
  sudo service mysql start

  -----------------------------------------------------------------------

**Step 5: Connect WordPress to the Database**

Copy the sample configuration file and update it with our database
details:

+-----------------------------------------------------------------------+
| sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php           |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| sudo -u www-data sed -i \'s/database_name_here/wordpress/\'           |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| sudo -u www-data sed -i \'s/username_here/wordpress/\'                |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| sudo -u www-data sed -i \'s/password_here/admin123/\'                 |
| /srv/www/wordpress/wp-config.php                                      |
+-----------------------------------------------------------------------+

**What are these commands doing?**

-   The first command copies the sample config file to create the real
    wp-config.php.

-   The sed commands find and replace the placeholder values
    (database_name_here, username_here, password_here) with the actual
    database credentials we set up.

Next, we need to set unique security keys. Open the config file:

  -----------------------------------------------------------------------
  sudo -u www-data nano /srv/www/wordpress/wp-config.php

  -----------------------------------------------------------------------

Find the section with lines that say \"put your unique phrase here\".
Delete all eight of those lines and replace them with fresh keys from
this URL (open it in your browser and copy the output):

  -----------------------------------------------------------------------
  https://api.wordpress.org/secret-key/1.1/salt/

  -----------------------------------------------------------------------

Save and close the file (Ctrl+X, Y, Enter).

**Step 6: Access WordPress in Your Browser**

Open your web browser on your host machine (not inside the VM) and go
to:

  -----------------------------------------------------------------------
  http://192.168.56.20

  -----------------------------------------------------------------------

You should see the WordPress setup screen! Fill in:

-   Site Title --- Choose any name you like.

-   Username --- Your admin username (do not use \"admin\" in
    production).

-   Password --- A strong password.

-   Email --- Your email address.

Click \"Install WordPress\" and you are done! You can log in at
http://192.168.56.20/wp-login.php.

+-----------------------------------------------------------------------+
| **✅ Congratulations!**                                               |
|                                                                       |
| You have just manually installed a complete LAMP stack (Linux,        |
| Apache, MySQL, PHP) and deployed WordPress on it. This is a           |
| real-world skill that system administrators and DevOps engineers use  |
| every day.                                                            |
+-----------------------------------------------------------------------+

**8. Introduction to Vagrant Provisioning**

You just spent several minutes typing commands to install WordPress. Now
imagine you need to do this on ten servers, or you destroy your VM and
need to set it up again. Doing it manually every time would be tedious
and error-prone.

This is where **provisioning** comes in.

**What Is Provisioning?**

Provisioning means automatically running setup commands when a VM is
created. Instead of typing each command by hand, you write a script and
tell Vagrant to execute it during \"vagrant up\". Vagrant supports
several provisioners, but the simplest one is the shell provisioner,
which runs a bash script.

**Why Is This Important?**

-   Automation --- No manual steps, fewer human errors.

-   Speed --- A full environment can be ready in minutes.

-   Repeatability --- Every VM is set up identically, every time.

-   Documentation --- The script itself documents exactly what was
    installed and configured.

-   Infrastructure as Code --- You can version-control your provisioning
    scripts alongside your Vagrantfile.

**Basic Syntax**

You can add provisioning directly in the Vagrantfile using an inline
script:

+-----------------------------------------------------------------------+
| Vagrant.configure(\"2\") do \|config\|                                |
|                                                                       |
| config.vm.box = \"ubuntu/focal64\"                                    |
|                                                                       |
| config.vm.provision \"shell\", inline: \<\<-SHELL                     |
|                                                                       |
| echo \"Hello from provisioning!\"                                     |
|                                                                       |
| sudo apt update                                                       |
|                                                                       |
| sudo apt install -y nginx                                             |
|                                                                       |
| SHELL                                                                 |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

Or you can point to an external script file:

  -----------------------------------------------------------------------
  config.vm.provision \"shell\", path: \"setup.sh\"

  -----------------------------------------------------------------------

**9. Automating WordPress Installation with Vagrant Provisioning**

Now we will take everything we did manually in Section 7 and turn it
into an automated provisioning script. When we run \"vagrant up\", the
VM will be created AND WordPress will be fully installed and ready to
use --- all from a single command.

**Step 1: Start Fresh**

If you still have the previous VM running, destroy it first:

  -----------------------------------------------------------------------
  vagrant destroy -f

  -----------------------------------------------------------------------

**Step 2: Create the Provisioning Script**

In the same folder as your Vagrantfile, create a new file called
wordpress_setup.sh:

+-----------------------------------------------------------------------+
| #!/bin/bash                                                           |
|                                                                       |
| \# ============================================                       |
|                                                                       |
| \# WordPress Automated Installation Script                            |
|                                                                       |
| \# She Codes Africa Academy - DevOps Track                            |
|                                                                       |
| \# ============================================                       |
|                                                                       |
| \# Exit immediately if any command fails                              |
|                                                                       |
| set -e                                                                |
|                                                                       |
| echo \'\>\>\> Step 1: Updating system packages\...\'                  |
|                                                                       |
| sudo apt update -y                                                    |
|                                                                       |
| echo \'\>\>\> Step 2: Installing Apache, MySQL, PHP, and              |
| dependencies\...\'                                                    |
|                                                                       |
| sudo apt install -y apache2 ghostscript libapache2-mod-php \\         |
|                                                                       |
| mysql-server php php-bcmath php-curl php-imagick php-intl \\          |
|                                                                       |
| php-json php-mbstring php-mysql php-xml php-zip                       |
|                                                                       |
| echo \'\>\>\> Step 3: Downloading WordPress\...\'                     |
|                                                                       |
| sudo mkdir -p /srv/www                                                |
|                                                                       |
| sudo chown www-data: /srv/www                                         |
|                                                                       |
| curl https://wordpress.org/latest.tar.gz \| sudo -u www-data tar zx   |
| -C /srv/www                                                           |
|                                                                       |
| echo \'\>\>\> Step 4: Configuring Apache\...\'                        |
|                                                                       |
| cat \> /tmp/wordpress.conf \<\<\'EOF\'                                |
|                                                                       |
| \<VirtualHost \*:80\>                                                 |
|                                                                       |
| DocumentRoot /srv/www/wordpress                                       |
|                                                                       |
| \<Directory /srv/www/wordpress\>                                      |
|                                                                       |
| Options FollowSymLinks                                                |
|                                                                       |
| AllowOverride Limit Options FileInfo                                  |
|                                                                       |
| DirectoryIndex index.php                                              |
|                                                                       |
| Require all granted                                                   |
|                                                                       |
| \</Directory\>                                                        |
|                                                                       |
| \<Directory /srv/www/wordpress/wp-content\>                           |
|                                                                       |
| Options FollowSymLinks                                                |
|                                                                       |
| Require all granted                                                   |
|                                                                       |
| \</Directory\>                                                        |
|                                                                       |
| \</VirtualHost\>                                                      |
|                                                                       |
| EOF                                                                   |
|                                                                       |
| sudo cp /tmp/wordpress.conf                                           |
| /etc/apache2/sites-available/wordpress.conf                           |
|                                                                       |
| sudo a2ensite wordpress                                               |
|                                                                       |
| sudo a2enmod rewrite                                                  |
|                                                                       |
| sudo a2dissite 000-default                                            |
|                                                                       |
| sudo service apache2 reload                                           |
|                                                                       |
| echo \'\>\>\> Step 5: Setting up MySQL database\...\'                 |
|                                                                       |
| sudo mysql -u root \<\<MYSQL_SCRIPT                                   |
|                                                                       |
| CREATE DATABASE IF NOT EXISTS wordpress;                              |
|                                                                       |
| CREATE USER IF NOT EXISTS wordpress@localhost IDENTIFIED BY           |
| \'admin123\';                                                         |
|                                                                       |
| GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER                   |
|                                                                       |
| ON wordpress.\* TO wordpress@localhost;                               |
|                                                                       |
| FLUSH PRIVILEGES;                                                     |
|                                                                       |
| MYSQL_SCRIPT                                                          |
|                                                                       |
| echo \'\>\>\> Step 6: Configuring WordPress\...\'                     |
|                                                                       |
| sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php \\        |
|                                                                       |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| sudo -u www-data sed -i \'s/database_name_here/wordpress/\' \\        |
|                                                                       |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| sudo -u www-data sed -i \'s/username_here/wordpress/\' \\             |
|                                                                       |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| sudo -u www-data sed -i \'s/password_here/admin123/\' \\              |
|                                                                       |
| /srv/www/wordpress/wp-config.php                                      |
|                                                                       |
| \# Generate and set security keys                                     |
|                                                                       |
| SALT=\$(curl -s https://api.wordpress.org/secret-key/1.1/salt/)       |
|                                                                       |
| sudo -u www-data bash -c \"                                           |
|                                                                       |
| TEMP_FILE=\\\$(mktemp)                                                |
|                                                                       |
| while IFS= read -r line; do                                           |
|                                                                       |
| if echo \\\"\\\$line\\\" \| grep -q \'put your unique phrase here\';  |
| then                                                                  |
|                                                                       |
| continue                                                              |
|                                                                       |
| fi                                                                    |
|                                                                       |
| echo \\\"\\\$line\\\" \>\> \\\"\\\$TEMP_FILE\\\"                      |
|                                                                       |
| done \< /srv/www/wordpress/wp-config.php                              |
|                                                                       |
| cp \\\"\\\$TEMP_FILE\\\" /srv/www/wordpress/wp-config.php             |
|                                                                       |
| rm \\\"\\\$TEMP_FILE\\\"                                              |
|                                                                       |
| \"                                                                    |
|                                                                       |
| \# Insert the salt keys before the \'stop editing\' comment           |
|                                                                       |
| SALT_ESCAPED=\$(echo \"\$SALT\" \| sed \'s/\[&/\\\\\]/\\\\&/g\')      |
|                                                                       |
| sudo -u www-data sed -i \"/stop editing/i\\\\                         |
|                                                                       |
| \$SALT_ESCAPED\" /srv/www/wordpress/wp-config.php                     |
|                                                                       |
| echo \'\>\>\> Step 7: Starting services\...\'                         |
|                                                                       |
| sudo systemctl enable apache2                                         |
|                                                                       |
| sudo systemctl enable mysql                                           |
|                                                                       |
| sudo service apache2 restart                                          |
|                                                                       |
| echo \'\'                                                             |
|                                                                       |
| echo \'============================================\'                 |
|                                                                       |
| echo \' WordPress installation complete!\'                            |
|                                                                       |
| echo \' Visit http://192.168.56.20 in your browser\'                  |
|                                                                       |
| echo \'============================================\'                 |
|                                                                       |
| echo \'\'                                                             |
+-----------------------------------------------------------------------+

**Step 3: Update the Vagrantfile**

Replace the content of your Vagrantfile with the following:

**For Windows / Intel Mac (VirtualBox):**

+-----------------------------------------------------------------------+
| Vagrant.configure(\"2\") do \|config\|                                |
|                                                                       |
| config.vm.box = \"ubuntu/focal64\"                                    |
|                                                                       |
| config.vm.network \"private_network\", ip: \"192.168.56.20\"          |
|                                                                       |
| config.vm.provider \"virtualbox\" do \|vb\|                           |
|                                                                       |
| vb.memory = \"1024\"                                                  |
|                                                                       |
| vb.cpus = 2                                                           |
|                                                                       |
| end                                                                   |
|                                                                       |
| config.vm.provision \"shell\", path: \"wordpress_setup.sh\"           |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

**For Mac with Apple Silicon (VMware):**

+-----------------------------------------------------------------------+
| Vagrant.configure(\"2\") do \|config\|                                |
|                                                                       |
| config.vm.box = \"spox/ubuntu-arm\"                                   |
|                                                                       |
| config.vm.box_version = \"1.0.0\"                                     |
|                                                                       |
| config.vm.network \"private_network\", ip: \"192.168.56.20\"          |
|                                                                       |
| config.vm.provider \"vmware_desktop\" do \|vmware\|                   |
|                                                                       |
| vmware.gui = true                                                     |
|                                                                       |
| vmware.allowlist_verified = true                                      |
|                                                                       |
| vmware.memory = \"1024\"                                              |
|                                                                       |
| vmware.cpus = 2                                                       |
|                                                                       |
| end                                                                   |
|                                                                       |
| config.vm.provision \"shell\", path: \"wordpress_setup.sh\"           |
|                                                                       |
| end                                                                   |
+-----------------------------------------------------------------------+

**Step 4: Run It!**

  -----------------------------------------------------------------------
  vagrant up

  -----------------------------------------------------------------------

Vagrant will create the VM and then automatically run the
wordpress_setup.sh script. When it finishes, open your browser and go to
http://192.168.56.20. You should see the WordPress setup screen, ready
for you to configure your site --- all from a single command!

+-----------------------------------------------------------------------+
| **✅ The Power of Provisioning**                                      |
|                                                                       |
| Compare what you just did to the manual process: instead of running   |
| dozens of commands one by one, you typed \"vagrant up\" and           |
| everything was set up automatically. If you ever need to start over,  |
| just run \"vagrant destroy -f\" followed by \"vagrant up\" and you    |
| get a fresh WordPress installation in minutes.                        |
+-----------------------------------------------------------------------+

**Re-running Provisioning**

If you make changes to your provisioning script and want to apply them
without destroying the VM, you can use:

+-----------------------------------------------------------------------+
| vagrant provision \# Run provisioning on a running VM                 |
|                                                                       |
| vagrant reload \--provision \# Restart the VM and re-run provisioning |
+-----------------------------------------------------------------------+

**10. Class Summary**

In this class, we covered a lot of ground. Here is a recap of everything
we learned:

-   **Vagrant Basics** --- What Vagrant is, why it matters, and how to
    install it on both Windows and Mac (including Apple Silicon).

-   **Essential Commands** --- vagrant up, ssh, halt, destroy, reload,
    provision, and more.

-   **VM Configuration** --- How to set up private networking (IPs),
    allocate RAM, and assign CPU cores.

-   **Manual WordPress Install** --- Setting up a full LAMP stack and
    deploying WordPress step by step.

-   **Provisioning** --- Automating the entire setup with a shell script
    so it runs during \"vagrant up\".

**Homework / Practice Exercise**

1.  **Exercise 1:** Destroy your current VM and re-create it using the
    provisioned Vagrantfile. Confirm WordPress loads in your browser.

2.  **Exercise 2:** Modify the Vagrantfile to use a different IP address
    (for example, 192.168.56.30) and increase the RAM to 2048 MB. Run
    \"vagrant reload\" and verify the changes.

3.  **Exercise 3:** Add a second VM to the same Vagrantfile (a CentOS
    box with a different IP) and bring both up at the same time.

4.  **Bonus:** Write your own provisioning script that installs Nginx
    instead of Apache, and serves a simple HTML page.

**Useful Resources**

-   Vagrant Documentation: https://developer.hashicorp.com/vagrant/docs

-   Vagrant Cloud (Find Boxes): https://app.vagrantup.com/boxes/search

-   Ubuntu WordPress Tutorial:
    https://ubuntu.com/tutorials/install-and-configure-wordpress

-   VirtualBox Downloads: https://www.virtualbox.org/wiki/Downloads