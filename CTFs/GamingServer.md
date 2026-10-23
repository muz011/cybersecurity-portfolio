# **TryHackMe CTF: [GamingServer](https://tryhackme.com/room/gamingserver)**
# Enumeration
First, we run an `nmap` scan on the server with the options `-sV`.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/dfd9a401-be72-4cc3-95a6-2345b30dfb2b" />
Two services are open. `ssh` and `http`. Let us visit the website to gather more information.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/98057c83-3705-4995-940a-824172f2a320" />
Seems like this is a gaming blog. Let us check the source of the `index.html` page of the website.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/1af35d24-be6b-426f-8a9c-4b5f54c39753" />
We find out that there is someone called `john`. This information may become useful later.

Let us do further enumeration by running `gobuster` to enumerate website directories. We will run this with the `common.txt` wordlist.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/51b61e86-a7a6-4aee-82bd-d722c067f9b4" />
We have found a `/secret` directory. Let us visit this directory to find anything useful.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/294a2648-aee3-4eaf-ae8c-f3036600fcf0" />
The directory has a file called `secretKey`. It is an RSA private key, sometimes used for `ssh` (which is the only other service open on the server). We will save this, as this may come handy.

# Exploitation

We will use `ssh2john` to convert the key into a format that `john` (or John The Ripper) can understand.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/fa2d414c-f4f0-41ff-a97b-5d8c6e7bd0ed" />

Let us use the converted file to crack the RSA private key with `john`. We will use the RockYou wordlist.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/3e5ff5d0-3153-48b5-a2ca-33d51b0d4645" />
`john` has successfully cracked the password.

Now, we try logging into ssh with the user `john` and the password `letmein`, making use of the RSA private key with it. Make sure to set the required permissions of the `secretKey.txt` file with `chmod 600 secretKey.txt`.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/91dffd39-2093-4552-bccc-dba856ce1a87" />
We have successfully obtained a shell on the machine.

# Post-Exploitation

Now we will try to escalate our privileges. We will run `find / -type f -perm -4000 2>/dev/null` to find all files that have the SUID bit set.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/2e35c205-646d-4d73-b80b-8847d3929909" />

`/usr/bin/pkexec` might be exploitable. Let us check the version.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/fba3017e-18c8-4478-beab-4c76fca1dc52" />
The version is `0.105`. We can use an exploit called [PwnKit](https://github.com/ly4k/PwnKit) to escalate our privileges.

Let us use `scp` to send the exploit to the server.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/9f3c7781-7901-41dc-98e8-8a2d0938e374" />

The exploit has been sent! Let us run it.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/9b9ac1c6-4a7c-4029-9117-f1bc34d970d2" />
We have successfully elevated our privileges.

Now we shall get both the user flag and the root flag.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/08a127ac-fb68-4a97-8c69-76f7d78c6062" />

Done!

*The intended method was complicated (exploiting lxd for privilege escalation) so I used a known exploit the machine was vulnerable to.*
