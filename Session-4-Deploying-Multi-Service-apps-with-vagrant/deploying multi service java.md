**SHE CODES AFRICA ACADEMY**

DevOps Bootcamp

**LESSON 4**

Deploying a Multi-Service Java Application

*From Source Code to Running System --- Understanding Every Step*

Prepared by: Okwuchi

Duration: 3--4 hours (hands-on lab)

Prerequisites: Lessons 1--3 (Intro to DevOps, Linux Basics, Vagrant &
WordPress)

**Table of Contents**

1\. Introduction --- What Are We Building and Why?

2\. The Big Picture --- Application Architecture

3\. Understanding the Technology Stack

4\. Prerequisites --- Setting Up Your Workstation

5\. Setting Up Virtual Machines with Vagrant

6\. Provisioning Step 1: MySQL Database

7\. Provisioning Step 2: Memcached (Database Caching)

8\. Provisioning Step 3: RabbitMQ (Message Broker)

9\. Provisioning Step 4: Tomcat (Application Server)

10\. Provisioning Step 5: Nginx (Web Server / Reverse Proxy)

11\. Verifying the Full Stack

12\. Thinking Like an Engineer --- Key Takeaways

13\. Assignment: Automated Provisioning & Bash Scripting

14\. Further Reading & Official Documentation

**1. Introduction --- What Are We Building and Why?**

In previous lessons, you deployed a simple WordPress site --- one
application talking to one database. That is a great starting point, but
real-world production systems are rarely that simple. Companies like
Paystack, Flutterwave, or any fintech you can think of run applications
that depend on multiple backend services working together: databases for
storage, caches for speed, message queues for reliability, application
servers to run business logic, and web servers to route user traffic.

Today, we are going to deploy a full Java web application called
vProfile that uses six different services. The goal is not to memorize
the commands --- you can always look those up. The goal is to understand
WHY each service exists, WHAT role it plays, and HOW these pieces
connect to form a working system.

+-----------------------------------------------------------------------+
| **Think Like an Engineer, Not a Typist**                              |
|                                                                       |
| For every command you run today, ask yourself three questions: (1)    |
| What is this command doing? (2) Why do I need to do this? (3) How     |
| would I figure this out if I were given a completely different        |
| application? If you can answer those three questions, you have        |
| learned something transferable. If you can only copy-paste, you have  |
| learned nothing.                                                      |
+-----------------------------------------------------------------------+

**What is vProfile?** vProfile is a Java-based web application that
simulates a user profile management system. It is an open-source project
designed specifically for practicing multi-tier application deployment.
The source code lives at:
[https://github.com/hkhcoder/vprofile-project]{.underline}

**2. The Big Picture --- Application Architecture**

Before touching any terminal, let us understand what we are building.
The vProfile application has a multi-tier architecture, which means
different responsibilities are handled by different services running on
different virtual machines.

**How a User Request Flows Through the System**

Imagine a user opens their browser and navigates to the vProfile
application. Here is what happens behind the scenes:

1.  The user's browser sends an HTTP request to Nginx (the web server on
    port 80).

2.  Nginx acts as a reverse proxy --- it does not run the application
    itself. It forwards the request to Tomcat (the application server on
    port 8080).

3.  Tomcat runs the Java application. If the application needs user
    data, it first checks Memcached (the cache on port 11211) to see if
    the data is already stored in memory for fast retrieval.

4.  If the data is not in the cache (a "cache miss"), Tomcat queries
    MySQL (the database on port 3306) to fetch it. It then stores a copy
    in Memcached so the next request is faster.

5.  If the application needs to perform a background task (like sending
    a notification), it places a message on RabbitMQ (the message broker
    on port 5672) for asynchronous processing.

6.  The response travels back: Tomcat → Nginx → User's Browser.

**Our Virtual Machines**

  -------------- -------------- -------------- -------------- --------------
  **VM Name**    **Hostname**   **Service**    **Role**       **Port**

  web01          web01          Nginx          Web Server /   80
                                               Reverse Proxy  

  app01          app01          Tomcat 10      Java           8080
                                               Application    
                                               Server         

  mc01           mc01           Memcached      In-Memory      11211
                                               Cache          

  rmq01          rmq01          RabbitMQ       Message Broker 5672

  db01           db01           MariaDB        Relational     3306
                                (MySQL)        Database       
  -------------- -------------- -------------- -------------- --------------

+-----------------------------------------------------------------------+
| **Why Separate VMs?**                                                 |
|                                                                       |
| In production, each service typically runs on its own server (or      |
| container). This separation means you can scale, update, or restart   |
| one service without affecting others. If your database needs more     |
| memory, you scale that server alone. If your application server       |
| crashes, the database keeps running safely. This is the foundation of |
| microservices thinking.                                               |
+-----------------------------------------------------------------------+

**Why This Order Matters**

We will provision services from the bottom of the stack upward: Database
first, then Cache, then Message Broker, then Application Server, and
finally the Web Server. Why? Because each layer depends on the one below
it. Tomcat needs the database to be running before it can connect. Nginx
needs Tomcat to be running before it can forward requests. Always build
the foundation before the roof.

**3. Understanding the Technology Stack**

Before we install anything, let us understand what each technology does
and why it was chosen. Remember: today the application is written in
Java, but the concepts apply to any language. A Python Django app might
use PostgreSQL instead of MySQL, Redis instead of Memcached, and Celery
with RabbitMQ for background tasks --- but the architecture pattern is
the same.

**3.1 MySQL / MariaDB --- The Database**

MySQL is a relational database management system (RDBMS). It stores data
in structured tables with rows and columns, similar to a spreadsheet but
with the power to handle millions of records, enforce relationships
between data, and process complex queries. MariaDB is a
community-maintained fork of MySQL --- they are functionally equivalent
for our purposes. We use MariaDB here because it ships with the
CentOS/RHEL repositories.

**Analogy:** If your application is a restaurant, the database is the
kitchen's pantry --- it stores all the raw ingredients (data). When a
customer orders a meal, the chef (application) goes to the pantry to get
what is needed.

**Official Documentation:**
[https://mariadb.com/kb/en/documentation/]{.underline}

**3.2 Memcached --- The Cache**

Memcached is an in-memory key-value store. Instead of querying the
database every single time (which involves reading from disk and is
slow), the application stores frequently accessed data in Memcached
(which lives in RAM and is extremely fast). Think of it as a sticky note
on the pantry door that says "we already checked, the tomatoes are on
shelf 3."

**Why not just use a bigger database?** Databases are designed for
durability (data survives restarts). Caches are designed for speed (data
is lost on restart, but who cares --- it can be regenerated). Each tool
is optimized for a different job.

**Official Documentation:** [https://memcached.org/]{.underline}

**3.3 RabbitMQ --- The Message Broker**

RabbitMQ is a message queuing system. When your application needs to do
something that can wait (send an email, process a report, update
analytics), it puts a "message" on a queue. A separate worker process
picks up messages from the queue and processes them. This means the user
does not have to wait for slow background tasks to complete before
getting a response.

**Analogy:** RabbitMQ is like a restaurant's order ticket system. The
waiter (web request) writes down the order and puts it on the ticket
rail. The chef (worker) picks up tickets when ready. The waiter does not
stand waiting for the food --- they go serve other customers.

**Official Documentation:**
[https://www.rabbitmq.com/documentation.html]{.underline}

**3.4 Apache Tomcat --- The Application Server**

Tomcat is a Java application server (technically a servlet container).
It takes your compiled Java code, runs it, and serves HTTP responses.
Think of it as the engine that executes your business logic. In the
Python world, you might use Gunicorn or uWSGI instead. In Node.js, the
runtime itself serves this role.

**Why not just use Nginx to run Java?** Nginx is designed to serve
static files and proxy requests --- it cannot execute Java code. You
need a Java-capable server (Tomcat, Jetty, WildFly) to actually run the
application.

**Official Documentation:**
[https://tomcat.apache.org/tomcat-10.1-doc/index.html]{.underline}

**3.5 Nginx --- The Web Server / Reverse Proxy**

Nginx sits at the front of your stack, facing the internet. It receives
all incoming user requests and decides where to send them. In our setup,
it forwards everything to Tomcat. In a more complex setup, it could
distribute traffic across multiple Tomcat instances (load balancing),
serve static files directly (images, CSS, JavaScript), or handle SSL/TLS
encryption.

**Analogy:** Nginx is the receptionist at a hospital. The receptionist
does not treat patients but knows exactly which department to send each
person to.

**Official Documentation:** [https://nginx.org/en/docs/]{.underline}

**3.6 Apache Maven --- The Build Tool**

Maven is a build tool for Java projects. It reads a configuration file
called pom.xml that lists all the libraries (dependencies) the
application needs, downloads them, compiles the source code, runs tests,
and packages everything into a deployable file (a .war file for web
applications). In Python, you might compare this to pip + setuptools. In
JavaScript, npm or yarn serve a similar role.

**Official Documentation:**
[https://maven.apache.org/guides/]{.underline}

**4. Prerequisites --- Setting Up Your Workstation**

Before we begin, you need these tools installed on your computer. We
covered the installation of VirtualBox and Vagrant in Lesson 3. If you
have not done this yet, go back and complete Lesson 3 first.

**Required Software**

  ----------------------- ----------------------- -------------------------------------------------
  **Tool**                **Purpose**             **Installation Guide**

  Oracle VirtualBox       Runs virtual machines   https://www.virtualbox.org/wiki/Downloads
                          on your computer        

  Vagrant                 Automates VM creation   https://developer.hashicorp.com/vagrant/install
                          from a config file      

  Git Bash (Windows) /    Command-line interface  https://git-scm.com/downloads
  Terminal (Mac)          for running commands    

  Vagrant Hostmanager     Automatically updates   See command below
  Plugin                  /etc/hosts files in VMs 
  ----------------------- ----------------------- -------------------------------------------------

Install the Vagrant hostmanager plugin by running:

> vagrant plugin install vagrant-hostmanager

+-----------------------------------------------------------------------+
| **Important: Know Your Computer's Architecture**                      |
|                                                                       |
| Your computer's processor (CPU) determines which Vagrant setup files  |
| you use. Windows and older Intel-based Macs use x86/amd64             |
| architecture. Newer Apple Macs (M1, M2, M3, M4 chips) use ARM         |
| architecture. The virtual machine images (boxes) are different for    |
| each. Using the wrong one will fail. Check your Mac's chip: Apple     |
| menu \> About This Mac. If it says "Apple M1/M2/M3/M4", you need the  |
| ARM setup.                                                            |
+-----------------------------------------------------------------------+

**Which Vagrant Folder Do I Use?**

  ----------------------------------- -----------------------------------------
  **Your Computer**                   **Folder to Use**

  Windows (any) or Mac with Intel     vagrant/Manual_provisioning_WinMacIntel
  chip                                

  Mac with Apple Silicon              vagrant/Manual_provisioning_MacOSM1
  (M1/M2/M3/M4)                       
  ----------------------------------- -----------------------------------------

+-----------------------------------------------------------------------+
| **Why Different Vagrantfiles?**                                       |
|                                                                       |
| VirtualBox on Intel machines uses x86 VM images. On Apple Silicon     |
| Macs, VirtualBox (or alternatives like VMware Fusion / UTM) need      |
| ARM-compatible images. The Vagrantfile specifies which base OS image  |
| (box) to download. The wrong architecture will either fail to boot or |
| run painfully slowly through emulation. This is a real-world concern  |
| --- in production, you will encounter the same issue when             |
| provisioning cloud servers (e.g., AWS Graviton instances are ARM,     |
| while most EC2 instances are x86).                                    |
+-----------------------------------------------------------------------+

**5. Setting Up Virtual Machines with Vagrant**

**Step 1: Clone the Source Code**

Open your terminal (Git Bash on Windows, Terminal on Mac) and clone the
vProfile project repository:

> git clone https://github.com/hkhcoder/vprofile-project.git
>
> cd vprofile-project
>
> git checkout local

+-----------------------------------------------------------------------+
| **What Did We Just Do?**                                              |
|                                                                       |
| git clone downloads a copy of the project from GitHub to your         |
| computer. git checkout local switches to the "local" branch, which    |
| contains the Vagrant configuration files for running everything on    |
| your machine. Branches let developers maintain different versions of  |
| the same codebase --- the "main" branch might be for production while |
| "local" is for local development.                                     |
+-----------------------------------------------------------------------+

**Step 2: Navigate to the Correct Provisioning Folder**

Based on your computer's architecture (see Section 4):

> \# For Windows or Intel Mac:
>
> cd vagrant/Manual_provisioning_WinMacIntel
>
> \# For Apple Silicon Mac (M1/M2/M3/M4):
>
> cd vagrant/Manual_provisioning_MacOSM1

**Step 3: Bring Up All Virtual Machines**

> vagrant up

+-----------------------------------------------------------------------+
| **This Will Take Time**                                               |
|                                                                       |
| Vagrant is about to create 5 virtual machines, each downloading an OS |
| image, allocating RAM and CPU, and configuring networking. This can   |
| take 15--30 minutes depending on your internet speed and computer     |
| specs. If it stops or fails midway, simply run "vagrant up" again --- |
| Vagrant is smart enough to pick up where it left off.                 |
+-----------------------------------------------------------------------+

**What the Vagrantfile Actually Does**

The Vagrantfile is a Ruby script that tells Vagrant how to create and
configure virtual machines. Let us understand the key decisions it
makes:

-   Defines 5 VMs with specific hostnames: db01, mc01, rmq01, app01,
    web01

-   Assigns each VM a private IP address so they can communicate with
    each other

-   Allocates RAM and CPU to each VM (e.g., the database might get more
    RAM)

-   Specifies the base OS image (CentOS Stream for most services, Ubuntu
    for Nginx)

-   Enables the hostmanager plugin to automatically populate /etc/hosts
    files

+-----------------------------------------------------------------------+
| **Why CentOS for Some and Ubuntu for Nginx?**                         |
|                                                                       |
| CentOS (and its successor RHEL-family distributions) is extremely     |
| popular in enterprise environments. Ubuntu is popular in cloud and    |
| web hosting. A good DevOps engineer is comfortable with both. Notice  |
| that CentOS uses dnf/yum for package management while Ubuntu uses     |
| apt. The commands are different, but the concept is identical: tell   |
| the operating system to download and install software from a trusted  |
| repository. In this lab, you will work with both, which is            |
| intentional.                                                          |
+-----------------------------------------------------------------------+

**6. Provisioning Step 1: MySQL Database**

We start with the database because it is the foundation. Every other
service either reads from it or depends on it being available.

**6.1 Connect to the Database VM**

> vagrant ssh db01

This connects you to the db01 virtual machine via SSH. You are now
inside a CentOS Linux server.

> sudo -i

This switches you to the root user. In production, you would use sudo
for individual commands rather than becoming root, but for a learning
lab, this simplifies things.

**6.2 Verify Host Entries**

> cat /etc/hosts

+-----------------------------------------------------------------------+
| **What is /etc/hosts?**                                               |
|                                                                       |
| The /etc/hosts file maps hostnames to IP addresses. When Tomcat needs |
| to connect to "db01", the operating system looks in this file first   |
| (before querying DNS). Our Vagrant hostmanager plugin automatically   |
| populates this file so all VMs can find each other by name. In        |
| production, you would use DNS servers instead, but the concept is the |
| same: translate human-readable names into machine-readable IP         |
| addresses.                                                            |
+-----------------------------------------------------------------------+

**6.3 Update the Operating System**

> dnf update -y

+-----------------------------------------------------------------------+
| **Breaking Down the Command**                                         |
|                                                                       |
| dnf is the package manager for RHEL/CentOS/Fedora (similar to apt on  |
| Ubuntu). "update" tells it to download and install the latest         |
| versions of all installed packages. "-y" automatically answers "yes"  |
| to confirmation prompts. Why do this? Security patches and bug fixes. |
| Always update before installing new software.                         |
+-----------------------------------------------------------------------+

**6.4 Install the EPEL Repository and MariaDB**

> dnf install epel-release -y
>
> dnf install git mariadb-server -y

**What is EPEL?** EPEL (Extra Packages for Enterprise Linux) is a
community repository that provides additional packages not included in
the base CentOS repositories. Think of it like adding a new app store to
your phone --- it gives you access to more software.

**Why git?** We need git to clone the source code later to import the
database schema and sample data.

**6.5 Start and Enable MariaDB**

> systemctl start mariadb
>
> systemctl enable mariadb

+-----------------------------------------------------------------------+
| **start vs enable --- A Critical Distinction**                        |
|                                                                       |
| "start" runs the service RIGHT NOW. "enable" tells the system to      |
| automatically start the service whenever the VM reboots. Without      |
| "enable", your database would stop working after every restart and    |
| you would have to manually start it again. Always do both.            |
+-----------------------------------------------------------------------+

**6.6 Secure the Database**

> mysql_secure_installation

This interactive script walks you through essential security hardening.
Here is what to answer and why:

  ----------------------- ----------------------- -----------------------
  **Prompt**              **Answer**              **Why**

  Set root password?      Y (use admin123)        Prevent unauthorized
                                                  access. In production,
                                                  use a strong, unique
                                                  password.

  Remove anonymous users? Y                       Anonymous users can
                                                  login without a
                                                  password. Remove them.

  Disallow root login     n                       We need remote access
  remotely?                                       for our app. In
                                                  production, create
                                                  separate DB users with
                                                  limited permissions.

  Remove test database?   Y                       The test database is
                                                  accessible to anyone.
                                                  Remove it.

  Reload privilege        Y                       Apply all the changes
  tables?                                         immediately.
  ----------------------- ----------------------- -----------------------

+-----------------------------------------------------------------------+
| **About Passwords in This Lab**                                       |
|                                                                       |
| We use "admin123" as the password throughout this lab for simplicity. |
| In any real environment, this would be a critical security            |
| vulnerability. Use password managers and environment variables to     |
| manage credentials. Never hardcode passwords in source code.          |
+-----------------------------------------------------------------------+

**6.7 Create the Application Database and User**

> mysql -u root -padmin123
>
> mysql\> create database accounts;
>
> mysql\> grant all privileges on accounts.\* TO \'admin\'@\'localhost\'
>
> identified by \'admin123\';
>
> mysql\> grant all privileges on accounts.\* TO \'admin\'@\'%\'
>
> identified by \'admin123\';
>
> mysql\> FLUSH PRIVILEGES;
>
> mysql\> exit;

+-----------------------------------------------------------------------+
| **Breaking Down the SQL Commands**                                    |
|                                                                       |
| CREATE DATABASE creates a new empty database named "accounts". GRANT  |
| ALL PRIVILEGES creates a user called "admin" and gives it full access |
| to the "accounts" database. The "@localhost" version allows           |
| connections from the same machine, while "@%" allows connections from |
| ANY machine (needed because Tomcat runs on a different VM). FLUSH     |
| PRIVILEGES reloads the permission tables so changes take effect       |
| immediately.                                                          |
+-----------------------------------------------------------------------+

**6.8 Import the Application Data**

> cd /tmp/
>
> git clone -b local https://github.com/hkhcoder/vprofile-project.git
>
> cd vprofile-project
>
> mysql -u root -padmin123 accounts \< src/main/resources/db_backup.sql

This imports a SQL file containing the table structure and sample data
that the application needs. The \< operator redirects the file contents
as input to the mysql command.

Verify the data was imported:

> mysql -u root -padmin123 accounts
>
> mysql\> show tables;
>
> mysql\> exit;

**6.9 Configure the Firewall**

> systemctl restart mariadb
>
> systemctl start firewalld
>
> systemctl enable firewalld
>
> firewall-cmd \--get-active-zones
>
> firewall-cmd \--zone=public \--add-port=3306/tcp \--permanent
>
> firewall-cmd \--reload
>
> systemctl restart mariadb

+-----------------------------------------------------------------------+
| **Understanding Firewalls**                                           |
|                                                                       |
| A firewall controls which network traffic is allowed in and out. By   |
| default, firewalld blocks everything. We explicitly open port 3306    |
| (MySQL's default port) so that Tomcat on app01 can connect to the     |
| database. The "\--permanent" flag means this rule survives reboots.   |
| Think of it as a security guard at a building entrance who has a list |
| of allowed visitors (ports). Without opening 3306, Tomcat's           |
| connection attempts would be silently rejected.                       |
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| **How Would You Know It's Port 3306?**                                |
|                                                                       |
| You do not memorize port numbers. You look up the documentation for   |
| the software you are installing. MySQL/MariaDB defaults to 3306. This |
| is documented at                                                      |
| htt                                                                   |
| ps://mariadb.com/kb/en/configuring-mariadb-for-remote-client-access/. |
| Different software uses different ports, and the official docs always |
| tell you.                                                             |
+-----------------------------------------------------------------------+

**7. Provisioning Step 2: Memcached (Database Caching)**

Now that our database is running, we set up the caching layer. Memcached
sits between the application and the database, storing frequently
accessed data in memory for faster retrieval.

**7.1 Connect and Prepare**

> vagrant ssh mc01
>
> sudo -i
>
> cat /etc/hosts \# Verify host entries
>
> dnf update -y

**7.2 Install and Start Memcached**

> dnf install epel-release -y
>
> dnf install memcached -y
>
> systemctl start memcached
>
> systemctl enable memcached
>
> systemctl status memcached

**7.3 Configure Memcached to Accept Remote Connections**

> sed -i \'s/127.0.0.1/0.0.0.0/g\' /etc/sysconfig/memcached
>
> systemctl restart memcached

+-----------------------------------------------------------------------+
| **What Does This sed Command Do?**                                    |
|                                                                       |
| By default, Memcached only listens on 127.0.0.1 (localhost), meaning  |
| only programs on the SAME machine can connect to it. But our          |
| application (Tomcat) runs on a DIFFERENT VM (app01). The sed command  |
| is a stream editor --- it finds "127.0.0.1" in the config file and    |
| replaces it with "0.0.0.0", which means "listen on all network        |
| interfaces." This allows remote connections. The "-i" flag means edit |
| the file in-place (modify the original file directly). The "g" means  |
| global (replace all occurrences, not just the first one).             |
+-----------------------------------------------------------------------+

**7.4 Configure the Firewall**

> systemctl start firewalld
>
> systemctl enable firewalld
>
> firewall-cmd \--add-port=11211/tcp
>
> firewall-cmd \--runtime-to-permanent
>
> firewall-cmd \--add-port=11111/udp
>
> firewall-cmd \--runtime-to-permanent
>
> memcached -p 11211 -U 11111 -u memcached -d

+-----------------------------------------------------------------------+
| **TCP vs UDP**                                                        |
|                                                                       |
| TCP (Transmission Control Protocol) is reliable --- it guarantees     |
| data arrives in order and without errors, like a registered letter.   |
| UDP (User Datagram Protocol) is fast but unreliable --- like shouting |
| across a room. Memcached uses TCP for normal operations (port 11211)  |
| and UDP for statistics and monitoring (port 11111). The last command  |
| starts a Memcached instance with these specific ports, running as the |
| "memcached" user, in daemon mode ("-d" means run in the background).  |
+-----------------------------------------------------------------------+

**8. Provisioning Step 3: RabbitMQ (Message Broker)**

The message broker handles asynchronous communication between parts of
the application. Even if the vProfile app does not heavily use messaging
in this demo, setting it up teaches you a fundamental production
pattern.

**8.1 Connect and Prepare**

> vagrant ssh rmq01
>
> sudo -i
>
> cat /etc/hosts
>
> dnf update -y

**8.2 Install RabbitMQ**

> dnf install epel-release -y
>
> dnf install wget -y
>
> dnf -y install centos-release-rabbitmq-38
>
> dnf \--enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
>
> systemctl enable \--now rabbitmq-server

+-----------------------------------------------------------------------+
| **What Does \--enablerepo Mean?**                                     |
|                                                                       |
| CentOS organizes software into repositories (repos). Some repos are   |
| disabled by default. The centos-release-rabbitmq-38 package adds the  |
| RabbitMQ 3.8 repository definition, and \--enablerepo explicitly      |
| activates it for this install command. It is like telling your        |
| phone's app store to temporarily include a beta channel to find a     |
| specific app. Also, "\--now" after "enable" is a shortcut that        |
| combines "systemctl enable" and "systemctl start" into one command.   |
+-----------------------------------------------------------------------+

**8.3 Create a RabbitMQ User**

> sh -c \'echo \"\[{rabbit, \[{loopback_users, \[\]}\]}\].\" \>
> /etc/rabbitmq/rabbitmq.config\'
>
> rabbitmqctl add_user test test
>
> rabbitmqctl set_user_tags test administrator
>
> rabbitmqctl set_permissions -p / test \".\*\" \".\*\" \".\*\"
>
> systemctl restart rabbitmq-server

+-----------------------------------------------------------------------+
| **Understanding Each Command**                                        |
|                                                                       |
| The first line writes a config that allows non-localhost connections  |
| (similar to what we did with Memcached). "rabbitmqctl add_user test   |
| test" creates a user named "test" with password "test."               |
| "set_user_tags test administrator" gives this user admin privileges.  |
| "set_permissions" grants read, write, and configure permissions on    |
| all resources (".\*" is a regex meaning "everything"). In production, |
| you would create users with minimal necessary permissions, not full   |
| admin access.                                                         |
+-----------------------------------------------------------------------+

**8.4 Configure the Firewall**

> systemctl start firewalld
>
> systemctl enable firewalld
>
> firewall-cmd \--add-port=5672/tcp
>
> firewall-cmd \--runtime-to-permanent
>
> systemctl restart rabbitmq-server

Port 5672 is RabbitMQ's default AMQP (Advanced Message Queuing Protocol)
port. Again, you find this in the official documentation, not by
memorizing it.

**9. Provisioning Step 4: Tomcat (Application Server)**

This is the most complex step. We need to install Java, install Tomcat,
build the application from source code, configure it to connect to all
our backend services, and deploy it. Take your time and understand each
phase.

**9.1 Connect and Install Dependencies**

> vagrant ssh app01
>
> sudo -i
>
> cat /etc/hosts
>
> dnf update -y
>
> dnf install epel-release -y
>
> dnf -y install java-17-openjdk java-17-openjdk-devel
>
> dnf install git wget -y

**Why Java 17?** The vProfile application is written in Java and
requires JDK 17. "openjdk" is the runtime (runs Java programs), while
"openjdk-devel" includes the compiler and development tools (needed to
build the source code). How do you know which version? Check the
project's pom.xml file --- it specifies the Java version the project
targets.

**9.2 Download and Install Tomcat**

> cd /tmp/
>
> wget
> https://archive.apache.org/dist/tomcat/tomcat-10/v10.1.26/bin/apache-tomcat-10.1.26.tar.gz
>
> tar xzvf apache-tomcat-10.1.26.tar.gz

+-----------------------------------------------------------------------+
| **What Are These Commands Doing?**                                    |
|                                                                       |
| wget downloads a file from the internet (like clicking a download     |
| link in your browser, but from the command line). tar xzvf extracts a |
| compressed archive: x = extract, z = decompress gzip, v = verbose     |
| (show progress), f = the file to extract. This is a .tar.gz file,     |
| which is Linux's equivalent of a .zip file. After extraction, you     |
| have a folder with all of Tomcat's files.                             |
+-----------------------------------------------------------------------+

**9.3 Set Up Tomcat as a Proper Service**

> useradd \--home-dir /usr/local/tomcat \--shell /sbin/nologin tomcat
>
> cp -r /tmp/apache-tomcat-10.1.26/\* /usr/local/tomcat/
>
> chown -R tomcat.tomcat /usr/local/tomcat

+-----------------------------------------------------------------------+
| **Why Create a Separate "tomcat" User?**                              |
|                                                                       |
| Security principle: the principle of least privilege. We create a     |
| dedicated user that owns only Tomcat's files and cannot log in        |
| interactively (\--shell /sbin/nologin). If an attacker exploits a     |
| vulnerability in Tomcat, they only get the permissions of the         |
| "tomcat" user, not root. If we ran Tomcat as root and it got hacked,  |
| the attacker would have complete control of the entire server.        |
+-----------------------------------------------------------------------+

**9.4 Create a Systemd Service File**

Create the file /etc/systemd/system/tomcat.service with the following
content:

> \[Unit\]
>
> Description=Tomcat
>
> After=network.target
>
> \[Service\]
>
> User=tomcat
>
> Group=tomcat
>
> WorkingDirectory=/usr/local/tomcat
>
> Environment=JAVA_HOME=/usr/lib/jvm/jre
>
> Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
>
> Environment=CATALINA_HOME=/usr/local/tomcat
>
> Environment=CATALINA_BASE=/usr/local/tomcat
>
> ExecStart=/usr/local/tomcat/bin/catalina.sh run
>
> ExecStop=/usr/local/tomcat/bin/shutdown.sh
>
> RestartSec=10
>
> Restart=always
>
> \[Install\]
>
> WantedBy=multi-user.target

+-----------------------------------------------------------------------+
| **What is a Systemd Service File?**                                   |
|                                                                       |
| Systemd is Linux's service manager --- it controls starting,          |
| stopping, and monitoring services. A .service file is like a recipe   |
| card that tells systemd: Who should run this? (User=tomcat), What     |
| should it execute? (ExecStart=\...), What happens if it crashes?      |
| (Restart=always, RetryAfter=10 seconds), When should it start?        |
| (After=network.target --- wait until networking is up). Without this  |
| file, you would have to manually start Tomcat every time. With it,    |
| systemd manages Tomcat just like any other system service.            |
+-----------------------------------------------------------------------+

> systemctl daemon-reload
>
> systemctl start tomcat
>
> systemctl enable tomcat

daemon-reload tells systemd to re-read its configuration files (because
we just added a new one).

**9.5 Configure the Firewall**

> systemctl start firewalld
>
> systemctl enable firewalld
>
> firewall-cmd \--zone=public \--add-port=8080/tcp \--permanent
>
> firewall-cmd \--reload

**9.6 Build and Deploy the Application**

This is where we compile the source code and deploy it to Tomcat.

**Install Maven:**

> cd /tmp/
>
> wget
> https://archive.apache.org/dist/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
>
> unzip apache-maven-3.9.9-bin.zip
>
> cp -r apache-maven-3.9.9 /usr/local/maven3.9
>
> export MAVEN_OPTS=\"-Xmx512m\"

**Download and configure the source code:**

> git clone -b local https://github.com/hkhcoder/vprofile-project.git
>
> cd vprofile-project
>
> vim src/main/resources/application.properties

+-----------------------------------------------------------------------+
| **Critical Step: application.properties**                             |
|                                                                       |
| This file tells the Java application how to connect to all the        |
| backend services. It contains the hostnames, ports, usernames, and    |
| passwords for MySQL, Memcached, and RabbitMQ. Make sure the hostnames |
| (db01, mc01, rmq01) match the VM names in your Vagrantfile and        |
| /etc/hosts. If this file is wrong, the application will start but     |
| immediately fail when trying to reach its dependencies.               |
+-----------------------------------------------------------------------+

**Build the code:**

> /usr/local/maven3.9/bin/mvn install

This command tells Maven to compile the Java source code, download all
required libraries listed in pom.xml, run the tests, and package
everything into a .war file (Web Application Archive). This is similar
to how npm install downloads dependencies and npm build creates a
production bundle in JavaScript projects.

**Deploy to Tomcat:**

> systemctl stop tomcat
>
> rm -rf /usr/local/tomcat/webapps/ROOT\*
>
> cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
>
> systemctl start tomcat
>
> chown tomcat.tomcat /usr/local/tomcat/webapps -R
>
> systemctl restart tomcat

+-----------------------------------------------------------------------+
| **Why ROOT.war?**                                                     |
|                                                                       |
| In Tomcat, the "ROOT" web application is the one served at the root   |
| URL (/). If we named it vprofile.war, it would be accessible at       |
| /vprofile instead. By naming it ROOT.war and removing the existing    |
| ROOT directory, our application becomes the default --- users can     |
| access it without specifying a path. Tomcat automatically extracts    |
| .war files into directories when it starts.                           |
+-----------------------------------------------------------------------+

**10. Provisioning Step 5: Nginx (Web Server / Reverse Proxy)**

The final piece. Nginx sits in front of Tomcat and routes user traffic
to the application. Notice that this VM runs Ubuntu, not CentOS --- pay
attention to the different package management commands.

**10.1 Connect and Prepare**

> vagrant ssh web01
>
> sudo -i
>
> cat /etc/hosts
>
> apt update && apt upgrade -y

+-----------------------------------------------------------------------+
| **Notice the Difference: apt vs dnf**                                 |
|                                                                       |
| On all previous VMs (CentOS), we used "dnf" to install packages. On   |
| this Ubuntu VM, we use "apt." Both are package managers --- they do   |
| the same job (download and install software from repositories) but    |
| with different syntax. "apt update" refreshes the package list (like  |
| refreshing your app store), "apt upgrade" installs updates. On        |
| CentOS, "dnf update" does both in one step. As a DevOps engineer, you |
| will work with multiple Linux distributions and must be comfortable   |
| with both.                                                            |
+-----------------------------------------------------------------------+

**10.2 Install Nginx**

> apt install nginx -y

**10.3 Configure Nginx as a Reverse Proxy**

Create the configuration file /etc/nginx/sites-available/vproapp with
the following content:

> upstream vproapp {
>
> server app01:8080;
>
> }
>
> server {
>
> listen 80;
>
> location / {
>
> proxy_pass http://vproapp;
>
> }
>
> }

+-----------------------------------------------------------------------+
| **Understanding the Nginx Configuration**                             |
|                                                                       |
| The "upstream" block defines a group of backend servers. Here it has  |
| just one (app01 on port 8080), but you could list multiple Tomcat     |
| servers for load balancing. The "server" block says: listen on port   |
| 80, and for any request to any URL (/), pass it to the "vproapp"      |
| upstream group. This is the essence of a reverse proxy --- the user   |
| talks to Nginx on port 80, completely unaware that Tomcat is doing    |
| the real work on port 8080 behind the scenes.                         |
+-----------------------------------------------------------------------+

**10.4 Activate the Configuration**

> rm -rf /etc/nginx/sites-enabled/default
>
> ln -s /etc/nginx/sites-available/vproapp
> /etc/nginx/sites-enabled/vproapp
>
> systemctl restart nginx

**What is a symbolic link (ln -s)?** It is like a shortcut on your
desktop. Nginx reads its active configurations from sites-enabled/.
Instead of copying the file, we create a link from sites-enabled/
pointing to the actual file in sites-available/. This way, you can
easily disable a site by removing the link, without deleting the
original configuration file.

**Why remove the default config?** Nginx ships with a default "Welcome
to Nginx" page. We remove it so our vproapp configuration is the only
one active. If both were enabled, they could conflict.

**11. Verifying the Full Stack**

Congratulations --- you have provisioned a complete multi-tier Java
application! Now let us verify everything works.

**11.1 Check Each Service**

SSH into each VM and verify the service is running:

> \# On db01:
>
> systemctl status mariadb
>
> \# On mc01:
>
> systemctl status memcached
>
> \# On rmq01:
>
> systemctl status rabbitmq-server
>
> \# On app01:
>
> systemctl status tomcat
>
> \# On web01:
>
> systemctl status nginx

Each service should show "active (running)" in green. If any service is
not running, check the logs:

> journalctl -u \<service-name\> \--no-pager -n 50

**11.2 Access the Application**

Open your web browser and navigate to the IP address of your web01 VM
(check the Vagrantfile for the IP). You should see the vProfile login
page. The default login credentials are typically admin_vp / admin_vp.

**11.3 Troubleshooting Checklist**

If the application does not load, work through these checks
systematically:

7.  Can web01 reach app01? From web01, run: curl http://app01:8080

8.  Can app01 reach db01? From app01, run: mysql -h db01 -u admin
    -padmin123 accounts

9.  Are firewalls configured correctly? Check with: firewall-cmd
    \--list-all

10. Are /etc/hosts entries correct on all VMs?

11. Is application.properties configured with the right hostnames and
    credentials?

12. Check Tomcat logs: tail -f /usr/local/tomcat/logs/catalina.out

+-----------------------------------------------------------------------+
| **The Engineering Mindset for Troubleshooting**                       |
|                                                                       |
| When something breaks, do not panic and do not randomly change        |
| things. Work systematically from the bottom of the stack upward. Is   |
| the database running? Can the application reach the database? Is the  |
| application running? Can Nginx reach the application? Each layer      |
| depends on the one below. Isolate the failure point by testing each   |
| connection individually.                                              |
+-----------------------------------------------------------------------+

**12. Thinking Like an Engineer --- Key Takeaways**

**12.1 Patterns, Not Commands**

Every service we set up today followed the same pattern, regardless of
the software:

13. Install the software (using the OS package manager or by downloading
    it)

14. Configure it (edit config files to set ports, users, permissions,
    connections)

15. Start the service and enable it for automatic restart

16. Open the firewall port so other services can connect

17. Verify it works before moving on

If tomorrow you are asked to deploy a Python Flask app with PostgreSQL
and Redis, the pattern is exactly the same. The commands change, but the
thinking does not.

**12.2 The Importance of Documentation**

Professional engineers do not memorize installation commands. They read
the official documentation for the specific version they are installing.
Bookmark these resources:

-   MariaDB: https://mariadb.com/kb/en/documentation/

-   Memcached: https://github.com/memcached/memcached/wiki

-   RabbitMQ: https://www.rabbitmq.com/docs

-   Apache Tomcat: https://tomcat.apache.org/tomcat-10.1-doc/

-   Nginx: https://nginx.org/en/docs/

-   Maven: https://maven.apache.org/guides/

-   CentOS/RHEL (dnf):
    https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/package-management/DNF/

-   Ubuntu (apt): https://ubuntu.com/server/docs/package-management

**12.3 Linux Distribution Differences Summary**

  ----------------------- ----------------------- -----------------------
  **Task**                **CentOS / RHEL (dnf)** **Ubuntu / Debian
                                                  (apt)**

  Update package list     dnf update -y           apt update

  Install updates         (included in update)    apt upgrade -y

  Install a package       dnf install \<package\> apt install \<package\>
                          -y                      -y

  Start a service         systemctl start         systemctl start
                          \<name\>                \<name\>

  Firewall tool           firewalld               ufw (or iptables)

  Default web server      nginx or httpd          nginx or apache2
  package name                                    
  ----------------------- ----------------------- -----------------------

**12.4 Security Lessons**

Even in a lab, we practiced real security concepts: creating dedicated
service users with limited permissions, configuring firewalls to only
expose required ports, securing databases with passwords and removing
test data, and understanding the risks of running services as root. In
production, you would add TLS/SSL encryption, use secrets management
tools (like HashiCorp Vault), implement network segmentation, and follow
the principle of least privilege rigorously.

**13. Assignment: Automated Provisioning & Bash Scripting**

+-----------------------------------------------------------------------+
| **Objective**                                                         |
|                                                                       |
| In class, we manually provisioned each service step by step. For your |
| assignment, you will explore the automated version that does          |
| everything we did today using bash scripts. This introduces you to    |
| Infrastructure as Code (IaC) --- the practice of automating           |
| infrastructure setup so it is repeatable, consistent, and             |
| version-controlled.                                                   |
+-----------------------------------------------------------------------+

**Part A: Run the Automated Setup**

18. First, destroy your manual VMs to free resources: vagrant destroy -f

19. Navigate to the automated provisioning folder:

> \# For Windows or Intel Mac:
>
> cd vagrant/Automated_provisioning_WinMacIntel
>
> \# For Apple Silicon Mac:
>
> cd vagrant/Automated_provisioning_MacOSM1

20. Run: vagrant up

21. Wait for all VMs to be created and provisioned automatically.

22. Verify the application works by accessing it in your browser, just
    like we did in Section 11.

**Part B: Understand the Bash Scripts**

The automated provisioning folder contains bash scripts (.sh files) that
automate everything we did manually. Open each script and answer the
following questions in a document:

23. Find the bash script for MySQL provisioning. List every command in
    the script and write a one-sentence explanation of what each command
    does (use your notes from today's class and the official
    documentation).

24. Compare the manual steps we followed in Section 6 (MySQL) with the
    automated bash script. What is identical? Is there anything the
    script does differently? Why might those differences exist?

25. How does the Vagrantfile trigger these bash scripts? Look for the
    "provision" keyword in the Vagrantfile and explain what it does.

26. The bash scripts use some constructs you may not have seen before.
    Research and explain the following: What does #!/bin/bash at the top
    of a script mean? What does set -e do and why is it useful? What
    does the pipe operator (\|) do? What does \> vs \>\> do when
    redirecting output to a file?

27. If you were given a Python Django application that uses PostgreSQL,
    Redis, and Celery, how would you adapt the architecture and scripts?
    You do not need to write actual scripts --- just describe what each
    VM would run and what the provisioning steps would look like. This
    tests whether you understood the pattern, not the specific commands.

+-----------------------------------------------------------------------+
| **Assignment Deadline & Submission**                                  |
|                                                                       |
| Submit your written answers as a document (PDF or Google Doc) before  |
| the next class. Your answers should demonstrate understanding, not    |
| just copy-pasting from the scripts. Explain in your own words.        |
+-----------------------------------------------------------------------+

+-----------------------------------------------------------------------+
| **Preview: Lesson 5 --- Bash Scripting**                              |
|                                                                       |
| In our next class, we will dive deep into bash scripting. You will    |
| learn how to write your own provisioning scripts, use variables,      |
| conditionals, loops, and functions. The automated provisioning        |
| scripts you are studying in this assignment are a perfect preview of  |
| what's coming. Pay close attention to them!                           |
+-----------------------------------------------------------------------+

**14. Further Reading & Official Documentation**

**Project Resources**

-   vProfile Project Repository:
    https://github.com/hkhcoder/vprofile-project

-   Manual Provisioning (Windows/Intel Mac):
    https://github.com/hkhcoder/vprofile-project/tree/local/vagrant/Manual_provisioning_WinMacIntel

-   Manual Provisioning (Apple Silicon):
    https://github.com/hkhcoder/vprofile-project/tree/local/vagrant/Manual_provisioning_MacOSM1

-   Automated Provisioning (Apple Silicon):
    https://github.com/hkhcoder/vprofile-project/tree/local/vagrant/Automated_provisioning_MacOSM1

**Official Documentation**

-   Vagrant Documentation: https://developer.hashicorp.com/vagrant/docs

-   VirtualBox Documentation: https://www.virtualbox.org/manual/

-   MariaDB Knowledge Base: https://mariadb.com/kb/en/documentation/

-   Memcached Wiki: https://github.com/memcached/memcached/wiki

-   RabbitMQ Documentation: https://www.rabbitmq.com/docs

-   Apache Tomcat 10.1 Documentation:
    https://tomcat.apache.org/tomcat-10.1-doc/

-   Nginx Documentation: https://nginx.org/en/docs/

-   Apache Maven Getting Started:
    https://maven.apache.org/guides/getting-started/

-   Systemd Service Files:
    https://www.freedesktop.org/software/systemd/man/systemd.service.html

-   firewalld Documentation: https://firewalld.org/documentation/

**Linux Fundamentals**

-   The Linux Command Line (free book):
    https://linuxcommand.org/tlcl.php

-   Bash Scripting Tutorial:
    https://linuxconfig.org/bash-scripting-tutorial

-   Linux File Permissions Explained:
    https://www.redhat.com/sysadmin/linux-file-permissions-explained

**Conceptual Reading**

-   What is a Reverse Proxy? (Nginx Blog):
    https://www.nginx.com/resources/glossary/reverse-proxy-server/

-   Understanding Caching: https://aws.amazon.com/caching/

-   Message Queues Explained:
    https://www.cloudamqp.com/blog/what-is-message-queuing.html

-   Principle of Least Privilege:
    https://csrc.nist.gov/glossary/term/least_privilege

**End of Lesson 4**

She Codes Africa Academy --- DevOps Bootcamp