# Linux Interview Preparation Cheat Sheet (Cloud & DevOps)

---

## 1. Linux Fundamentals and File System

**1. Explain the Linux directory structure.**  
Linux uses a single inverted tree hierarchy starting at root (`/`). All directories, files, devices, and mounted drives branch under `/`.

**2. What is the difference between `/etc`, `/var`, `/tmp`, `/home`, `/opt`, and `/usr`?**  
* `/etc`: System configuration files.  
* `/var`: Variable data (logs, databases, mail queues).  
* `/tmp`: Temporary files cleared on reboot.  
* `/home`: User home directories.  
* `/opt`: Optional third-party software packages.  
* `/usr`: User binaries, documentation, and shared libraries.

**3. What is the difference between a hard link and a soft link?**  
* **Hard Link:** Points directly to the file’s inode. Sharing the same inode data; cannot span across different filesystems or link to directories.  
* **Soft Link (Symlink):** Points to the target file's path (like a shortcut). Has a distinct inode; breaks if the original file is deleted or moved.

**4. What happens when you execute a Linux command?**  
The shell parses input, checks for aliases/built-ins, searches for the binary in directories listed in `$PATH`, forks a child process using `fork()`, executes the binary via `execve()`, and returns an exit code ($?).

**5. What is the difference between `$PATH` and `$HOME`?**  
* `$PATH`: Environment variable listing colon-separated directory paths searched when running executable commands.  
* `$HOME`: Environment variable specifying the current user's home directory path.

**6. Explain stdin, stdout, and stderr.**  
* `stdin` (0): Standard input stream (keyboard/input stream).  
* `stdout` (1): Standard output stream (normal terminal output).  
* `stderr` (2): Standard error stream (error messages).

**7. What is the difference between `>` and `>>`?**  
* `>`: Redirects output to a file, **overwriting** existing content.  
* `>>`: Redirects output to a file, **appending** to existing content.

**8. What is a pipe (`|`) in Linux?**  
A pipe connects the `stdout` of one command directly to the `stdin` of another command (`cmd1 | cmd2`).

---

## 2. File and Directory Management

**1. How do you find a particular file in Linux?**  
```bash
find /path -name "filename.txt"
# Or for quick database lookups:
locate filename.txt
```

**2. How do you search for a specific word inside multiple files?**  
```bash
grep -rnw '/path/to/dir/' -e 'search_word'
```

**3. How do you find files larger than 100 MB?**  
```bash
find /path -type f -size +100M
```

**4. How do you find files modified in the last 24 hours?**  
```bash
find /path -type f -mtime -1
```

**5. How do you copy directories recursively?**  
```bash
cp -r /source_dir /destination_dir
```

**6. How do you delete files older than 7 days?**  
```bash
find /path -type f -mtime +7 -delete
```

**7. How do you compare two files?**  
```bash
diff file1.txt file2.txt
# Or line-by-line visually:
cmp file1.txt file2.txt
```

---

## 3. Linux Permissions and Ownership

**1. Explain `chmod 755`.**  
* Owner: Read + Write + Execute (`7` = rwx)  
* Group: Read + Execute (`5` = r-x)  
* Others: Read + Execute (`5` = r-x)

**2. What is the difference between `644` and `755`?**  
* `644`: Owner has Read/Write (rw-); Group and Others have Read-only (r--). Files cannot be executed.  
* `755`: Owner has Read/Write/Execute (rwx); Group and Others have Read/Execute (r-x). Enables executable scripts/directories.

**3. What does `chmod 777` mean?**  
Grants Read, Write, and Execute permissions to Everyone (Owner, Group, Others). Highly insecure and discouraged in production.

**4. Difference between `chmod` and `chown`?**  
* `chmod`: Changes file/directory access **permissions** (read/write/execute).  
* `chown`: Changes file/directory **ownership** (user and group).

**5. What is `umask`?**  
A default setting that masks (subtracts) permissions when creating new files and directories (Default base permissions: 666 for files, 777 for directories).

**6. What is a sticky bit?**  
A permission bit set on directories (e.g., `/tmp`, mode `1777`) ensuring that only the file owner or root can delete or rename files inside that directory.

**7. How do you give a user permission to execute a script?**  
```bash
chmod +x script.sh
```

**8. Why would you get a "Permission Denied" error?**  
You lack the necessary read, write, or execute bit permissions on the targeted file, parent directory path, or you aren't running as root/sudo.

---

## 4. Process Management

**1. What is a process?**  
An instance of a program currently executing in memory.

**2. What is the difference between a process and a thread?**  
* **Process:** An independent program with its own isolated memory space.  
* **Thread:** A lightweight execution unit within a process that shares the parent process's memory space.

**3. What is PID and PPID?**  
* **PID:** Process Identifier (unique ID of a process).  
* **PPID:** Parent Process Identifier (ID of the process that launched it).

**4. How do you find a running process?**  
```bash
ps aux | grep process_name
# Or:
pgrep process_name
```

**5. How do you kill a process?**  
```bash
kill <PID>
# Or by name:
pkill process_name
```

**6. Difference between `kill` and `kill -9`?**  
* `kill` (`SIGTERM` / signal 15): Gracefully asks a process to terminate, allowing cleanup.  
* `kill -9` (`SIGKILL` / signal 9): Instantly kills the process at the OS level; cannot be caught or ignored.

**7. What is a zombie process?**  
A process that has completed execution but remains in the process table because its parent hasn't read its exit status (`wait()` call).

**8. What is a defunct process?**  
Another name for a zombie process (state `Z`).

**9. What is the difference between foreground and background processes?**  
* **Foreground:** Takes over the terminal session, blocking new input until completed.  
* **Background:** Runs asynchronously in the background (`command &`), allowing the shell session to remain interactive.

**10. What is a daemon?**  
A background service running continuously without direct user interaction (e.g., `sshd`, `nginx`, `dockerd`).

**11. What is the difference between `nice` and `renice`?**  
* `nice`: Launches a **new** command with a specified priority level (-20 highest to 19 lowest priority).  
* `renice`: Alters the priority level of an **already running** process using its PID.

---

## 5. Networking in Linux

**1. How do you check the IP address of a Linux server?**  
```bash
ip addr show
# Or:
hostname -I
```

**2. How do you check whether a port is listening?**  
```bash
sudo ss -tuln
# Or:
sudo netstat -tuln
```

**3. How do you check connectivity between two servers?**  
```bash
ping <ip_or_hostname>
# Or test specific port:
nc -zv <ip> <port>
```

**4. Difference between TCP and UDP?**  
* **TCP:** Connection-oriented, reliable, guarantees order and packet delivery (e.g., HTTP, SSH).  
* **UDP:** Connectionless, faster, no delivery guarantee or ordering overhead (e.g., DNS, video streaming).

**5. What is DNS?**  
Domain Name System: translates human-readable domain names (e.g., `example.com`) into IP addresses.

**6. How do you troubleshoot DNS resolution issues?**  
* Use `dig example.com` or `nslookup example.com`.  
* Inspect local override mapping in `/etc/hosts`.  
* Check resolver settings in `/etc/resolv.conf`.

**7. What is the difference between `curl` and `wget`?**  
* `curl`: Tool for transferring data over network protocols (supports API calls, headers, payloads). Output defaults to `stdout`.  
* `wget`: Non-interactive command-line downloader built primarily to fetch files and recursively download web pages directly to disk.

**8. How do you check network connections?**  
```bash
ss -ant
# Or:
netstat -an
```

**9. How do you check routing tables?**  
```bash
ip route show
# Or:
netstat -rn
```

**10. What is the purpose of `/etc/hosts`?**  
A local file mapping IP addresses to hostnames, bypassing DNS resolution queries.

**11. What is the difference between public and private IP addresses?**  
* **Public IP:** Globally unique address reachable over the internet.  
* **Private IP:** Reserved non-routable internal address (e.g., `10.x.x.x`, `172.16.x.x`, `192.168.x.x`) used within a private local network or VPC.

**12. How do you check whether port 8080 is accessible?**  
```bash
nc -zv <host> 8080
# Or:
telnet <host> 8080
```

---

## 6. Disk and Storage Management

**1. How do you check disk utilization?**  
```bash
df -h
```

**2. Difference between `df` and `du`?**  
* `df` (Disk Free): Reports disk space usage at the file system level.  
* `du` (Disk Usage): Estimates file/directory space consumption recursively.

**3. What happens when a Linux server runs out of disk space?**  
Services crash, log generation fails, database writes fail, temporary locks break, and interactive logins/sessions may freeze.

**4. How do you identify which directory is consuming the most space?**  
```bash
du -ah /path --max-depth=1 | sort -hr | head -n 10
```

**5. What is an inode?**  
An Index Node data structure storing metadata about a file (size, permissions, ownership, block locations), excluding its filename and actual content.

**6. What happens when inodes are exhausted?**  
No new files or directories can be created, even if `df -h` shows available disk space. (Check using `df -i`).

**7. What is a mount point?**  
A directory path in the directory hierarchy used to access a storage device or filesystem partition.

**8. How do you mount an additional EBS volume?**  
1. Format volume (if empty): `sudo mkfs -t ext4 /dev/xvdf`  
2. Create mount path: `sudo mkdir /data`  
3. Mount: `sudo mount /dev/xvdf /data`

**9. What is `/etc/fstab`?**  
A system configuration file mapping filesystems and storage devices to mount points, ensuring automount on system boot.

**10. How do you expand a Linux filesystem?**  
1. Resize block partition (e.g., `growpart /dev/xvda 1`).  
2. Resize filesystem: `resize2fs /dev/xvda1` (for `ext4`) or `xfs_growfs /` (for `xfs`).

---

## 7. Logs and System Monitoring

**1. Where are Linux system logs stored?**  
Inside `/var/log` (e.g., `/var/log/syslog`, `/var/log/messages`, `/var/log/auth.log`).

**2. How do you check system logs?**  
```bash
tail -f /var/log/syslog
# Or using systemd:
journalctl -xe
```

**3. How do you investigate an application failure?**  
Check service status (`systemctl status app`), inspect service logs (`journalctl -u app`), review `/var/log` logs, verify disk space (`df -h`), check memory (`free -m`), and test ports (`ss -tuln`).

**4. How do you check CPU utilization?**  
```bash
top
# Or:
htop / mpstat
```

**5. How do you check memory utilization?**  
```bash
free -h
```

**6. What is `journalctl`?**  
A command-line tool used to view and query binary logs collected by the `systemd-journald` service.

**7. How do you check logs of a particular service?**  
```bash
sudo journalctl -u nginx.service -n 100 --no-pager
```

**8. How do you identify a memory-consuming process?**  
```bash
ps aux --sort=-%mem | head -n 10
# Or sort by memory inside top (press Shift + M)
```

**9. What is load average?**  
The average number of runnable or uninterruptible threads waiting for CPU/disk I/O resources over 1, 5, and 15-minute intervals (seen via `uptime`).

**10. Difference between RAM and swap?**  
* **RAM:** High-speed physical memory.  
* **Swap:** Dedicated disk space used as virtual memory when physical RAM becomes exhausted.

---

## 8. Services and Package Management

**1. What is `systemd`?**  
The default init system and service manager for modern Linux OS, used to bootstrap the user space and manage background processes/daemons.

**2. What is `systemctl`?**  
The CLI command used to inspect and control the `systemd` system and service manager.

**3. How do you start, stop, and restart a service?**  
```bash
sudo systemctl start service_name
sudo systemctl stop service_name
sudo systemctl restart service_name
```

**4. How do you enable a service at boot?**  
```bash
sudo systemctl enable service_name
# Enable and start immediately:
sudo systemctl enable --now service_name
```

**5. Difference between `restart` and `reload`?**  
* `restart`: Stops and restarts the process completely (causes brief downtime).  
* `reload`: Re-reads configuration files in-place without stopping the active running process.

**6. How do you check service status?**  
```bash
systemctl status service_name
```

**7. What is the difference between `apt` and `yum`?**  
* `apt`: Package manager for Debian-based distros (Ubuntu, Debian) handling `.deb` packages.  
* `yum` / `dnf`: Package manager for RedHat-based distros (RHEL, CentOS, Fedora, Amazon Linux) handling `.rpm` packages.

**8. How do you install Nginx?**  
```bash
# Ubuntu/Debian:
sudo apt update && sudo apt install nginx -y

# RHEL/CentOS:
sudo yum install nginx -y
```

---

## 9. Users, Groups and SSH

**1. How do you create a Linux user?**  
```bash
sudo useradd username
# Or interactive:
sudo adduser username
```

**2. How do you delete a user?**  
```bash
sudo userdel -r username
```
*(The `-r` flag removes the user's home directory and mail spool).*

**3. How do you add a user to a group?**  
```bash
sudo usermod -aG groupname username
```

**4. What is `sudo`?**  
"SuperUser DO": Allows an authorized non-root user to execute commands with elevated root/administrative privileges.

**5. What is SSH?**  
Secure Shell: A cryptographic network protocol for operating network services and shell access securely over an unsecured network.

**6. How does SSH key-based authentication work?**  
Asymmetric cryptography: The client sends a request signed with their **Private Key**. The server decrypts it using the user's registered **Public Key** (`~/.ssh/authorized_keys`) to verify authenticity without sending passwords across the wire.

**7. Where are SSH keys stored?**  
* Client local user directory: `~/.ssh/id_rsa` (private), `~/.ssh/id_rsa.pub` (public).  
* Remote target host: `~/.ssh/authorized_keys`.

**8. What is the purpose of `/etc/passwd` and `/etc/shadow`?**  
* `/etc/passwd`: Publicly readable system file listing user account information (username, UID, GID, home directory, shell).  
* `/etc/shadow`: Restricted system file containing encrypted user passwords and account expiration details.

**9. How do you troubleshoot SSH connection failures?**  
Execute SSH with verbose output:
```bash
ssh -vvv user@hostname
```
Check firewall rules, verify SSH port 22 open status, ensure proper file permissions on `~/.ssh` (`700`) and `authorized_keys` (`600`), and check `/var/log/auth.log` on the target server.

---

## 10. Shell Scripting and Automation

**1. What is shell scripting?**  
A text file containing a sequence of command-line instructions executed sequentially by a shell interpreter to automate manual tasks.

**2. Difference between Bash and Shell?**  
* **Shell:** The generic specification/interface (e.g., `sh`, POSIX standard).  
* **Bash (Bourne Again Shell):** An enhanced implementation of the Unix shell standard with extra features like history, auto-completion, and advanced scripting functions.

**3. What is a shebang?**  
The first line in a script (`#!/bin/bash`) specifying the exact interpreter binary path used to execute the script file.

**4. What are variables in shell scripting?**  
Storage containers assigned using `NAME="value"` and referenced using `$NAME` or `${NAME}`.

**5. What are positional parameters?**  
Command-line arguments passed to a script upon execution (`$1` is 1st argument, `$2` is 2nd argument, `$0` is the script name itself).

**6. What is `$?`?**  
An environment parameter returning the **exit status code** of the last executed command (0 = success, non-zero = error).

**7. Difference between `$*` and `$@`?**  
When enclosed in double quotes:  
* `$*`: Combines all positional parameters into a single string ("$1 $2 $3").  
* `$@`: Expands all positional parameters into distinct individual strings ("$1" "$2" "$3").

**8. What are `if-else` statements?**  
Conditional structures executing code blocks based on specified evaluations:
```bash
if [ "$VAR" -eq 10 ]; then
   echo "Matches"
else
   echo "No Match"
fi
```

**9. What are loops?**  
Control structures that repeat a block of code until a condition is satisfied (`for`, `while`, `until`).

**10. What is `cron`?**  
A time-based job scheduler daemon in Unix-like operating systems used to run background tasks periodically.

**11. How do you schedule a script?**  
Edit the user crontab using `crontab -e` and add an entry:
```text
# Min Hour Day Month DayOfWeek Command
0 2 * * * /path/to/script.sh
```

**12. How do you handle errors in Bash?**  
Add safety flags at the beginning of your script:
```bash
set -e # Exit immediately on error
set -u # Exit on unset variables
set -o pipefail # Fail pipeline if any command fails
```