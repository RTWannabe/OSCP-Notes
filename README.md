# Reconnaissance

# Scanning and Enumeration
### Nmap ping sweep
```nmap -sn 10.11.0.1/24```
### Nmap system scan
```nmap -sS -sV -O 10.11.1.5```
### Nmap deep scan
```nmap -v -p- -sT 10.11.1.5```
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
