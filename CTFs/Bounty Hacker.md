# **TryHackMe CTF: [Bounty Hacker](https://tryhackme.com/room/cowboyhacker)**
# Enumeration
First, we collect information about the server. To do this, we run a `nmap` scan on the target IP. `-sV` helps us identify service versions along with the ports.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/0e1dcd15-91dc-42eb-9ee7-155cc5ab959d" />
Three ports, `21`, `22` and `80` are open. The services on these ports are `ftp`, `ssh`, and `http`.

After that, we will try to discover website directories with `gobuster`

The third TryHackMe question:
<img width="1254" height="96" alt="image" src="https://github.com/user-attachments/assets/fa2cb142-1ba1-4a26-8bc6-633c024c5b86" />

To figure this out, we log in to the `ftp` server. We can do this by running `ftp <TARGET_IP>`. The `ftp` command is included in almost every mainstream distribution.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/1c2297ef-c7f6-494c-b588-c1635563da22" />
When we try to log in, we are denied access with the reason: `530 This FTP server is anonymous only.` To fix this, we can write `ftp` or `anonymous` as the user when connecting.
<img width="1333" height="768" alt="image" src="https://github.com/user-attachments/assets/189bd689-808a-49df-bebf-d3ffb5fe45f3" />
We are now logged in. Let us try listing the files using the `ls` command. You may need to run the `passive` command before running any other commands if the commands hang.
