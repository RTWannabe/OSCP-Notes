# HTB - Blunder

![](../.gitbook/assets/image%20%281%29.png)

This writeup examines the Blunder machine from Hack the Box. This machine reinforces the value of deep enumeration and is great when preparing for various certification exams.

#### Scanning and enumeration

We start by scanning the machine with Nmap to identify open ports. Our scan reveals two ports: FTP on port 21 \(closed\), and an HTTP server operating on port 80.  We immediately proceed to enumerating the web server; however, we also start a UDP scan to be sure that we aren’t missing any other possible attack vectors \(update: no UDP ports open\).

![](https://oncyberwar.com/wp-content/uploads/2021/01/image.png)

Next, we manually view the page to look for information and check hyperlinks. The page is a simple blog that contains a few articles. The page also has a link for an “about” page and the “created by” link in the footer links to a Twitter profile.![](https://oncyberwar.com/wp-content/uploads/2021/01/image-18.png)

Next, we move on to scanning and directory busting. Nikto reveals the robots.txt and the presence of /admin/config.php. By navigating to the page discover a ‘BLUDIT’ login page.![](https://oncyberwar.com/wp-content/uploads/2021/01/image-19.png)

A quick web search for BLUDIT suggests there is an RCE exploit available, but it requires login credentials—we’ll return to this later.

Basic credential guesses aren’t successful so we continue with more through directory busting.  Much like the Haircut machine, this machine reminds us of the importance of thorough directory busting. Running dirsearch a second time to search for txt and pdf files provides an additional clue:  ‘/todo.txt.’![](https://oncyberwar.com/wp-content/uploads/2021/01/image-20.png)

Navigating to the document we learn that the root of the page \(as suspected\) is a CMS.  The bottom note is also interesting, as it suggests a possible username beyond the seemingly unrelated Twitter page.  With this information we may be able to brute force the login.

Brute forcing can be a bit of an art, but its always worthwhile to fully understand how the web page is sending login requests to the server.  For this we use a proxy interceptor \(Burp Suite, ZAP, etc.\).![](https://oncyberwar.com/wp-content/uploads/2021/01/image-21.png)

Reviewing the request in Burp Suite we find that the login attempt is made via POST, but more importantly we identify that the request is accompanied by a CSRF token. After some experimentation it appears this CSRF token is used by BLUDIT to temporarily lockout accounts after 10 failed login attempts. A quick Google search identified [this](https://rastating.github.io/bludit-brute-force-mitigation-bypass/) article, which includes a POC for bypassing this protection.  The POC requires modification to import external wordlists; however, after a couple attempts I had a working brute force tool that bypassed the lockout. 

**Modified tool:** [https://github.com/sourtimez/Bludit-CMS-Brute-Force-Mitigation-Bypass](https://github.com/sourtimez/Bludit-CMS-Brute-Force-Mitigation-Bypass)

Despite the working tool our brute force attempt is unsuccessful.  Perhaps the answer is contained within the content of the CMS?  To check, we use a tool called CeWL to generate a custom wordlist from the contents of the page.  \(\*\*IMO this is a bit unrealistic for a CMS with only three articles, but may be useful for larger corporate websites so there is still a valuable lesson to learn\*\*\)

```text
cewl -m 4 http://10.10.10.191 -w wordlist.txt
```

Using this wordlist we find a matching password.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-22.png)

#### Establishing a foothold

Next, we return to the previously identified Bludit RCE located at [https://www.exploit-db.com/exploits/48701](https://www.exploit-db.com/exploits/48701)

To launch the exploit, we download and modify the necessary portions of the exploit code, paying careful attention to create the required .png and .htaccess files.  After launching the exploit we receive confirmation of successful uploads.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-23.png)

Finally, we start a netcat listener to catch our shell and navigate to the target page \(identified in the exploit code\). Success, we have a shell!

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-24.png)

#### Privilege escalation

Our initial shell is somewhat limited and despite several attempts I am unable to upgrade.  Instead, I opt to configure a [better php reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) and upload it to /var/www/bludit-3.9.2/reverse.php with `wget`.  Navigating to the folder in our restricted shell confirms the upload was successful.

 ![](https://oncyberwar.com/wp-content/uploads/2021/01/image-25.png)

To launch the newly uploaded shell start another netcat listener and navigate to the appropriate URL in the browser.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-26.png)

With a stable shell we can now upgrade to TTY using python and enable tab autocompletion.

```text
python -c "import pty; pty.spawn('/bin/bash')"
```

Next, we move on to looking for privilege escalation opportunities. Nothing stands out from our basic search commands so we upload and run [LinPeas.sh](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS). Nothing stands out in our LinPeas scan so we continue with more in depth manual enumeration.  The following command searches for passwords and provides color coded output of the results:

```text
grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null
```

Scrolling through the results \(there are a lot!\) we identify several hashes located in the Bludit directory.  These hashes are present in the ‘users.php’ file for both installed versions of Bludit.  Of note, /var/www/bludit-3.10.0a/bl-content/databases/users.php contains the following:

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-27.png)

Entering the hash on [Crackstation.net](https://crackstation.net/) reveals that Hugo’s bludit password is ‘**Password120**.’  Fortunately, this user also reused this password and we pivot to the account with `su hugo`. \(There are also hashes for fergus and the admin user; however, there are not matches available in Crackstation for these additional hashes\).![](https://oncyberwar.com/wp-content/uploads/2021/01/image-28.png)

After recovering the user.txt flag from home/hugo, we begin enumerating the account .  `sudo -l` indicates some privileges, and through a quick Google search we find that the version of sudo installed on the target has an available PE exploit. This exploit works by "tricking" sudo into executing /bin/bash as root with `sudo -u#-1 /bin/bash`.  You can read more about this vulnerability [here](https://www.exploit-db.com/exploits/47502):

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-29.png)

The root.txt in the /root folder.
