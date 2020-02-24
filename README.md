# OSCP exam notes

## Reconnaissance

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
