<br/>
<br/>

# AWS T-POT

<img src="https://github.com/telekom-security/tpotce/blob/master/doc/tpotsocial.png">

---

## SUMMARY:
Starting off, I wanted to see if I could effectively and securely deploy a AWS based honeypot. I really wanted to be able to see what actual malicious traffic looked like, see some of the raw data
that indicates specific type of attacks, and just get my hands a little dirty with installing and using monitoring applications. 
I was able to successfully install and deploy a T-Pot instance for about 4 days, browse the various tools in the T-Pot software package,
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

## TAKEAWAYS:
- Catching Malware organically for analysis is harder then expected
  - If I return to a T-Pot project like this, I need to create automations that would check
  The md5 and sha512 hashes to see if they match any known malware entities.
- The shear volume of Open Source and free to use tools for SOC type analysis that is available is staggering.
- I need to figure out a more adequate way to export the CSV files I want for analysis so I don't burn cash having the AWS instance running all the time
- I really enjoy the investigative style of this workflow, trying to analyze the data and sift through to find usable information is super interesting and enthralling.
- While I didn't fully achieve my goal of getting defanged Malware from the Honey Pot for further analysis, I took away a lot of information on general security practices and how to
deploy and monitor instance in the cloud.

--- 

## NEXT PROJECT(S):
- Creating a home SOC for my own network and home systems would be my next step.
  - I have the hardware and basic understanding of how to set it up
  - It would be interesting to setup the Automations and workflows for checking network traffic
  - I also want to see about blocking certain devices (such as my Smart TV) from exporting my data for advertisers
- Setting up a Isolated lab to deconstruct some malware both statically and dynamically.
  - The static analysis will be first. That is much easier to set up securely and safely then the dynamic lab.
  - My vision is a laptop that is fully disconnected from the network that I can run Malware on local VMs and watch the VM traffic with WireShark and other tools
  - Also make a personal write-up of the malware I analyze to figure out the process of documenting that.
 
---

# [Return Home](index.md)
 
