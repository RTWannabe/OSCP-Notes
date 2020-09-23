# 2 - scanning and enumeration

## Port Scanning

### Network Sweep `nmap -sn 10.11.1.1-254`

```text
-sn = disbale port scan, host discovery only, probes with ICMP and several other packets to determine availability
```

### Standard scan `nmap -A 10.11.1.5`

### Deep scan `nmap -p- -A 10.11.1.5`

### UDP scan `nmap -sU 10.11.1.5`

### Important flags

```text
-sT = TCP connect scan

-sS = TCP SYN scan

-sU = UDP scan

-sN = null scan (no flags sets, can sometimes assist with detecting open ports on firewalled systems)

-sX = Xmas tree scan (FIN, PSH, URG flags on)

-sC = runs default scripts

-sV = attempts to identify the version of the service running on ports

-O = attempts OS detection using TCP/IP fingerprinting

-A = includes -sC, -sV, -O

-p- = scan all ports, not only top 1,000

-Pn = skip host discovery and scan all addresses

-T = enables timing options, from 0 to 5 with 0 being the slowest

-oG = outputs results in a greppable format (-oG filename)

--top-ports=20 = will scan the top 20 ports, number is user specifiable
```

## Enumeration

### Banner grabbing

Netcat: `netcat: nc 10.11.1.5 4445`

Telnet: `telnet 10.10.10.10 port`

### FTP \(21\)

```text
ftp 10.11.1.5
Username: anonymous
Password: anything
```

### SSH \(22\)

Look for version info, attempt anonymous login

`ssh 10.10.10.5`

### SMTP \(25\)

`nc -nv $targetip 25`

VRFY asks a server to verify an email address

EXPN asks the server for the membership of a mailing list

Both are executed after creating a connection with netcat on port 25

### DNS \(53\)

#### DNS enumeration

`Host -t ns megacorpone.com` \(nameservers\)

`Host -t mx megacorpone.com` \(mail servers\)

`Host www.megacorpone.com` \(web server\)

#### DNS zone transfers

`Host -l megacorpone.com ns1.megacorpone.com`   or `dig axfr @10.10.10.10 Domain.com`

#### Automated tools

DNSRecon - `dnsrecon -d megacorpone.com -t axfr`

DNSenum - `dnsenum zonetransferme.com`

### HTTP \(80 and 443\)

```text
 Look for service versions, directories, source code, vulnerabilities  
```

1. Check out the page in a web browser, manually enumerate the pages looking for information
2. View the source of the available web pages, look for comments, etc.
3. Scan with Nikto: `nikto -host 10.11.1.5`
4. Scan for open directories with Dirbuster \("go faster, medium word list, applicable file extensions\)

   Microsoft IIS - .asp or .aspx; Apache - .php; other intersting files - .pdf, .doc, .xls, .xlsx, .ppt, .pptx

5. Dirsearch is another option.

   a. change directory to /opt/dirsearch

   b. run: `python3 dirsearch.py -u http://10.10.87.202:3333/ -e html -x 401,402,403`

6. View web traffic with a proxy \(Burp or Zap\)

Other possibilities:

* Scan SSL for vulnerabilities `sslscan $RHOST`

### RPC/NFS \(111\)

`nmap -sV -p 111 --script=rpcinfo $RHOST` identifies services registered with RPC Bind

`nmap -p 11 --script nfs* 10.11.1.72`

`rcpinfo -p $targetip`

`nmap -p 111 --script nfs* $RHOST`

`showmount $targetip -a`

Mounting an NFS share

1. Create a directory for the mount `mkdir home`
2. Mount the share

`sudo mount -o nolock 10.11.1.72:/SHARENAME /mntname`

If files aren't readable, you may be able to create a new user, then use sed to change the UUID of the user to match the requirements \(su username\)

### SMB \(139/NetBios and SMB/445 - NetBios often required for backward compatibility\)

SMB1 – Windows 2000, XP and 2003

SMB2 – Windows Vista SP1 and 2003

SMB2.1 – Windows 7 and Windows 2008 R2

SMB3 – Windows 8 and Windows 2012

`nmap -p 139,445 --script=smb* 10.11.1.75`

`nmap --script=smb-enum* 10.11.1.227`

`nmap -p 139,445 --script=smb-enum-users 10.11.1.75`

`nmap -v -p 139,445 -oG smb.txt 10.11.1.1-245 –open`

`nmap --script smb-vuln-* 10.10.10.40`

Tools:

`sudeo nbtscan -r 10.11.1.0/24` for enumerating NetBios information

`enum4linux -a 10.11.1.227`

`rpcclient -U "" $targetip`

`smbclient -L \\\\$ip\\$share`

Metasploit

```text
#### SNMP (161/udp)

The MIB tree contains information on the network

```nmap -sU –-open -p 161 10.11.1.1-254 -oG mega-snmp.txt
```

`snmp-check $targetip`

`onexixtyone -c community -i $targetip`

snmpwalk

Useful if community string is known, usually “public”

Enumerating entire tree `snmpwalk -c public -v1 -t10 10.11.1.219`

You can enumerate users, processes, open ports and more by including the proper MIB value after the IP address

## Nmap Scripting Engine \(NSE\)

Scripts stored in: /usr/share/nmap/scripts

`locate *.nse | grep smb`

Using the scripts:

Banner grabbing `nmap --script banner 10.10.10.5`

Vulnerability scanning `nmap --script vuln 10.10.10.5`

`nmap -p 139,445 --script=$scriptname $targetip`

The scripting parameter also accepts wildcards:

`nmap -p 139,445 --script=smb-vuln* $targetip`

`nmap -v -p 21 --script=ftp-anon.nse 10.11.1.1-254`

`nmap -v -p 139, 445 --script=smb-security-mode 10.11.1.236`
