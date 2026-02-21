# DevOps Engineering Track: Session 2
## Linux & Basic Commands - From Zero to Comfortable on the Command Line


**Prerequisite**: Session 1 (Setting up a VM & Installing Linux OS)

---

## About These Lesson Notes

This lesson note is your companion guide. Read it, practice the commands, break things, and fix them. That is how you learn Linux.

## Before We Start

In Session 1, we set up a virtual machine and installed Linux (Ubuntu). If you missed that session or had trouble getting it working, do not worry at all. You can still follow along and practice everything in this lesson using a free online tool.

### Practice Tool: KillerCoda (Free, No Installation)

Go to: https://killercoda.com/playgrounds/scenario/ubuntu

KillerCoda gives you a real Linux terminal right in your browser. No downloads. No setup. Just open the link, wait a few seconds, and you have a working Ubuntu system to practice on. It resets after a while, but that is fine — you can always start a new session.

### How to Use KillerCoda

1. Open the link above in your browser (Chrome or Firefox recommended).
2. Wait a few seconds for the terminal to load. You will see a black screen with a blinking cursor.
3. Start typing the commands from this lesson. That is it. You are using Linux.

---

## 1. What is the Command Line and Why Should You Care?

When you use your phone or laptop normally, you tap icons and click buttons. That is a **Graphical User Interface (GUI)**. It is visual and friendly.

The **command line** (also called the terminal, shell, or CLI) is a different way to talk to your computer. Instead of clicking, you type instructions. The computer reads your instruction, does what you asked, and shows you the result. That is it.

### Why learn this?

As a DevOps engineer, you will spend most of your time managing servers. Servers are computers that run websites, apps, and services. Most servers do not have a graphical interface — there are no icons, no mouse, no desktop wallpaper. The only way to interact with them is through the command line. 

**So learning the command line is not optional; it is the foundation of everything else in this course.**

---

## 2. Understanding the Linux File System

Before you start creating and moving files, you need to understand how Linux organises things. On Windows, you have `C:\`, `D:\`, and folders like Documents, Downloads, etc. Linux works differently.

In Linux, everything starts from one place called the **root directory**, written as a single forward slash: `/`. Every file and folder on the entire system lives somewhere under this root. There is no `C:\` or `D:\`.

### Key Directories You Should Know

| Directory | Purpose | Example |
|-----------|---------|---------|
| `/` | Root - the very top | Everything lives under here |
| `/home` | Users' personal folders | `/home/ada`, `/home/bob` |
| `/home/username` | Your personal directory | `/home/ada` is where your files live |
| `/root` | The root user's home folder | Only the administrator uses this |
| `/etc` | System configuration files | Settings for the entire system |
| `/var` | Variable data (logs, caches) | `/var/log` contains system logs |
| `/tmp` | Temporary files | Cleaned up automatically |
| `/usr` | User programs and libraries | Most installed software lives here |
| `/bin` | Essential command programs | `ls`, `cp`, `mv` are here |
| `/opt` | Optional software | Additional programs you install |

### How Paths Work

A **path** is like an address. It tells the system exactly where to find a file or folder. There are two types:

- **Absolute path**: Starts from the root (`/`). It is the full address. 
  - Example: `/home/ada/Documents/notes.txt`

- **Relative path**: Starts from wherever you currently are. If you are already inside `/home/ada`, then `Documents/notes.txt` is enough.

---

## 3. Essential Navigation and File Commands

Now let us get our hands dirty. These are the commands you will use every single day.

### 3.1 Finding Your Way Around

| Command | What it does | Example |
|---------|-------------|---------|
| `pwd` | Print Working Directory - shows where you are | `pwd` → `/home/ada/Documents` |
| `ls` | List files in current folder | `ls` |
| `ls -l` | List with details (permissions, size, date) | `ls -l` |
| `ls -la` | List everything, including hidden files | `ls -la` |
| `ls -h` | List with human-readable sizes (KB, MB) | `ls -lh` |
| `cd folder_name` | Change Directory - go into a folder | `cd Documents` |
| `cd ..` | Go up one level (to parent folder) | `cd ..` |
| `cd ~` | Go to your home folder | `cd ~` |
| `cd /` | Go to root directory | `cd /` |
| `cd -` | Go back to previous directory | `cd -` |
| `tree` | Show folder structure visually | `tree` |
| `find` | Search for files | `find . -name "*.txt"` |

### 3.2 Creating, Copying, Moving, and Deleting

These commands let you create files and folders, make copies, move things around, and clean up. Think of them as the basic actions you do with files on your phone or laptop, but typed out instead of dragged and dropped.

| Command | What it does | Example |
|---------|-------------|---------|
| `mkdir folder_name` | Make a new folder | `mkdir myproject` |
| `touch filename` | Create an empty file | `touch notes.txt` |
| `cp source destination` | Copy a file | `cp file.txt file_copy.txt` |
| `cp -r folder/ destination/` | Copy a folder and everything inside | `cp -r myproject/ backup/` |
| `mv source destination` | Move or rename a file | `mv old_name.txt new_name.txt` |
| `rm filename` | Delete a file (be careful!) | `rm notes.txt` |
| `rm -r folder/` | Delete a folder and everything inside | `rm -r myproject/` |
| `cat filename` | Show file contents | `cat notes.txt` |
| `less filename` | View file one page at a time | `less bigfile.txt` |

---

## 4. Editing Files with Vim

### 4.1 What is Vim and Why Do We Need It?

So far, you have created empty files using `touch`. But how do you actually write something inside a file when you only have a terminal? You cannot right-click and open Notepad. You need a text editor that works inside the terminal.

There are several terminal-based text editors: `nano`, `vim`, and `vi` are the most common. We are going to focus on **Vim** because it is installed on almost every Linux server you will ever work with, and knowing how to use it is a skill that will serve you for your entire career.

### 4.2 The One Thing You Must Understand First: Vim Has Modes

Vim works differently from any text editor you have used before. In Google Docs or Notepad, when you open a file, you just start typing. Vim does not work that way.

Vim has different modes, and each mode does something different:

| Mode | What it does | How to enter | How to exit |
|------|-------------|--------------|------------|
| **Normal Mode** | Navigate, delete, copy text | Press `Esc` (default when you open) | N/A |
| **Insert Mode** | Type and edit text | Press `i` | Press `Esc` |
| **Command Mode** | Save, quit, search, replace | Type `:` in Normal Mode | Press `Esc` |
| **Visual Mode** | Select and manipulate text | Press `v` in Normal Mode | Press `Esc` |

### 4.3 Your First Time Using Vim (Step by Step)

Let us open a file in Vim, write something, save it, and exit. Follow each step carefully:

#### Step 1: Open (or create) a file with Vim
```bash
vim hello.txt
```

This opens a file called `hello.txt`. If it does not exist, Vim creates it. You will see a mostly empty screen with tildes (`~`) on the left side.

#### Step 2: Enter Insert Mode so you can type
Press the `i` key on your keyboard.

You should see `-- INSERT --` appear at the bottom of the screen. Now you can type normally.

#### Step 3: Type something
```
Hello! I am learning Linux and this is my first Vim file.
```

#### Step 4: Exit Insert Mode
Press the `Esc` key.

The `-- INSERT --` text at the bottom disappears. You are now back in Normal Mode.

#### Step 5: Save and quit
Type `:wq` and press Enter.

The `:` enters Command Mode. The `w` means write (save). The `q` means quit. Together, `:wq` means 'save the file and close Vim'.

#### Step 6: Verify your file was saved
```bash
cat hello.txt
```

The `cat` command prints the contents of a file. You should see the text you typed.

### Quick Reference: Getting Out of Vim

If you ever get stuck in Vim and panic, remember this:

1. Press `Esc` (to make sure you're in Normal Mode)
2. Type `:q!` (quit without saving) or `:wq` (save and quit)
3. Press `Enter`

---

## 5. File Types in Linux

In Windows, you know a file is a Word document because it ends with `.docx`, or a picture because it ends with `.jpg`. **Linux is different**. Linux does not care about file extensions. A file named `photo.txt` could actually be an image. 

Linux determines what a file is by looking at its **contents**, not its name.

That said, there are different categories of files in Linux:

### File Types

| Type | Symbol | Meaning | Example |
|------|--------|---------|---------|
| Regular file | `-` | Normal file (text, image, binary) | `notes.txt`, `app.py`, `image.jpg` |
| Directory | `d` | A folder | `Documents`, `myproject` |
| Symbolic link | `l` | A shortcut to another file | `shortcut.txt` → points to `actual_file.txt` |
| Device file | `c` or `b` | A hardware device | `/dev/sda` (hard drive) |

You can see these symbols when you run `ls -l`. The very first character on each line tells you the file type. For example:

```bash
drwxr-xr-x  2 ada ada 4096 Feb 20 10:00 Documents
-rw-r--r--  1 ada ada   52 Feb 20 10:05 hello.txt
```

- The `d` at the start of the first line means `Documents` is a directory.
- The `-` at the start of the second line means `hello.txt` is a regular file.

### What Does a File Really Contain?

To find out what a file truly is, use the `file` command:

```bash
file hello.txt          # Output: hello.txt: ASCII text
file /bin/ls            # Output: /bin/ls: ELF 64-bit executable
```

---

## 6. Filters: Searching and Processing Text

Filters are commands that take text as input, do something with it, and give you a result. They are incredibly useful when you are looking through log files, configuration files, or any text data.

| Command | What it does | Example |
|---------|-------------|---------|
| `grep` | Find lines containing text | `grep "error" logfile.txt` |
| `grep -i` | Find (case-insensitive) | `grep -i "ERROR" logfile.txt` |
| `head` | Show first 10 lines | `head file.txt` |
| `head -n 5` | Show first 5 lines | `head -n 5 file.txt` |
| `tail` | Show last 10 lines | `tail file.txt` |
| `tail -f` | Follow file (watch new lines) | `tail -f logfile.txt` |
| `sort` | Sort lines alphabetically | `sort names.txt` |
| `uniq` | Remove duplicate lines | `sort names.txt \| uniq` |
| `wc` | Count words/lines | `wc -l file.txt` (count lines) |
| `sed` | Find and replace text | `sed 's/old/new/g' file.txt` |
| `awk` | Process text columns | `awk '{print $1}' data.txt` |

---

## 7. Redirection: Controlling Where Output Goes

By default, when you run a command, the output appears on your screen. But what if you want to save that output to a file? Or what if you want to feed the output of one command into another command? That is what **redirection** does.

### 7.1 Output Redirection

| Symbol | What it does |
|--------|-------------|
| `>` | Send output to a file (overwrites) |
| `>>` | Append output to a file (adds) |
| `<` | Use file as input to a command |
| `2>` | Send errors to a file |
| `&>` | Send both output and errors to file |

#### Try This

```bash
echo 'First line' > myfile.txt      # Creates file with 'First line'
cat myfile.txt                       # Shows: First line

echo 'Second line' >> myfile.txt    # Adds to the file
cat myfile.txt                       # Shows: First line and Second line

echo 'Oops' > myfile.txt            # OVERWRITES everything
cat myfile.txt                       # Shows: Oops (First and Second are gone)
```

### 7.2 The Pipe ( `|` )

The pipe symbol `|` takes the output of one command and sends it as input to another command. It is like an assembly line: each station (command) does one job and passes the result to the next.

```bash
ls -la | grep '.txt'           # List files, then filter to show only .txt files
cat biglog.txt | head -20      # Show only the first 20 lines of a big file
history | grep 'mkdir'         # Search your command history for mkdir commands
cat names.txt | sort | uniq    # Sort names and remove duplicates
```

---

## 8. Users and Groups

Linux is a multi-user system. This means multiple people can use the same computer, each with their own files, settings, and permissions. Even if you are the only person using your computer, Linux still treats you as a user with a specific identity.

### 8.1 Understanding Users

Every user has a username and a unique number called a **UID** (User ID). There is one special user called **root** — this is the system administrator with full control over everything. The root user's UID is always 0.

| Command | What it does | Example |
|---------|-------------|---------|
| `whoami` | Show current user | `whoami` → `ada` |
| `id` | Show user and group IDs | `id` |
| `sudo useradd username` | Create a new user | `sudo useradd bob` |
| `sudo userdel username` | Delete a user | `sudo userdel bob` |

### 8.2 Understanding Groups

A **group** is a collection of users. Groups make it easy to give the same permissions to multiple people at once. For example, if you have a team of 5 developers, you can create a group called 'developers' and give that group access to certain folders, instead of setting permissions for each person individually.

| Command | What it does | Example |
|---------|-------------|---------|
| `groups` | Show groups for current user | `groups` |
| `groups username` | Show groups for a user | `groups ada` |
| `sudo groupadd groupname` | Create a new group | `sudo groupadd developers` |
| `sudo groupdel groupname` | Delete a group | `sudo groupdel developers` |

---

## 9. File Permissions

This is one of the most important topics in Linux. **Permissions** control who can read a file, who can modify it, and who can execute (run) it.

### 9.1 Reading Permission Strings

When you run `ls -l`, you see something like this:

```bash
-rw-r--r--  1 ada devs  1024 Feb 20 10:00 script.sh
```

That first part, `-rw-r--r--`, is the **permission string**. Let us break it down:

```
-    rw-    r--    r--
^    ^^^    ^^^    ^^^
|    |      |      |
|    |      |      +-- Others (everyone else)
|    |      +--------- Group
|    +--------------- Owner (the person who created it)
+-------------------- File type (- = regular file, d = directory)
```

The three permission types are: **r** (read), **w** (write), and **x** (execute). A dash `-` means that permission is not granted.

#### What Each Permission Means

- **r** (read) = You can view the file's contents
- **w** (write) = You can modify or delete the file
- **x** (execute) = You can run the file (if it's a program)

#### Example Breakdown

```
-rw-r--r--

Owner:  rw-  (read + write, but not execute)
Group:  r--  (read only)
Others: r--  (read only)
```

### 9.2 Changing Permissions with chmod

`chmod` (change mode) is the command to change permissions. There are two ways to use it:

#### Method 1: Symbolic (Letters)

```bash
chmod u+x script.sh     # Give the owner (u) execute (x) permission
chmod g+w file.txt      # Give the group (g) write (w) permission
chmod o-r file.txt      # Remove read (r) from others (o)
chmod a+r file.txt      # Give everyone (a = all) read permission
```

**Symbols**:
- `u` = user (owner)
- `g` = group
- `o` = others
- `a` = all
- `+` = add permission
- `-` = remove permission
- `=` = set exactly this

#### Method 2: Numeric (Numbers)

Each permission has a number: **read = 4**, **write = 2**, **execute = 1**. You add them up for each group (owner, group, others):

```bash
chmod 755 script.sh    # Owner: rwx (4+2+1=7), Group: r-x (4+1=5), Others: r-x (4+1=5)
chmod 644 notes.txt    # Owner: rw- (4+2=6), Group: r-- (4), Others: r-- (4)
chmod 700 private.txt  # Owner: rwx (7), Group: none (0), Others: none (0)
```

| Code | Permission |
|------|-----------|
| 7 | rwx (read + write + execute) |
| 6 | rw- (read + write) |
| 5 | r-x (read + execute) |
| 4 | r-- (read only) |
| 0 | --- (no permissions) |

### 9.3 Changing Ownership with chown

| Command | What it does | Example |
|---------|-------------|---------|
| `chown user file.txt` | Change the owner | `chown ada file.txt` |
| `chown user:group file.txt` | Change owner AND group | `chown ada:devs file.txt` |
| `chown -R user:group folder/` | Change ownership recursively | `chown -R ada:devs myproject/` |

---

## Practice Exercises

### Practice Exercise 1: File and Folder Basics

Try these commands one after the other in your terminal:

```bash
mkdir my_first_project
cd my_first_project
touch readme.txt
touch app.py
mkdir assets
mkdir docs
ls
cp readme.txt docs/readme_copy.txt
mv app.py assets/
ls
ls assets/
ls docs/
cd ..
pwd
```

After running these, you should have a folder called `my_first_project` with:
- An `assets` folder containing `app.py`
- A `docs` folder containing `readme_copy.txt`

If something goes wrong, delete everything with `rm -r my_first_project` and start again. That is how we learn!

### Practice Exercise 2: Vim

Try this sequence:

```bash
vim practice.txt        # Open a new file
                        # Press i to enter Insert Mode
                        # Type: My name is [your name]
                        # Type: I am learning DevOps
                        # Press Esc
                        # Type :wq and press Enter
cat practice.txt        # Check that your text was saved
```

### Practice Exercise 3: Permissions

```bash
touch myscript.sh
echo '#!/bin/bash' > myscript.sh
echo 'echo Hello World' >> myscript.sh
ls -l myscript.sh                    # Check current permissions
chmod +x myscript.sh                 # Make it executable
ls -l myscript.sh                    # See the change
./myscript.sh                        # Run it! Should print: Hello World
```

### Practice Exercise 4: Archiving

```bash
mkdir archive_test
cd archive_test
touch file1.txt file2.txt file3.txt
echo 'hello' > file1.txt
cd ..
tar -czvf my_backup.tar.gz archive_test/    # Create compressed archive
ls -lh my_backup.tar.gz                      # Check the file size
mkdir restored
tar -xzvf my_backup.tar.gz -C restored/     # Extract into restored folder
ls restored/archive_test/                     # Verify the files are there
```

---

## 10. Sudo: Doing Things as the Administrator

Some actions on Linux require administrator (root) privileges. Installing software, changing system settings, managing users — these are all restricted to prevent accidental damage. Instead of logging in as root (which is risky), you use `sudo` (Super User Do) before a command to run it with root powers temporarily.

```bash
sudo apt update                 # Update package lists (needs admin rights)
sudo useradd newuser            # Create a new user (needs admin rights)
sudo cat /etc/shadow            # View password file (restricted)
```

When you use `sudo`, the system will ask for your password. **As you type it, nothing will appear on screen — no dots, no asterisks, nothing.** This is normal. Just type your password and press Enter.

---

## 11. Package Management: Installing and Removing Software

On your phone, you install apps from the App Store or Play Store. On Linux (Ubuntu specifically), you install software using a **package manager** called **APT** (Advanced Package Tool). A **package** is simply a bundle of files that make up a piece of software.

| Command | What it does |
|---------|-------------|
| `sudo apt update` | Refresh list of available packages |
| `sudo apt upgrade` | Upgrade all packages to newer versions |
| `sudo apt install package_name` | Install a package |
| `sudo apt remove package_name` | Remove a package |
| `sudo apt search package_name` | Search for a package |
| `apt list --installed` | List installed packages |

### Practice: Install a Fun Package

```bash
sudo apt update                 # Refresh package list
sudo apt install cowsay -y      # Install cowsay (-y means yes to prompts)
cowsay 'I am learning Linux!'   # Try it out!
cowsay -f tux 'DevOps is fun'   # Try the penguin version
```

`cowsay` makes a little cow say whatever you type. It is useless in production but fantastic for making the terminal feel less intimidating.

---

## 12. Services: Programs That Run in the Background

A **service** (also called a daemon) is a program that runs in the background without you needing to interact with it. Your web server, database, and SSH server are all examples of services. They start when the computer boots up and keep running quietly.

Ubuntu uses `systemctl` to manage services:

| Command | What it does |
|---------|-------------|
| `systemctl status servicename` | Check if a service is running |
| `sudo systemctl start servicename` | Start a service |
| `sudo systemctl stop servicename` | Stop a service |
| `sudo systemctl restart servicename` | Restart a service |
| `sudo systemctl enable servicename` | Start on boot |
| `sudo systemctl disable servicename` | Don't start on boot |

### Example: Check SSH Service

```bash
systemctl status ssh          # Check if SSH is running
```

**SSH** (Secure Shell) is the service that allows you to connect to a server remotely. As a DevOps engineer, you will use SSH constantly. We will cover it in detail in a future class.

---

## 13. Processes: Programs That Are Currently Running

Every time you run a command or start a program, Linux creates a **process** for it. A process is simply a running instance of a program. Each process gets a unique number called a **PID** (Process ID).

| Command | What it does |
|---------|-------------|
| `ps` | List running processes |
| `ps aux` | List all processes with details |
| `ps aux \| grep processname` | Find a specific process |
| `kill PID` | Stop a process |
| `kill -9 PID` | Force stop a process |
| `top` | Interactive view of running processes |

### Example: Find and Stop a Process

```bash
sleep 300 &                 # Start a process that does nothing for 300 seconds (& runs it in background)
ps aux | grep sleep         # Find it (note the PID number)
kill <the_PID_number>       # Stop it
```

---

## 14. Archiving and Compression

Archiving means bundling multiple files into one file. Compression means making that file smaller. On Windows, you are probably familiar with `.zip` files. On Linux, the most common format is `.tar.gz` (a tar archive that has been compressed with gzip).

### 14.1 tar — The Tape Archive Tool

The name `tar` comes from 'tape archive' (yes, from the days of tape storage). Do not worry about the history — just know that `tar` is the standard way to bundle files on Linux.

| Command | What it does |
|---------|-------------|
| `tar -cvf archive.tar folder/` | Create archive |
| `tar -czvf archive.tar.gz folder/` | Create compressed archive |
| `tar -xvf archive.tar` | Extract archive |
| `tar -xzvf archive.tar.gz` | Extract compressed archive |
| `tar -xzvf archive.tar.gz -C folder/` | Extract to specific folder |
| `tar -tzvf archive.tar.gz` | List contents without extracting |

**Flags explained**:
- `c` = create
- `x` = extract
- `z` = compress with gzip
- `v` = verbose (show what it's doing)
- `f` = file (the filename follows)

### 14.2 zip and unzip

If you need to work with `.zip` files (common when sharing with Windows/Mac users):

```bash
zip -r myfiles.zip folder/     # Create a zip archive
unzip myfiles.zip              # Extract a zip archive
unzip -l myfiles.zip           # List contents without extracting
```

---

## Command Cheat Sheet

Here is a quick reference of every command covered in this lesson:

### Navigation
```bash
pwd              # Print working directory
cd folder        # Change directory
ls               # List files
ls -la           # List with details and hidden files
tree             # Show folder structure
```

### Files & Folders
```bash
mkdir folder     # Create folder
touch file.txt   # Create file
cp source dest   # Copy file
mv source dest   # Move/rename file
rm file.txt      # Delete file
rm -r folder/    # Delete folder
cat file.txt     # View file contents
```

### Vim
```bash
vim file.txt     # Open in vim
i                # Insert mode (inside vim)
Esc              # Exit insert mode
:wq              # Save and quit
:q!              # Quit without saving
```

### Permissions
```bash
chmod 755 file   # Change permissions (numeric)
chmod u+x file   # Add execute for owner
chown user file  # Change owner
```

### Text Processing
```bash
grep pattern file          # Find text
head -n 5 file             # First 5 lines
tail -n 5 file             # Last 5 lines
sort file                  # Sort lines
uniq file                  # Remove duplicates
```

### Redirection
```bash
command > file             # Redirect output (overwrite)
command >> file            # Redirect output (append)
command1 | command2        # Pipe output
```

### Package Management
```bash
sudo apt update            # Refresh packages
sudo apt install package   # Install package
sudo apt remove package    # Remove package
```

### Services & Processes
```bash
systemctl status service   # Check service
ps aux                     # List processes
kill PID                   # Stop process
```

### Archiving
```bash
tar -czvf archive.tar.gz folder/   # Create archive
tar -xzvf archive.tar.gz           # Extract archive
zip -r archive.zip folder/         # Create zip
unzip archive.zip                  # Extract zip
```

---

## Homework / Self-Practice

The only way to learn Linux is to use it. Here are things to try on your own before the next session:

### Assignment 1: Create a Project Structure

Make a folder called `devops_homework`. Inside it, create three subfolders: `scripts`, `logs`, and `config`. Create at least one file in each folder.

```bash
mkdir devops_homework
cd devops_homework
mkdir scripts logs config
touch scripts/deploy.sh
touch logs/app.log
touch config/settings.conf
```

### Assignment 2: Practice Vim

Open a file in Vim, write at least 5 lines about what you learned today, save and quit. Then use `cat` to verify.

```bash
vim my_notes.txt
# Write your notes, press Esc, type :wq
cat my_notes.txt
```

### Assignment 3: Experiment with Permissions

Create a script file, try to run it (it will fail), then use `chmod` to make it executable, and run it again.

```bash
touch my_script.sh
echo 'echo "Hello from my script!"' > my_script.sh
./my_script.sh                    # This will fail
chmod +x my_script.sh             # Make it executable
./my_script.sh                    # Now it works!
```

### Assignment 4: Install Something

Use apt to install `tree`:

```bash
sudo apt install tree
tree devops_homework/             # See your folder structure
```

### Assignment 5: Archive Your Work

Use tar to create a compressed backup of your `devops_homework` folder:

```bash
tar -czvf devops_homework_backup.tar.gz devops_homework/
```

---

## You've Got This! 🎉

Learning Linux is a journey, not a race. Every expert was once a beginner. Make mistakes, break things, and learn from them. That's how you become a DevOps engineer.

**Next Session**: We'll dive deeper into scripts, automation, and making Linux do the heavy lifting for you.

---
