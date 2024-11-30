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

