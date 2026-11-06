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

The Brute It CTF found on TryHackMe [here](https://tryhackme.com/room/bruteit), I found to be a great beginner CTF challenge room. It is great because it covers a classic pentesting pattern: reconnaissance, initial access/getting a shell, and then privilege escalation/getting root access on the target machine. These are core steps in any cybersecurity penetration testing framework, and it was fun to go through the stages in a straightforward way.

In this writeup

## Reconnaissance



![ping scan](./images/1_ping.webp)

### How many ports are open?

![nmap scan](./images/2_nmap.webp)




### What version of SSH is running?


:::spoiler Reveal spoiler
`OpenSSH 7.6p1`
:::

### What version of Apache is running?


:::spoiler Reveal spoiler
`2.4.29`
:::

### Which Linux distribution is running?



:::spoiler Reveal spoiler
`Ubuntu`
:::


### What is the hidden directory?

![gobuster](./images/3_gobuster.webp)

![gobuster](./images/4_gobuster.webp)

:::spoiler Reveal spoiler
`/admin`
:::

## Getting a shell

### What is the user:password of the admin panel?


![admin page](./images/5_admin_page.webp)


![gobuster](./images/6_admin_source.webp)


![gobuster](./images/7_admin_request.webp)

![gobuster](./images/8_admin_response.webp)


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




