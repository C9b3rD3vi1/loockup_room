# lookup

Lookup offers a treasure trove of learning opportunities for aspiring hackers. This intriguing machine showcases various real-world vulnerabilities, ranging from web application weaknesses to privilege escalation techniques. By exploring and exploiting these vulnerabilities, hackers can sharpen their skills and gain invaluable experience in ethical hacking. Through "Lookup," hackers can master the art of reconnaissance, scanning, and enumeration to uncover hidden services and subdomains. They will learn how to exploit web application vulnerabilities, such as command injection, and understand the significance of secure coding practices. The machine also challenges hackers to automate tasks, demonstrating the power of scripting in penetration testing.

Scan with nmap port scanner, I noticed that port 80 AND port 22 are identified in the scan

![nmap scan](/nmap_scan.png)

Try running the ip in web application fails with a redirect to **lookup.thm**

I added the my machine_ip address to host file

![host file modification](/host_scan.png)

Now when i try to browse...I can see a login page displaying!!

![discovered login page](/login.png)

We have to find Credentials to the Login page @Port 80
We can do this by couple of ways , by using burp suite or use Hydra to do a Brute-force attack

We will choose the Hydra way since this is a beginner friendly room

Let’s First try using the default credentials like →
    admin:admin OR admin:password

![Find the credentials](/passloginatt.gif)

Default credentials do not work here

    Wrong password. Please try again.
    Redirecting in 3 seconds.

The Form action uses login.php →

![php login function](/loginphp.png)

let user ***hydra*** to brute force authentication with default username as ***admin**

hydra -l admin -P /usr/share/wordlists/rockyou.txt lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong password. Please try again."

hydra:

The main tool for conducting brute-force attacks against login services.
-l admin:

Specifies the username to test.

In this case, the username is fixed as admin.

    -P /usr/share/wordlists/rockyou.txt:

Specifies the password list to use.

Here, the wordlist is the popular rockyou.txt, commonly used in brute-force attempts.

    ipaddress.com:

The target server’s domain or IP address. Replace this with the actual address of the target.

    http-post-form:

Specifies the service/module to attack. In this case, it’s a web form using the HTTP POST method.

    "/login.php:username=^USER^&password=^PASS^:Wrong Password. Please try again.":

/login.php: The login form's URL endpoint.

username=^USER^&password=^PASS^:

The POST parameters sent to the server.
^USER^: Placeholder for the username being tested
(replaced by -l admin).

^PASS^: Placeholder for the password being tested
(taken from rockyou.txt).

Wrong Password. Please try again.: The failure message from the server. Hydra uses this text to identify incorrect login attempts.

Hydra sends HTTP POST requests to /login.php, replacing ^USER^ with admin and ^PASS^ with each password from the rockyou.txt wordlist.

If the response does not contain Wrong Password. Please try again., Hydra assumes the credentials are valid.

Valid Credentials that were found

![credentials found](/credentials.png)

    admin : password123

Let’s Try to login using the just Found Credentials

After trying to login to the above found credentials , we still were not able to login to the page , which could mean that we need to find other username for the login page

Now we will use the Password → ***password123*** to find other username for the login page

    hydra -L /usr/share/seclists/Usernames/Names/names.txt -p password123 lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:F=try again"

![real credentials](/realcredentials.gif)

    jose:password123

![real credentials](/hydra.png)

Let’s now Trying login in with these credentials → jose:password123

![login credentials redirect](/loginredirect.gif)

After trying to login with the credentials → jose:password123, We are redirected to a new URL → files.lookup.thm

Let’s add an entry of → files.lookup.thm to → /etc/hosts

![host files](/hostfiles.png)

![Add a new entry of → files.lookup.thm](/hostfiles.gif)

You can ping files.lookup.thm to check connectivity

![Now access the host files](/accessscred.gif)

After refreshing the page we find a page with 20 Items
There are 18 Files with padlock icon which could indicate access restriction
There are 2 Files without the padlock icon which could mean file could be read

![file access restriction](/fileaccess.webp)

Let’s First check the 2 Files without padlock

![Access restriction](/file_accessibility.gif)

Under the file credentials we found → think:nopassword

Can this be the Credentials for SSH login ?

![ssh login restriction](/ssh.gif)

Web File Manager is Running on → elFinder Version 2.1.47

![software version](/elfinder.gif)

![software version](/efinder.webp)

Let’s Find the Vulnerability of elFinder using Searchsploit on CLI

![msfconsole](/msf.png)

After Selecting the correct module → use 0 …. Let’s set the options parameter

![msfconsole](/console.gif)

Exploiting , we get a metasploit shell access

![hacker](/hacked1.png)

Let’s Get the shell of the Meterpreter

![shell](/shell.png)

We got the www-data Shell from the Metasploit exploit

Privilege Escalation To User Account →

Remember we found think credentials ? ,

Now we need to get the password to user ***think***

Let’s Now check for SUID Binaries

    find / -perm /4000 2>/dev/null

![binaries](/binary.gif)

    /usr/sbin/pwn

/usr/sbin/pwn Looks interesting

On execution this binary returns the id value and then tried to grab the File → ***/home/www-data/.passwords**

We have to get to the .passwords file

on the Binary the Path is set to user www-data , if we are able to manipulate the path by changing it to tmp directory → inside /tmp directory we have set the current path to /tmp because /tmp is World-readable and then creating a bash file which impersonates as think user

Let’s Change the Directory to /tmp , and create a bash file which impersonates the user think →

Let’s create a file named id

    echo -e '#!/bin/bash\n echo "uid=33(think) gid=33(think) groups=33(think)"' > id

Don’t forget to CHMOD +x to id file

    chmod +x id

Now Let’s try running → /usr/sbin/pwm now , the SUID will now set the user to think and returns the passwords file

    /usr/sbin/pwm

![found passwords](/passwords.gif)

Find the password to username ***think*** by brute force using ***hydra***


I found the password , Let’s login to the User think with the newly discovered password using SSH


I Have found the → User Flag

![flag0001](/flag001.png)

## Privilige Escalation To Root

While logged in to user → ***think*** Let’s check it’s sudo privileges

    sudo -l

![privilige Escalation To Root](/GOTBin.gif)

![privilige Escalation To Root](/sudo.webp)


We can now use the above command to get the id_rsa Key to get to root login directly 

Usually the location for id_rsa is stored under the .ssh folder 

    sudo /usr/bin/look '' /root/.ssh/id_rsa

id_rsa key

![id_rsa key](/id_rsa.png)

Now after getting the id_rsa key , we need to save it to a file locally and give id_rsa permissions by 

    chmod 600 id_rsa

We have the id_rsa for root account, Let’s Login to root via SSH

    ssh -i id_rsa root@lookup.thm

Root access

![Access root account](/root.png)

Root account access, Flag obtained

![Access root account](/root.gif)