# PenetrationTesting-HarryPotter:Nagini
This repository is dedicated to the "**Penetration Testing & Ethical Hacking**" course project. The project consists of the penetration testing process of "**HarryPotter:Nagini**", a virtual machine vulnerable by design. Originally, this VM was developed for a CTF challenge, but it can be used in other ways, for example as the asset of a penetration testing process.

At the end of the pentration testing process, the following documents were produced:
- Penetration Testing Report;
- Penetration Testing Narrative;
- Digital Presentation.

You can consult these documents in the ```docs``` folder.

# 1. Introduction
The process of **Penetration Testing** allows to analyze and evaluate the security level of a computer system or network (in general of an asset) by emulating what a **Black Hat Hacker** might do.

This paper discusses all the steps related to the penetration testing activity performed on the HarryPotter:Nagini machine. In particularly, the entire activity is divided in the following steps:

 - **Target Scoping**;
 - **Information Gathering**;
 - **Target Discovery**;
 - **Enumerating Target & Port Scanning**;
 - **Vulnerability Mapping**;
 - **Post Exploitation**.

These steps are part of the **Framework Generale per il Penetration Testing** (**FGPT**). In our context, we can skip the "Target Scoping" phase because it's required a client that commission the penetration testing activity. In addition, it is also possible to skip the "information gathering" phase because the asset is a virtual machine, which means that the only information we can gather is that provided by the developer, but this has not released anything.

# 2. Tools and Instruments
This section will describe the tools used, more specifically the attacking machine, the target machine and the virtual environment.

## Kali Linux - Attacking Machine
The **Kali Linux** (64-bit) operating system, version 2023.1, is used as the attack machine. It is a Debian-based GNU/Linux distribution designed for digital forensics and cybersecurity, particularly for penetration testing.

## HarryPotter:Nagini - Target Machine
 The **HarryPotter:Nagini** machine is used as the target machine. This is a virtual machine about which we are not given any information. It can be downloaded at the following link: https://www.vulnhub.com/entry/harrypotter-nagini,689/,.

## Virtual Box - Virtual Environment
The **Oracle VM Virtual Box** software is used for the virtualization. A virtual network with NAT (called PTEH) was created to connect the two virtual machine. 

# 3. Target Discovery
The goal of this phase is to find the target machine in the network and collect the first useful information for the next steps.

## Finding the IP address
First, we try to find the target machine and get its IP address. The ***netdiscover*** tool can be used for this purpose, because we are in a local network and can define the range of IP addresses in which to search. Therefore, we run the command:

    netdiscover -r 10.0.2.0/24
- ```-r``` : range of IP addresses in which to search

<p align="center">
    <img src="./images/target_discovery/netdiscover.png" width="80%">
</p>

The first three addresses are used by Virtual Box to handle NAT network virtualization. Therefore, we can assume by exclusion that **10.0.2.4** is the address of the Nagini virtual machine.

## Reachability of the target machine
The ***ping*** command can be used to check the reachability of the target machine:

<p align="center">
    <img src="./images/target_discovery/ping.png" width="80%">
</p>

We can see that the target machine can be reached.

## OS Fingerprinting
Once we have discovered the IP address of the target machine, we can proceed with an **active OS Fingerprinting** procedure to obtain information regarding the target machine's operating system. The ***nmap*** tool can be used. Therefore, we run the command:

    nmap -O 10.0.2.4
- `-O` : enables OS detection.

<p align="center">
    <img src="./images/target_discovery/active_os_fingerprinting.png" width="80%">
</p>

We discover that a Linux-based operating system, whose version is between 4.15 and 5.6, is installed on the Nagini target machine.

# Enumerating Target & Port Scanning
Once we have discovered the IP address of the target machine and verified its reachability, we can proceed to individuate open ports (TCP and UDP) and services offered (with related version) by the target machine Nagini.

## TCP Port Scanning
The ***netdiscover*** tool can be used for this purpose. More precisely, we execute the command:

    nmap -sV -T5 -p- 10.0.2.4 -oX nmap_tcp_scan.xml
- `-sV` : allows to obtain as much information as possibile about the services provided by the ports;
- `-T5` : allows for maximum scanning speed;
- `-p-` : allows to scan all the 65535 ports;
- `-oX` : the output is a XML file.

Since the output of the previous command is a XML file, we prooced to convert it to HTML format:

    xsltproc nmap_tcp_scan.xml -o nmap_tcp_scan.html

The following is a table of open ports, identified by nmap, with the services offered and their version. Ports not shown in the table are closed.

<p align="center">
    <img src="./images/enumerating_target/tcp_scan.png" width="80%">
</p>

## UDP Port Scanning
Next, we scan the UDP ports. The ***unicornscan*** tool can be used for this purpose because is faster then ***nmap***. The ***unicornscan*** tool isn't installed in Kali, therefore you need to install it with the following command:

    sudo apt install unicornscan

Once ***unicornscan*** is installed, we can proceed to scan the UDP ports with the command:

    unicornscan -mU -Iv 10.0.2.4:1-65535 -r 5000
- `-mU` : indicates that the scanning mode is "UDP scanning";
- `-Iv` : enables printing of results;
- `-r` : indicates the rate of packets sent per second.

<p align="center">
    <img src="./images/enumerating_target/udp_scan.png" width="80%">
</p>

From the output of the previous command, you can see that there are no open UDP ports or that they are filtered.

# Vulnerability Mapping
Once we have discovered the operating system and the services that the target machine Nagini provides, we need to understand whether or not the exposed services or the operating system have vulnerabilities that can be exploited.

## Automated Vulnerability Analysis
First, we proceed with an automated vulnerability analysis. For this purpose, the two main tools used are **Nessus** and **OpenVas**. For this project, both were used so as to combine their results.

### Nessus
**Nessus** is a widely used vulnerability scanning tool in cybersecurity that allows scans to be performed on individual target machines or entire portions of a network. As part of this project, a "**Basic Network Scan**" was created and performed on the target machine Nagini. The results of that scan are shown below:

<p align="center">
    <img src="./images/vulnerability_mapping/nessus.png" width="80%">
</p>

We can notice that 30 vulnerabilities are discovered: 5 critical-level, one high-level and 24 info-level. The severity level, CVSS 3.0 score, and vulnerability name are shown for each vulnerability. These results are combined with those of OpenVas.

### OpenVAS
**OpenVas** is a vulnerability mapping framework that allows scanning of one or more machines to detect detailed information. As part of this project, a "**OpenVAS Default Scan**" was created and executed on the target machine Nagini:

<p align="center">
    <img src="./images/vulnerability_mapping/openvas_default_scan.png" width="80%">
</p>

The scan results are shown below:

<p align="center">
    <img src="./images/vulnerability_mapping/openvas_results.png" width="75%">
</p>

Nine vulnerabilities were detected, including 3 high-level, 4 medium-level, and 2 low-level vulnerabilities. For each vulnerability the name, mitigation type and severity level according to CVSS 2.0 is given. In reality the vulnerabilities found are greater, in fact for many of them it is written "Multiple Vulnerabilities."

Importantly, several vulnerabilities were found concerning **Joomla!**, which is a content management system for building and managing web pages. It is probably installed on the target machine Nagini.

In addition, we can see that different vulnerabilities were detected than those detected by Nessus. So it was important to use both tools so as to combine the results.

### Summary
The following graphics, which can be found in the "Finding Summary" section of the Penetration Testing Report, summarize the results obtained:

<p align="center">
    <img src="./images/vulnerability_mapping/finding_summary1.png" width="80%">
</p>

<p align="center">
    <img src="./images/vulnerability_mapping/finding_summary2.png" width="80%">
</p>


## Web Vulnerability Analysis

### Information Leakage - gobuster

### Information Leakage - JoomScan


# Target Exploitation


# Post-Exploitation