---
title: Brute It - TryHackMe CTF
slug: brute-it-tryhackme-ctf
dek:  A beginner TryHackMe CTF on reconnaissance, brute forcing, hash cracking and privilege escalation.
author: takibyte
date: 2026-10-30
category: ctf
tags: [ CTF, TryHackMe, Reconnaissance, Privilege Escalation ]
cover: 8_admin_response.webp
---

## Introduction

The Brute It CTF found on TryHackMe [here](https://tryhackme.com/room/bruteit), I found to be a great beginner CTF challenge room. It is nice because it covers a classic basic pentesting pattern: reconnaissance, initial access/getting a shell, and then privilege escalation/getting root access on the target machine. These are core steps in any cybersecurity penetration testing framework, and it was fun to go through the stages in a straightforward way.

To complete this CTF I connected my Kali Linux VM to the TryHackMe network via VPN, and started the target machine. When the target machine instance became available, an IP address for target machine appeared. The target machine I received was `10.146.186.112`, this will be the IP all scans and tests will be directed towards.

```terminal title=target_ip.txt
10.146.186.112
```

First thing to do is to make sure the VPN connection is working, and that I can actually connect to the target machine, therefore we'll do a quick ping scan as shown here.

![ping scan](./images/1_ping.webp)

Now I'll be going through the questions for this CTF from start to finish.

## Reconnaissance

 First we can start with some reconnaissance, the process of scouting and mapping out the network attack surface - looking for any open ports and services, or anything else.

### How many ports are open?

The first question asks how many ports are open. To test this we'll use Nmap, an industry standard network mapping and scanning tool.

Scanning for open ports is simple, we'll use the following command to do a stealth/half-open scan using `-sS` including the version information flag `-sV`, which will give extra intel required for the next three questions. The whole command is:

```
sudo nmap -sS -sV 10.146.186.112
```

Using the command above to scan the target we get the following results as shown in the below.

![nmap scan](./images/2_nmap.webp)

We can clearly see there are ||2|| ports open, tcp port 22 and tcp port 80.


### What version of SSH is running?

Since we already ran the scan with version detection, we can look for the open port 22 line where we can notice the service is ssh (as expected), and that the version of ssh is: 

:::spoiler 
OpenSSH 7.6p1
:::

### What version of Apache is running?

Again, within the same scan report we can answer this question too. Apache is web server software, so it'll make sense for it to be found on the version details for the port 80 http service line, and here we can see the Apache version is: 

:::spoiler
2.4.29
:::

### Which Linux distribution is running?

Now we need to look for any information about the Linux distribution. Thankfully this is also found alongside the version information from the scan. Looking back at the report, on the version information section for either of the open ports, we can see some references for ||Ubuntu|| being the Linux distribution.


### What is the hidden directory?

At this point be have found a few useful pieces of information in the reconnaissance so far. We have two open ports to target. We also know that one of them is a web server, because it is port 80 running an http service with a particular Apache version.

We could take a few different approaches at this point, but the next question is quite clear in the intended direction. We need to find the hidden directory. This would likely be referencing a web directory as we have access to the public facing web server already at: http://10.146.186.112

If we visit the index page of the web server we just see a default landing page for an Apache web server.

A tool that is perfect for this is Gobuster, using it we can do a directory scan with `dir`, provide a target URL with `-u`, and provide a directory wordlist to automatically scan from using the `-w` flag. The command I used was the following:

```
gobuster dir -u http://10.146.186.112 -w /usr/share/wordlists/dirb/common.txt
```

![gobuster](./images/3_gobuster.webp)

Looking at the image above, we can see results from the gobuster scan, and in these results we can identify a very pertinent looking hidden directory ||/admin||.

Now we have completed the reconnaissance part. We now have a couple of open ports with services to target, an http web server, and an ssh service. We have also now have discovered a hidden directory which we can target in the next phase 'Getting a shell'.


## Getting a shell

In this phase of the CTF, we'll move on from reconnaissance, and now attempt to get a shell on the target machine.

### What is the user:password of the admin panel?

Naturally, after discovering the newly found directory `/admin`, we will want to visit it at http://10.146.186.112/admin as shown in the image below.

![admin page](./images/5_admin_page.webp)

Here we can see an admin login panel. Going along with the question, we want to find out the user:password of the admin panel.

The first thing I did was view the page source by right-clicking and selecting view source.

![gobuster](./images/6_admin_source.webp)

In this image we can see the html source code of the admin panel. There is some useful information here.

The first thing that stands out is the comment at the bottom. This comment gives away two important bits of information.

1. There is a person/administrator named John
2. The username is admin

So from this we have half of the login info, the username is `admin`. Having this will actually allow us to attempt to brute-force the password using a tool like Hydra.

Before doing that we can also note in the source code the name values `user` and `pass` of the input tags in the form. These values will be needed for our Hydra command to brute-force the password.

![gobuster](./images/7_admin_request.webp)

Another way of confirming these form input name values is shown in this image here. Here I've opened the browser developer tools and navigated to the network tab. Next I attempted to log in with any username and password to test the request and response to the server. This first image shows the information from the resulting http POST request (as found in the request tab of this particular POST request).

The form data shows:

```terminal title=form_data.txt
user: 'admin'
pass: 'password'
```

So we can see again that the form input values are `user` and `pass`, and that the test I did took my input of `admin` and `password` respectively.

Hydra needs these values in order to know how to make legitimate requests to the server when brute-forcing the admin panel.


![gobuster](./images/8_admin_response.webp)

This image shows the response tab from the same test request I made. Here I've toggled the raw html code, and we can see the actual html response body that was received back to the client/browser.

Here there is another important piece of information we'll need for the Hydra command, 


![gobuster](./images/9_hydra.webp)


![gobuster](./images/10_hydra.webp)


![gobuster](./images/11_password_success_panel.webp)

:::spoiler Reveal spoiler
`admin:xavier`
:::


### What is John's RSA Private Key passphrase?

![gobuster](./images/12_rsa_private_key.webp)


![gobuster](./images/13_create_rsa_key.webp)

![gobuster](./images/14_bad_permissions.webp)

![gobuster](./images/15_chmod.webp)

![gobuster](./images/16_ssh_need_passphrase.webp)

![gobuster](./images/17_ssh2john.webp)

![gobuster](./images/18_hash_cracked.webp)



:::spoiler Reveal spoiler
`rockinroll`
:::


### user.txt

![gobuster](./images/19_ssh_access.webp)


![gobuster](./images/20_user_text_flag.webp)

:::spoiler Reveal spoiler
`THM{a_password_is_not_a_barrier}`
:::


### Web flag


:::spoiler Reveal spoiler
`THM{brut3_f0rce_is_e4sy}`
:::



## Privilege escalation

### What is the root's password?


![gobuster](./images/21_sudo-l.webp)

![gobuster](./images/22_sudo_cat_shadow.webp)

![gobuster](./images/23_root_cracked.webp)


:::spoiler Reveal spoiler
`football`
:::


### root.txt


![gobuster](./images/24_root_access.webp)

:::spoiler Reveal spoiler
`THM{pr1v1l3g3_3sc4l4t10n}`
:::




