# Windows privilege escalation

## Step 1 - Basic enumeration

### System

Basic OS information:  `systeminfo`

Patch information: `wmic qfe`

List drives: 

```text
wmic logicaldisk
list drives
mountvol
```

Running services: `tasklist /SVC`

Scheduled tasks: `schtasks /query /fo LIST /v`

Installed software:

```text
wmic product get name
wmic product get version
wmic product get vendor
```

Readable files: `accesschk.exe -uws "Everyone" "C:/Program Files"`

### User

View current user: `whoami`

View privileges: `whoami /priv`  \(pay particular attention to SEImpersonatePrivilege or SEAssignToken\)

List users: `net users`

Local groups: `net localgroup`

Specific group: `net localgroup administrators`

### Network

```text
ipconfig
ipconfig /all
arp -a
route print
netstat -ano
```

### Firewall and Antivirus

```text
sc query windefend
sc query type=service
netsh advfirewall dump
netsh firewall show state
netsh advfirewallshow currentprofile
```

### Find password strings

```text
findstr /si password *.txt
findstr /si password *.xml
findstr /si password *.ini
dir /s *pass* == *cred* == *vnc* == *.config*
findstr /spin “password” *.*
findstr /spin “password” *.*
```

## Step 2 - Automated tools

```text
winPEAS.exe
PowerUp.ps1
windows-exploit-suggester.py
Metasploit - post/multi/recon/local_exploit_suggester
```

### windows-exploit-suggester.py

* Run `systeminfo` and save the output into a text document 
* Update the database - `./windows-exploit-suggester.py --update` 
* .`/windows-exploit-suggester.py --database DBNameHere --systeminfo filepath.txt`

### Metasploit exploit suggester

* Background session `background`
* Select exploit to use
* Set Session
* Set LHOST and LPORT
* Run
