<br/>
<br/>

# AWS T-Pot

<img src="https://github.com/telekom-security/tpotce/blob/master/doc/tpotsocial.png">

---

## SUMMARY:
Starting off, I wanted to see if I could effectively and securely deploy a AWS based honeypot. I really wanted to be able to see what actual malicious traffic looked like, see some of the raw data
that indicates specific type of attacks, and just get my hands a little dirty. I was able to successfully install and deploy a T-Pot instance for about 4 days, browse the various tools in the T-Pot software package,
and collect some intriguing data. From this project, I learned how to...

- Set up a AWS instance
- Setting up Security policy on AWS
  - specific Key based security policies (.pem)
  - IP restrictions over specific ports
- Installing and running a honeypot software (T-Pot)
  - Using linux commands to install the T-Pot from it's github repository onto my AWS instance
  - Using the Dashboard feature in Kabana to look at macro data in relation to traffic
  - Digging through the Discover Feature to find specific data
    - attack_connection.payload.md5_hash.keyword
    - attack_connection.payload.sha512_hash.keyword
    - attack_connection.payload.data_hex.keyword
    - payload_printable.keyword
   
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
