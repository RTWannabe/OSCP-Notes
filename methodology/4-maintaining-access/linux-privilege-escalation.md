# Linux privilege escalation

Enumerate

* What are your current permissions?

  ```text
  ```hostname
  ```

  `uname -a`

  `uname -r` Kernel version

  ```text
  ```history
  ```

  `pwd`

* Are you in the sudoers file? Can you sudo anything without a pw?

  `sudo -l`

  `cat etc/sudoers`

* Who else is has/is logged in?

  ```text
  ```w
  ```

  `last`

* Gather network info

  `ifconfig -a`

  ```text
  ```lsof -i
  ```

* Services/software

  `ps aux`

  `ps -ef`

* list versions

  `dpkg -l`

  `httpd -v`

  `mysql --version`

  `python --version`

  `ruby -v`

* Interesting files to review

  SUID `find / -perm -u=s -type f 2>/dev/null`

  `cat etc/passwd`

* files that run as root
* chron jobs

  `ls -la /etc/cron*`

  * SSH keys

  `ls -la ~/.ssh/`

* Linux version info:

`uname -ar` `cat /etc/issue` `cat /etc/*-release` `cat /etc/lsb-release` Debian-based machines `cat /etc/redhat-release` Redhat-based machines

Upload and run LinEnum.sh \(Documents/Tools\)

Use searchsploit to look for kernel or software vulnerabilities

Use any passwords found to attempt to become root by using: `sudo su`

Refer to gotmilk's article: [https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)
