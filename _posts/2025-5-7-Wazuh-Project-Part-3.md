---
layout: post
title: "Wazuh Project Part 3"
date: 2025-05-07
categories: [cybersecurity]
tags: [Project]
image: 
    path: /assets/img/wazuh_image.jpg

description: The third technical post on creating a fully functional Wazuh SIEM setup.
---

## Introduction 
Hello! If you have read through my other two posts I sincerely appreciate it! So far, we have...
- Spun up a self-hosted VM with a Wazuh Server application 
- Configured the various ports and connections for the VM to access the Dashboard outside of it.
- Created a Tailscale (VPN) network and added full access for the computers on it (something we will change later).
- configured Endpoints to start the log data flowing.
- started playing with the Wazuh Dashboard.

What this 3rd and potentially final post on this Wazuh serious will be focused on is...
- Securing the Tailscale network with proper ACLs 
- Adding endpoints via automatic Tailscale deployments
- Creating USEFUL dashboard configurations
- Triaging Malware flags
- Expanding to non-personal computers
- have at least 1 computer in every "common" OS (Windows, Mac, and Linux).

---
### Modifying ACLs in the Tailscale Network
- This is *VERY IMPORTANT*  for proper networks security.
By default, Tailscale will deny connection attempt *except* that the [default ACLs includes an auto-allow rule](https://tailscale.com/kb/1192/acl-samples#allow-all-default-acl). So make sure to scrub the current ACLs to start from a base of connection denials. To provide adequate Agent-communication between the Wazuh Server and the endpoints, we have to make sure that the server has proper ACLs for this communication. We need to provide log access but also a high-degree of security for every device that connects to the network. 

- In order to start working with ACLs, go to tailscale.com > login > admin > Access controls
- Start off with erasing everything that is there, then building up. This is to make sure that we start from a basis of full denial, and build up the core functionality we want.
- example of an **Access control rule for Wazuh-agent communications**. This rule adds the functionality of tcp communications for all individuals in the `autogroup:admin`. 
```json
{
	"acls": 
	[
		{
			"action": "accept",
			"src":    ["autogroup:admin"], // everyone who is admin can have access to these ports
			"proto":  "tcp",
			"dst":    ["*:1514", "*:1515", "*:443"]
		}, 
		// Add more acls HERE, 
	]
}
```

> I prefer to manage access by assigning tags. So in this instance, the `autogroup:admin` will be assigned to a tag used specifically for my wazuh-server, and so when I assign that tag to my server (as a admin myself) it gains the permissions of the `autogroup:admin`.
{: .prompt-tip }

- We also want to make sure that as a administrator on the network, we can gain access to computers via the Tailscale network.
- There is an `autogroup` called `admin` which gives full access to the dedicated computer to the other computers on the network. I like to create a tag which stream lines the process of adding or subtracting endpoints from specific roles.
- In Tailscale, you can then go and add a tag (on the admin dashboard) to a computer to grant them those given permissions and functions that are assigned to the group.
- **admin tag example for secure administration responsibilities**
```JSON
{
//"acls": 
//     [
//         		{
//                ACL Rule stuff
//        		}, 
// Add more acls HERE, 
//	],

"tagOwners": {
		"tag:<admin-laptop>":    ["autogroup:admin"], // admins can add the tag "admin-laptop", and everyone with that tag will be assigned the "autogroup:admin" permissions
	},
}

```


- **Tailscale SSH for user onboarding (Linux and MacOS)**
- This is a great resource when managing Linux and MacOS devices. it takes the "authorization" need for SSH connections to be the computer having access to the VPN. No need to setup SSH keys to share between devices manually. 
-  in the *Access Control* window on the admin board, we can just "flip on" *Tailscale SSH*.
- For security purposes, it is recommended to only have admins have access to the SSH functionality, so I like to make a specific rule for this.

```JSON
		{ // This allows the admin autogroup to SSH into Networked devices under the tag of "network_admin"  as a nonroot user.
		
			"action": "accept",
			"src":    ["autogroup:admin"],
			"dst":    ["tag:network_admin"],
			"users":  ["autogroup:nonroot"],
		},
```

> When setting up SSH permissions, it is generally a good idea to NOT allow root access via SSH. This can lead to some very deep security concerns if a admin computer is ever compromised.
{: .prompt-warning }

- Once the Tailscale SSH ACL is setup, you can go onto that computer and run the command `sudo tailscale up --ssh` to get that device accessible via SSH (This is the one part that has to be done on the computer...*Not a fully Zero-Touch SSH setup* but close!).
- After that, you can right-click the device in the admin board and use a Browser to SSH into the device! You will need the correct credentials for the endpoint however.
Past these rules, there are various ACLs that can be configured and modified, but I believe that these fundamental permissions is what matters the most and can be built upon with some levels of effort and clarity.

### User Onboarding protocol

> I spent a significant amount of time trying to take away any friction for onboarding. I had the hope of gaining a Zero-Touch-SSH functionality once people connected to my VPN... But the more I took time to look into the general functionality of SSH the more I realized that this was not really possible at my scale. This is possible with a 3rd-party remote-access product, but any useful Zero-Touch solution is not going to be free.
{: .prompt-note }

- Once I had a family or friend willing to connect to be a part of this project, I shared with them a VPN invite for my Tailscale and the account for them to sign into (because there is only 3 accounts allowed in the free-tier, I kept 1 account for every user and just restricted through ACLs what accounts had access to).
- I then created two different "Onboarding papers" for the individual depending on if they ran Linux, MacOS, or Windows.

### Onboarding process
- Quite simple
- Email client with Connection invite for Tailscale VPN plus link to sign in.
- Sent Along with the Onboarding documents for adding remote support (for me, this was using [RustDesk](https://rustdesk.com/))

### My Wazuh Dashboard Setup
- I am not going to go deep into how to configure Waul Visualizations... But once again there is some fantastic documentation that I found exceptionally insightful for this project!
- [Wazuh Dashboard/Visualization Documentation](https://documentation.wazuh.com/current/user-manual/wazuh-dashboard/creating-custom-dashboards.html)

- The follow picture is my Dashboard setup I created for basic vulnerability configuration and monitoring.
![ExampleDashboard_Wazh](https://github.com/user-attachments/assets/1ae26ffc-4d73-46fc-bd38-01e1e55101f9)
- Really helped me understand more about applying filters and actually using the data Wazuh collects in order to create visualizations.

## Final Thoughts
- After around 40+ hours of effort (between the 3 blog posts), I have now created a fully functional Wazuh setup. This setup includes...
	- A full MFA enabled VPN setup with hardened ACLs and geographically distributed nodes.
	- On-Prem hosted Wazuh Server instance with the server, indexer, and dashboard on a hardened Oracle VM.
	- Remotely accessible Wazuh Admin Board through HTTPS on the VPN.
	- 6 distinct endpoints with at least one of every unique Operating system (3 Windows, 1 Mac, 2 Linux). 
	- A useable custom Wazuh Dashboard for real-time response and analysis. 
- This has enabled me to learn a ton about...
	- General ACL hygiene in Networks.
	- Tailscale VPN modifications and usage, especially in a very distributed environment.
	- Configuring a Virtual Machine with proper CPU core count, Ram, and Storage for a specific application use-case (Wazuh).
	- Standing up the Wazuh Indexer, Server, and Dashboard.
	- Modifying VM firewall rules for maximum security benefits and functionality.
	- Integrating multiple services (VPN, Wazuh, RustDesk, and 3rd-party notification app) to create a functional and useable service.
  - Setting up SSH services and other Remote Access integrations.
	- Increased time in Linux environments, directory transversal, bash scripting, etc.
Over all this has been one of the coolest and most enjoyable projects I have had the ability to do! The breadth of knowledge I have gained from this has been amazing,
 and I look forward to building out similarly in depth projects in the future!
If you want to see the high-level summary of this whole project, That will be released shortly.

### Relevant Links
- [Wazuh Project Part 1](https://legendaryluke007.github.io/posts/Wazuh-Part-1/)
- [Wazuh Project Part 2](https://legendaryluke007.github.io/posts/Wazuh-Part-2/)


