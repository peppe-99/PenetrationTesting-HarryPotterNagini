# PenetrationTesting-HarryPotter:Nagini
This repository is dedicated to the "**Penetration Testing & Ethical Hacking**" course project. The project consists of the penetration testing process of "**HarryPotter:Nagini**", a virtual machine vulnerable by design. Originally, this VM was developed for a CTF challenge, but it can be used in other ways, for example as the asset of a penetration testing process.

# Introduction
The process of **Penetration Testing** allows to analyze and evaluate the security level of a computer system or network (in general of an asset) by emulating what a **Black Hat Hacker** might do.

This paper discusses all the steps related to the penetration testing activity performed on the HarryPotter:Nagini machine. In particularly, the entire activity is divided in the following steps:

 - **Target Scoping**;
 - **Information Gathering**;
 - **Target Discovery**;
 - **Enumerating Target & Port Scanning**;
 - **Vulnerability Mapping**;
 - **Target Exploitation**;
 - **Post Exploitation**.

These steps are part of the **Framework Generale per il Penetration Testing** (**FGPT**). In our context, we can skip the "Target Scoping" phase because it's required a client that commission the penetration testing activity. In addition, it is also possible to skip the "information gathering" phase because the asset is a virtual machine, which means that the only information we can gather is that provided by the developer, but this has not released anything.

# Tools and Instruments
This section will describe the tools used, more specifically the attacking machine, the target machine and the virtual environment.

## Kali Linux - Attacking Machine
The **Kali Linux** (64-bit) operating system, version 2023.1, is used as the attack machine. It is a Debian-based GNU/Linux distribution designed for digital forensics and cybersecurity, particularly for penetration testing.

## HarryPotter:Nagini - Target Machine
 The **HarryPotter:Nagini** machine, downloaded at the following link: https://www.vulnhub.com/entry/harrypotter-nagini,689/, is used as the target machine. This is a virtual machine about which we are not given any information.

## Virtual Box - Virtual Environment
The **Oracle VM Virtual Box** software is used for the virtualization. A virtual network with NAT (called PTEH) was created to connect the two virtual machine. 

# Target Discovery
The goal of this phase is to find the target machine in the network and collect the first useful information for the next steps.

## Finding the IP address
First, we try to find the target machine and get its IP address. The ***netdiscover*** tool can be used for this purpose, because we are in a local network and can define the range of IP addresses in which to search. Therefore, we run the command:

    netdiscover -r 10.0.2.0/24
- `-r`: range of IP addresses in which to search

< inserire immagine risultato netdiscover >
The first three addresses are used by Virtual Box to handle NAT network virtualization. Therefore, we can assume by exclusion that **10.0.2.4** is the address of the Nagini virtual machine.

## Reachability of the target machine
The ***ping*** command can be used to check the reachability of the target machine:
< inserire immagine risultato ping >
We can see that the target machine can be reached.

## OS Fingerprinting
Once we have discovered the IP address of the target machine, we can proceed with an **active OS Fingerprinting** procedure to obtain information regarding the target machine's operating system. The ***nmap*** tool can be used. Therefore, we run the command:

    nmap -O 10.0.2.4
- `-O`: enables OS detection.

< inserire immagine risultato nmap -O >
We discover that a Linux-based operating system, whose version is between 4.15 and 5.6, is installed on the Nagini target machine.
