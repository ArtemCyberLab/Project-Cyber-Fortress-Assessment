The goal of my project was to study and test the security of a server on the TryHackMe platform. This room incorporates elements of both Red Teaming and Blue Teaming, allowing me to develop skills on both the offensive and defensive sides of cybersecurity. To begin, I conducted a full port scan using Nmap:

nmap -sC -sV -A -p- 10.10.3.53

After this, I performed a detailed scan of the discovered ports (22, 80, 8089, 8000). Then, I used Gobuster to search for interesting directories on the server:

gobuster dir -u http://admin1337special.hackme.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

I discovered that the website checks the request origin using window.location.hostname. To obtain the file inviteCode1337HM.php, I needed to add an entry to /etc/hosts and modify the page code to point to the required file. After these changes, the server returned an invite code, granting me access to a guest account. After entering the invite code, I accessed a page resembling a well-known website. There were two login options: free and VIP. Since I didn’t have VIP access, I bypassed the check by changing true to false in Burp Suite. Upon gaining VIP access, I found a link that allowed me to launch the "AttackBox" on TryHackMe. This gave me the ability to execute commands on the server. Using Burp Suite, I was able to send commands to the server. However, there was a filter blocking most commands. In the page code, I found that the cat command could be used to view the contents of the config.php file.

On one of the discovered pages, I encountered a 403 error. I had a secure admin token but no place to use it. I decided to run Gobuster again with the following command:

gobuster dir -u http://admin1337special.hackme.thm:40009/public/html/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

Gobuster found a /login page. After trying standard login pairs (admin:admin), I realized an SQL injection was needed. Entering the ' character in the input field triggered a database error. I saved the intercepted request from Burp Suite into a file and ran SQLmap:

sqlmap -r request.txt --dump --batch

After SQLmap completed its task, I obtained a database dump and gained access to the admin panel. At the end, I returned to the main page and retrieved the flag, confirming the successful completion of the challenge.

