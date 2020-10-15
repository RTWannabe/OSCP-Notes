# Linux

### Linux file system

* /bin - basic programs \(ls, cd, cat\)
* /sbin - system programs
* /etc - configuration files
* /tmp - temporary files
* /usr/bin - applications
* /usr/share - application support and data files

### Commands

* man pages - name, description, synopsis, switches
  * man -k searchterm - performs a keyword search
* ls - prints a basic file listing
  * -a displays all files, including hidden
  * -1 displays each file on a separate line
* Navigating
  * / is root directory
  * ~ is home directory
  * cd - change directory
  * mkdir -p test/{recon, exploit, report}

### Finding files

Update database `updatedb`

Three options for searching

* Locate `locate file.exe`
  * Can be manually updated with `updatedb`
* Which \(searches $PATH\) `which filename`
* Find \(recursive search\) `find / -name filename*`
  * Can also search by a number of other characteristics

### Services

Start service `service ssh start` or `systemctl start ssh`

To enable a service at runtime `systemctl enable ssh`

HTTP service `service apache2 start` or `systemctl start apache2`

### Tools

* APT - package manager, recursively satisfies packages
  * apt update - updates the apt database
  * apt upgrade - updates packages
  * apt-cache search - identifies if the package is available in the Kali repository
  * apt install - used to add packages to the system
  * apt remove --purge - completely removes packages from Kali \(data and config files\)
* DPKG - core tool used to install a package
  * does not install dependencies
