---
layout: post
title: "Wazuh Project Overview"
date: 2025-05-07
categories: [cybersecurity]
tags: [analysis]
image: 
    path: /assets/img/Wazuh_SiemLogo.png

description: Overview of my Wazuh SIEM Project.
---

## Purpose:

The goal of this project is to expand my own understanding of the Wazuh SIEM product by building out my own fully functional setup. 
Not only does this provide a foundation of understanding when it comes to the general architecture of SIEM applications, 
but it also gives me a chance to troubleshoot issues before I ever have to do it in a production/work environment.

## Project Content:

- A self-hosted Wazuh Instance with a [Server, Indexer, and Dashboard](https://documentation.wazuh.com/current/getting-started/components/index.html) all contained in my Home Lab.
	- Includes a secured Virtual Machine for the service to run 24/7.
- A securely configured [Tailscale VPN](https://tailscale.com/) to provide Log connectivity to each node that is using the Wazuh service (no matter there geolocation).
	- Using the ACL functionality of Tailscale
	- using [Tailscale SSH](https://tailscale.com/kb/1193/tailscale-ssh) for quicker deployments and troubleshooting on Linux nodes.
- Active Logging and tracking of endpoint data using [Wazuh's endpoint agent](https://documentation.wazuh.com/current/user-manual/agent/index.html).
- Deployment of Wazuh endpoint agents in Linux, MacOS, and Windows setups.
- Translating the log data into a useable dashboard accessible via the [Wazuh Dashboard](https://documentation.wazuh.com/current/getting-started/components/wazuh-dashboard.html).
- Running Audit reports for key compliance specifications, specifically *PCI DSS, HIPAA, and GDPR*.

## Value Added:

- 40+ hours in Standing up, Onboarding, and troubleshooting a geographically distributed Wazuh Integrated Network.
- A practical-examination of my own understanding of IP addresses and troubleshooting connectivity between separate nodes.
- Insight in ACL construction and implementation.
- Insight in setting up SSH, as well as the downsides, upsides, and general limitations of the protocol.
- Lots of troubleshooting network connectivity between nodes, making sure various endpoints have connection but ONLY the connection they need. 
- A understanding of the key benefits, qualities, and functionality of the Wazuh platform and how it can be used.
- Deeper understanding of Log Data as well as the possibilities and utilities of it in a security environment.

## Pictures:

- **ADD PICTURES PLUS CAPTIONS FOR PROOF**
- I can log in via the Tailscale Static IP address that is assigned to the individual node that is the VM hosting the service. In that VM, I had open the adequate port (443 for HTTPS access).
I also had to configure adequate ACL on the TailScale Network so that my computer had network access to that device's HTTPs port.
![VPN-Internal_IP_Access_Wazuh](https://github.com/user-attachments/assets/d7f8f3af-3184-4a92-9930-009accf811e4)

	
- The following is a picture of the initial Dashboard I set up. 
![ExampleDashboard_Wazh](https://github.com/user-attachments/assets/1ae26ffc-4d73-46fc-bd38-01e1e55101f9)

- The hardware requirements aren't crazy. Mainly, from what I saw it would be best to make sure to have a VM that could have 16 GBs of Ram, 4 CPU Cores, and around 500 GBs of storage. 
Smaller or bigger setups would work... But for a Lab-setup there isn't any huge reason to go bigger. The full cost of the hardware for this was right around $300.
![DellOptiplexPic](https://github.com/user-attachments/assets/fb22c6e6-db94-479b-b4a3-c363d49ea79f)

## Few Thoughts

While I didn't hit all of the aspects that Wazuh has to offer (Multi-Cluster Indexing, Alerts/Notifications) I did as much as I could do without spending any money past the minimal hardware. What I would like to do in a future post is to expand into Cloud applications, potentially spin up a AWS network and make sure that the functionality there makes sense and works like I expect it to. That combined with a few key things (such as Alerts/Notifications) would be the last parts I haven't had my hands fully on.

If you find this project interesting/informative, feel free to reach out with any questions or thoughts! I am always happy to chat about Tech, Security, or life in general.
Cheers!

## Specific Technical Details:

- [Wazuh Project Part 1](https://legendaryluke007.github.io/posts/Wazuh-Part-1/) - The Initial spinning-up of the service 
- [Wazuh Project Part 2](https://legendaryluke007.github.io/posts/Wazuh-Part-2/) - Initial endpoint setup
- [Wazuh Project Part 3](https://legendaryluke007.github.io/posts/Wazuh-Part-3/) - ACLs, Tailscale VPN, and Onboarding Endpoints
