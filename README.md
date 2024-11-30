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
