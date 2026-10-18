# Essential Linux Commands: Practical Guide & Reference

## Table of Contents
- [Introduction to the Linux Command Line](#-introduction-to-the-linux-command-line)
- [Project Overview](#-project-overview)
- [Objective](#-objective)
- [Linux Environments & Distribution Baselines](#️-linux-environments--distribution-baselines)
- [Architecture & Command Flow (ASCII Diagram)](#-architecture--command-flow)
- [1. File & Directory Navigation](#1-file--directory-navigation)
- [2. File Creation, Copying & Deletion](#2-file-creation-copying--deletion)
- [3. Viewing & Searching Text Files](#3-viewing--searching-text-files)
- [4. Process Monitoring & Task Management](#4-process-monitoring--task-management)
- [5. System Information, Date & Hardware Diagnostics](#5-system-information-date--hardware-diagnostics)
- [6. Disk Space & Memory Usage](#6-disk-space--memory-usage)
- [7. Networking, Connectivity & Remote Access](#7-networking-connectivity--remote-access)
- [8. Users, Passwords & File Permissions](#8-users-passwords--file-permissions)
- [9. Package Management (Debian/Ubuntu vs. RHEL/CentOS)](#9-package-management-debianubuntu-vs-rhelcentos)
- [10. Compressing & Archiving Files](#10-compressing--archiving-files)
- [11. Finding Files, Manuals & Terminal Shortcuts](#11-finding-files-manuals--terminal-shortcuts)
- [Practical Everyday Troubleshooting Workflows](#-practical-everyday-troubleshooting-workflows)
- [Ethical Guidelines & Disclaimer](#️-ethical-guidelines--disclaimer)

---

## Introduction to the Linux Command Line
The Linux command-line interface (CLI) is the primary administration console for cloud virtual machines, home labs, web servers, and security appliances. Unlike graphical desktop environments that consume valuable RAM and CPU, the terminal gives you direct, scriptable access to the operating system kernel and filesystem.

Whether you are configuring network firewalls, installing a SIEM like Wazuh or Splunk, or managing containers in Docker, understanding how to read, navigate, and troubleshoot Linux systems from a shell prompt is a fundamental technical skill.

## Project Overview
This repository serves as a clear, categorized handbook of the most important Linux commands. Instead of throwing a flat list of 50 disconnected commands at you, this guide organizes utilities by their real-world jobs—such as inspecting processes, tracking memory leaks, troubleshooting network drops, and comparing Debian/Ubuntu commands with Red Hat Enterprise Linux (RHEL) alternatives.

## Objective
To provide a fast, beginner-friendly yet technically thorough reference for anyone working with Linux. Every command includes a plain-English explanation, standard syntax, common flags, and copy-pasteable examples for immediate use.

## Linux Environments & Distribution Baselines
Linux distributions generally fall into two major families based on how they package and manage software:

| Family | Major Distributions | Package Tool | Low-Level Installer | Default Firewall Service |
| :--- | :--- | :--- | :--- | :--- |
| **Debian Family** | Debian, Ubuntu, Linux Mint, Kali Linux | `apt` / `apt-get` | `dpkg` | `ufw` (Uncomplicated Firewall) |
| **Red Hat Family** | RHEL, CentOS, Rocky Linux, AlmaLinux, Fedora | `dnf` / `yum` | `rpm` | `firewalld` |

Both families share the same core POSIX commands (`ls`, `cd`, `grep`, `ps`, `systemctl`, `ip`). The primary operational difference lies in **package installation** and **firewall rule management**.

---

## Architecture & Command Flow

```text
+---------------------------------------------------------------------------------------+
|                         HOW A USER COMMAND RUNS IN LINUX                              |
+---------------------------------------------------------------------------------------+

                     [ User Terminal / SSH Session ]
                                   |
                                   v
                      +-------------------------+
                      |   Shell (Bash / Zsh)    |
                      | Reads input & resolves  |
                      | builtins, aliases, PATH |
                      +------------+------------+
                                   |
         +-------------------------+-------------------------+
         |                         |                         |
         v                         v                         v
+------------------+      +------------------+      +------------------+
| Files & Storage  |      | Process Control  |      | Network & System |
| ls, cd, cp, rm   |      | ps, top, kill    |      | ip, ping, ss     |
| grep, awk, sed   |      | systemctl        |      | uname, free, df  |
+--------+---------+      +--------+---------+      +--------+---------+
         |                         |                         |
         +-------------------------+-------------------------+
                                   |
                                   v  (System Calls: read, write, fork, socket)
                      +-------------------------+
                      |      Linux Kernel       |
                      | Manages CPU, RAM, Disks |
                      +-------------------------+
```

---

## 1. File & Directory Navigation

Use these commands to move around the directory structure and see what files exist on the system.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `pwd` | Shows the full path of where you currently are (Print Working Directory) | `pwd` | `-P` (Shows real path instead of symlink shortcut) |
| `cd` | Changes your current working folder to another path | `cd [directory]` | `cd ~` (Go home), `cd ..` (Go up 1 level), `cd -` (Go back to previous folder) |
| `ls` | Lists files and folders inside the directory | `ls [options] [path]` | `-l` (Detailed view), `-a` (Show hidden files starting with `.`), `-h` (Show sizes in KB/MB/GB), `-t` (Sort newest first) |
| `mkdir` | Makes a brand new empty folder | `mkdir [options] <name>` | `-p` (Creates missing parent folders in one go) |
| `rmdir` | Deletes an empty folder (fails safely if folder has files inside) | `rmdir <directory>` | `-p` (Removes empty parent folders along the path) |

### Real-World Examples

* **List all files, including hidden configuration files, with human-readable sizes:**
  ```bash
  ls -lah
  ```
* **Create a nested set of folders in a single step:**
  ```bash
  mkdir -p projects/lab1/data
  ```
* **Jump back to the previous folder you were working in:**
  ```bash
  cd -
  ```

---

## 2. File Creation, Copying & Deletion

Use these commands to create blank files, duplicate configurations, rename assets, and clean up disk artifacts.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `touch` | Creates a blank file or updates timestamps if the file already exists | `touch <filename>` | `-m` (Updates modification time only) |
| `cp` | Copies files or entire folders from one location to another | `cp [options] <src> <dest>` | `-r` (Recursive: copy folders), `-p` (Preserves file permissions and dates) |
| `mv` | Moves a file to another location, or renames it in place | `mv <old_name> <new_name>` | `-i` (Asks before overwriting), `-f` (Overwrites without asking) |
| `rm` | Deletes files or entire directories permanently | `rm [options] <target>` | `-r` (Recursive delete), `-f` (Force delete without confirmation prompts) |

### Real-World Examples

* **Make a quick backup copy of a configuration file before editing it:**
  ```bash
  cp -p /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
  ```
* **Rename a log file:**
  ```bash
  mv app_old.log app_archive.log
  ```
* **Safely remove a folder and everything inside it:**
  ```bash
  rm -rf old_project/
  ```

---

## 3. Viewing & Searching Text Files

Use these utilities to inspect system logs, search for error codes, and transform output streams.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `cat` | Dumps the entire contents of a file directly onto the terminal | `cat <filename>` | `-n` (Numbers every line) |
| `less` | Opens a file in a scrollable view (forward and backward) | `less <filename>` | `-N` (Show line numbers), press `q` to exit, `/` to search |
| `head` | Shows the first few lines of a file (default is 10 lines) | `head [options] <file>` | `-n <number>` (Number of lines to show) |
| `tail` | Shows the last few lines of a file (default is 10 lines) | `tail [options] <file>` | `-n <number>` (Line count), `-f` (Follow new lines in real time) |
| `grep` | Searches through files or output for matching text or regex | `grep [options] "text" <file>` | `-i` (Case-insensitive), `-r` (Search all folders), `-v` (Invert: show lines that do not match) |
| `diff` | Compares two text files line-by-line and highlights changes | `diff <file1> <file2>` | `-u` (Unified view), `-y` (Side-by-side view) |
| `sort` | Reorders lines of text alphabetically or numerically | `sort [options] <file>` | `-n` (Numeric sort), `-r` (Reverse order), `-u` (Remove duplicates) |
| `echo` | Prints a string to the terminal or writes text into a file | `echo "text"` | `>` (Overwrite file with text), `>>` (Append text to the end of file) |

### Real-World Examples

* **Watch an application log file update in real time as events happen:**
  ```bash
  tail -f /var/log/syslog
  ```
* **Search for failed login events inside security authentication logs:**
  ```bash
  grep -i "failed" /var/log/auth.log
  ```
* **Append a line to a configuration file without opening a text editor:**
  ```bash
  echo "server 192.168.1.1" >> /etc/ntp.conf
  ```

---

## 4. Process Monitoring & Task Management

Use these commands to view running programs, monitor CPU/RAM usage, and close frozen applications.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `top` | Live terminal task manager showing CPU, RAM, and running processes | `top` | Press `q` to quit, `k` to kill a process by PID, `M` to sort by memory |
| `htop` | Color-coded, interactive task manager (easier to read than `top`) | `htop` | Press `F9` to terminate tasks, `F6` to change sort columns |
| `ps` | Takes a static snapshot of currently running processes | `ps [options]` | `aux` (Show all processes for all users), `-ef` (Standard full view with parent PIDs) |
| `kill` | Stops a program by sending a termination signal to its Process ID (PID) | `kill [signal] <PID>` | `-15` (Polite request to close: `SIGTERM`), `-9` (Immediate force kill: `SIGKILL`) |
| `jobs` | Lists background tasks started in your current terminal session | `jobs` | `-l` (Includes PID numbers) |

### Real-World Examples

* **Find the Process ID (PID) of a specific service like Apache:**
  ```bash
  ps aux | grep apache2
  ```
* **Forcefully terminate a frozen program using its PID:**
  ```bash
  kill -9 14520
  ```

---

## 5. System Information, Date & Hardware Diagnostics

Use these commands to check what version of Linux you are running, how long the server has been running, and who is currently logged in.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `uname` | Displays the operating system and kernel release | `uname [options]` | `-a` (Show all information: kernel, hostname, architecture), `-r` (Kernel version only) |
| `uptime` | Shows how long the machine has been running and system load | `uptime` | `-p` (Shows uptime in readable hours and days) |
| `whoami` | Prints the exact user account you are currently operating as | `whoami` | None |
| `w` | Shows who is logged into the server and what command they are running | `w` | None |
| `finger` | Shows contact and shell details for a user (older diagnostic tool) | `finger [username]` | None |
| `cal` | Displays a simple monthly calendar in the terminal | `cal` | `-3` (Shows previous, current, and next month) |

### Real-World Examples

* **Check the system kernel and CPU architecture (e.g., x86_64 or aarch64):**
  ```bash
  uname -a
  ```
* **Quickly check how long the system has been up without reboots:**
  ```bash
  uptime -p
  ```

---

## 6. Disk Space & Memory Usage

Use these commands to verify available storage capacity, identify large folders eating up drive space, and check free RAM.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `df` | Displays available disk space across all mounted drives | `df [options]` | `-h` (Human-readable: displays in GB and MB instead of blocks), `-T` (Shows filesystem types) |
| `du` | Shows how much storage space specific folders and files take up | `du [options] [path]` | `-h` (Human-readable sizes), `-s` (Show total summary for the folder), `-d 1` (Only 1 level deep) |
| `free` | Displays used and available physical RAM and Swap memory | `free [options]` | `-h` (Displays in GB/MB), `-m` (Displays in Megabytes) |

### Real-World Examples

* **Check disk space across all drives in human-readable GB:**
  ```bash
  df -h
  ```
* **Find the 5 largest folders inside `/var` to clean up disk space:**
  ```bash
  du -h --max-depth=1 /var 2>/dev/null | sort -hr | head -n 5
  ```
* **Check live available RAM:**
  ```bash
  free -h
  ```

---

## 7. Networking, Connectivity & Remote Access

Use these utilities to test connectivity, view your IP addresses, check open listening ports, and establish secure remote shells.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `ping` | Tests whether another computer or server is reachable over the network | `ping [options] <host>` | `-c <count>` (Stop after sending a specific number of pings) |
| `ip a` | Displays IP addresses and link status on all network interfaces | `ip a` | `-br` (Brief one-line summary per interface) |
| `ifconfig` | Older legacy network tool for viewing network interfaces | `ifconfig` | `ifconfig <interface>` (Check a specific card like `eth0`) |
| `ss` | Shows open network sockets and listening ports (modern replacement for `netstat`) | `ss [options]` | `-t` (TCP), `-u` (UDP), `-l` (Listening only), `-n` (Numeric port numbers), `-p` (Show process names) |
| `ssh` | Connects securely to a remote Linux machine over port 22 | `ssh user@host` | `-p <port>` (Custom port), `-i <key.pem>` (Use private key file) |

### Real-World Examples

* **Quickly check your IP address in brief format:**
  ```bash
  ip -br a
  ```
* **Send exactly 4 ping packets to test an internet connection:**
  ```bash
  ping -c 4 8.8.8.8
  ```
* **See what services are listening on your machine and which ports they are using:**
  ```bash
  sudo ss -tulnp
  ```
* **Log in to a remote server using a private SSH key:**
  ```bash
  ssh -i ~/.ssh/mykey.pem ubuntu@192.168.1.50
  ```

---

## 8. Users, Passwords & File Permissions

Linux is a multi-user operating system. Use these commands to manage users, reset passwords, and control who can read, write, or execute files.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `sudo` | Runs a single command with root (administrator) privileges | `sudo <command>` | `-i` (Opens an interactive root terminal shell) |
| `useradd` | Creates a new user account on the system | `useradd [options] <name>` | `-m` (Creates a home folder for the user), `-s /bin/bash` (Sets login shell) |
| `passwd` | Changes the password for your account or another user | `passwd [username]` | `sudo passwd <user>` (Change another user's password) |
| `chmod` | Modifies file permissions (Read, Write, Execute) | `chmod <mode> <file>` | `-R` (Recursive for entire folder), `755` (Owner: rwx, Others: r-x), `600` (Owner only: rw-) |
| `chown` | Changes the owner and group of a file or folder | `chown <user:group> <file>` | `-R` (Recursive for entire folder) |

### Real-World Examples

* **Add a new user with a home directory and a standard bash shell:**
  ```bash
  sudo useradd -m -s /bin/bash newuser
  ```
* **Set or change the password for that new user:**
  ```bash
  sudo passwd newuser
  ```
* **Lock down a private SSH key file so only your account can read it:**
  ```bash
  chmod 600 ~/.ssh/id_rsa
  ```
* **Give ownership of a folder to the web server account:**
  ```bash
  sudo chown -R www-data:www-data /var/www/html
  ```

---

## 9. Package Management (Debian/Ubuntu vs. RHEL/CentOS)

Installing software differs between Linux distributions. Use this quick comparison table to find the right command for your environment:

| Task | Debian / Ubuntu / Mint / Kali | RHEL / CentOS / Rocky / Fedora |
| :--- | :--- | :--- |
| **Refresh package catalog** | `sudo apt update` | `sudo dnf check-update` (or `yum check-update`) |
| **Install software** | `sudo apt install -y <package>` | `sudo dnf install -y <package>` (or `yum install`) |
| **Remove software** | `sudo apt remove <package>` | `sudo dnf remove <package>` |
| **Search for software** | `apt search <keyword>` | `dnf search <keyword>` |
| **Install downloaded file** | `sudo dpkg -i package.deb` | `sudo rpm -ivh package.rpm` |
| **Check service status** | `sudo systemctl status <service>` | `sudo systemctl status <service>` |
| **Manage firewall** | `sudo ufw allow <port>/tcp` | `sudo firewall-cmd --add-port=<port>/tcp --permanent` |

### Real-World Examples

* **Install Wireshark and Curl on Ubuntu:**
  ```bash
  sudo apt update && sudo apt install -y wireshark curl
  ```
* **Install Wireshark and Curl on Rocky Linux / RHEL:**
  ```bash
  sudo dnf install -y wireshark curl
  ```

---

## 10. Compressing & Archiving Files

Use these utilities to bundle multiple files into a single archive or compress backups to save drive space.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `tar` | Packages multiple files into a single archive file (tarball) | `tar [options] -f <file.tar> <targets>` | `-c` (Create), `-x` (Extract), `-z` (Compress with gzip), `-v` (Verbose list), `-f` (Filename) |
| `zip` | Compresses files into a standard Windows-compatible `.zip` file | `zip [options] <file.zip> <targets>` | `-r` (Include folders recursively) |
| `unzip` | Extracts files from a `.zip` archive | `unzip <file.zip>` | `-d <folder>` (Extract into a specific target folder) |

### Real-World Examples

* **Create a compressed `.tar.gz` backup of a directory:**
  ```bash
  tar -czvf my_backup.tar.gz /home/user/documents/
  ```
* **Extract a `.tar.gz` archive to the current folder:**
  ```bash
  tar -xzvf my_backup.tar.gz
  ```
* **Unzip a `.zip` file into a specific folder:**
  ```bash
  unzip project_files.zip -d ./extracted_data/
  ```

---

## 11. Finding Files, Manuals & Terminal Shortcuts

Use these tools to locate missing configuration files, view help pages, and inspect previously executed shell commands.

| Command | What It Does | Basic Syntax | Useful Flags |
| :--- | :--- | :--- | :--- |
| `find` | Scans the filesystem in real time to locate files matching specific criteria | `find <path> [criteria]` | `-name "*.txt"` (Search by name), `-type f` (Files only), `-type d` (Folders only) |
| `locate` | Rapidly finds files using a pre-indexed system database | `locate <filename>` | `-i` (Case-insensitive search) |
| `whereis` | Shows where a command's binary executable, source code, and manual page live | `whereis <command>` | `-b` (Binaries only), `-m` (Manuals only) |
| `whatis` | Shows a one-line summary of what any Linux command does | `whatis <command>` | None |
| `man` | Displays the complete official instruction manual for any command | `man <command>` | Press `q` to exit, `/` to search |
| `history` | Lists the commands you previously typed in the terminal | `history` | Press `Ctrl + R` in terminal to search previous commands |
| `clear` | Wipes the current terminal screen clean | `clear` | Shortcut: `Ctrl + L` |
| `alias` | Creates a custom shorthand shortcut for a long command | `alias name='command'` | `alias` (View all current shortcuts) |

### Real-World Examples

* **Find where an Nginx configuration file is stored on the system:**
  ```bash
  find /etc -name "nginx.conf"
  ```
* **Find all `.log` files modified in the last 24 hours:**
  ```bash
  find /var/log -type f -mtime -1
  ```
* **Create a permanent shortcut for updating Ubuntu packages:**
  ```bash
  alias updateall='sudo apt update && sudo apt upgrade -y'
  ```

---

## Practical Everyday Troubleshooting Workflows

### Scenario 1: A Service Fails to Start (Web Server, Wazuh, or Splunk)
1. **Check if the service is running:**
   ```bash
   sudo systemctl status nginx
   ```
2. **If it failed, inspect the live error logs:**
   ```bash
   sudo journalctl -u nginx -n 50 --no-pager
   ```
3. **Verify the port isn't already taken by another process:**
   ```bash
   sudo ss -tulnp | grep :80
   ```

### Scenario 2: System Reports "No space left on device"
1. **Check which hard drive partition is full:**
   ```bash
   df -h
   ```
2. **Locate the largest folders consuming drive space:**
   ```bash
   sudo du -h --max-depth=1 / 2>/dev/null | sort -hr | head -n 10
   ```
3. **Clean up old cached installation packages (Ubuntu):**
   ```bash
   sudo apt clean
   ```

---

## Ethical Guidelines & Disclaimer
This guide is compiled for educational training, daily system administration, and technical reference. Commands that modify permissions (`chmod`), delete files (`rm -rf`), or kill processes (`kill -9`) should always be tested carefully in safe or virtual lab environments to avoid accidental data loss.
