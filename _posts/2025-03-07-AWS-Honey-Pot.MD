---
layout: post
title: "AWS Honey-Pot Project"
date: 2025-03-07
categories: [cybersecurity]
tags: [analysis]
image: 
    path: /assets/img/tpotsocial.png

description: Overview of a AWS Honey Pot Project and lessons learned.
---

# AWS based Honey-Pot

## INTRODUCTION:

I really wanted to get my hands dirty with some vulnerability data, and quite honestly, digging through other people's research didn't seem engaging enough. So, I decided to find a way to get my own data about attempted exploits... And that is where the Honey Pot comes into play. 

A Honey-Pot is a system that looks like a vulnerable machine, but is really just there to attract adversaries to "leave their fingerprints" so to speak. The T-Pot which I used is a free to use honeypot software developed by T-Mobile to help promote cyber research. There are many different methods for deployment, and while this is built out on a *AWS EC2* instance, It could also be deployed on VMs, dockers, or many other setups. There will be a link at the bottom for anyone who is interested in trying the stuff out for themselves!

---

## SUMMARY:
Starting off, I wanted to see if I could effectively and securely deploy a AWS based honeypot. I really wanted to be able to see what actual malicious traffic looked like, see some of the raw data that indicates specific type of attacks, and just get my hands a little dirty with installing and using monitoring applications. I was able to successfully install and deploy a T-Pot instance for about 4 days, browse the various tools in the T-Pot software package,
and collect some intriguing data. This project was chalenging due to the shear volume of applications that I hadn't ever used, but I walked away with a lot of new information and insights.
From this project, I learned about...

- Setting up a AWS instance
- Setting up Security policy on AWS
  - specific Key based security policies (.pem)
    - Security principles such as rotating keys 
  - IP restrictions over specific ports
- Installing and running a honeypot software (T-Pot)
  - Using linux commands to install the T-Pot from it's github repository onto my AWS instance
  - Using the Dashboard feature in Kabana to look at macro data in relation to traffic
  - Digging through the Discover Feature to find specific data
    - attack_connection.payload.md5_hash.keyword
    - attack_connection.payload.sha512_hash.keyword
    - attack_connection.payload.data_hex.keyword
    - payload_printable.keyword
- Using CyberChef for Hexadecimal Decryption
  - Digging into different types of Encryption methodologies, md5, sha512, etc. etc.
  - Finding some payloads referencing IPs and attempting to download it locally on the AWS instance
- Learned about CVEs and their application in securing a device/network

---

#### CVEs ATTEMPTED:
- [CVE-2002-0013](https://www.cvedetails.com/cve/CVE-2002-0013)
	- SNMPv1 vulnerability allow denial of service or privilege escalation
- [CVE-2019-12263](https://www.cvedetails.com/cve/CVE-2019-12263)
	- Wind River VxWorks 6.9.4 and vx7 Buffer Overflow Vulnerability
- [CVE-2021-43798](https://www.cvedetails.com/cve/CVE-2021-43798)
	- Grafana Visualization software Vulnerability
- [CVE-2019-11500](https://www.cvedetails.com/cve/CVE-2019-11500)
	- Dovecoat Vulnerability, Memory Corruption, Execute code
- [CVE-2021-41773](https://www.cvedetails.com/cve/CVE-2021-41773)
	- Apache HTTP Server Path Traversal Vulnerability
- [CVE-2021-42013](https://www.cvedetails.com/cve/CVE-2021-42013)
	- Apache HTTP Server Path Traversal Vulnerability
- [CVE-2024-4577](https://www.cvedetails.com/cve/CVE-2024-4577) 
	- PHP-CGI OS Command Injection Vulnerability

---

#### T-POT GITHUB:

- [T-Pot](https://github.com/telekom-security/tpotce)

---

## KEY TAKEAWAYS:
- Catching Malware organically for analysis is harder then expected
  - If I return to a T-Pot project like this, I need to create automations that would check
  The md5 and sha512 hashes to see if they match any known malware entities.
- The shear volume of Open Source and free to use tools for SOC type analysis that is available is staggering.
- I need to figure out a more adequate way to export the CSV files I want for analysis so I don't burn cash having the AWS instance running all the time
- While I didn't fully achieve my goal of getting defanged Malware from the Honey Pot for further analysis, I took away a lot of information on general security practices and how to deploy and monitor an EC2 instance in the cloud.

 