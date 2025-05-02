### 🎯 Show and Tell Final

#### 🌟 **Favorite Thing**

Add nested item under what their favorite thing is.  If it is not on this list add a new bullet.  
Nested bullets should be in form: `* Last name`

* Gaming
  * Largent  
* Cooking
* PC Building
  * Kelly
* Retired and Decided to go back to school
  * Porter
  

---

#### 🖥️ **Host OS**

Add nested item under which OS the software is hosted on.  If it is not on this list add a new bullet.  
Nested bullets should be in form: `* Last name - OS version`

* Windows < 2019
* Debian
* Ubuntu
  * Kelly - Ubuntu Server 24.04 LTS
  * Largent - Ubuntu Server 24.04 LTS
  * Porter - Ubuntu Server 24.04 LTS Syncthing, it runs on any Linux distribution, wanted to try something new, figured Ubuntu would be easy to configure. 
* RedHat

---

#### 💾 **Software**

Add nested item under which software they implemented.  If it is not on this list add a new bullet.  
Nested bullets should be in form: `* Last name - software version`

* Unraid
* TrueNAS
* Proxmox
* Grafana
* Homepage
* Immich
* Mealie
  * Kelly - Mealie v1.6.0 Intuitive recipe management app that tracks urls and decompresses and collectivize collected recipes
* GitLab
* ownCloud
* Minecraft
  * Largent - Minecraft Java Edition 1.21.5, able to run Minecraft and hav
* Syncthing
  * Porter - anything done with one system, it gets mirrored to another tailed system via P2P networking. It is essentially a file transfer software and has connected to two remote devices. So Porter can configure device to download an image via terminal once image is downloaded it can transfer these files to selected remote devices. 


---

#### 🧪 **Demo**

Add nested item how you are interacting with their self-hosted implementation.  If it is not on this list add a new bullet.  
Nested bullets should be in form: `* Last name - describe what was done to demo`

* Made Account
* Joined server
 *   Largent - Is able to sucessfully join a Minecraft server and have other players join, is connected to Wright State network and seemingly runs flawless. Largent has a ping of 65 ms average when on his server. Largent allows for 2GB's of RAM for usage in server and can host Minecraft. 
* Viewed Dashboard
* Adding Recipe
    * Kelly - Created and imported in a recipe from basically any cooking site that he could find and would reduce clutter giving a simpler way to view instructions and ingredients
* Migrated file
  * Porter - was able to migrate a file from his laptop to a file that is at home via P2P networking. It works establishes a Peer to Peer connection across local network. Every single machine uses a code that talks to other machines 

#### 💥 **Live Troubleshooting**

Add nested item under whether you gave or received help, a new idea, or a suggestion for a fix. 
Nested bullets should be in form: `* Last name - description`

* I received help
  * Kelly - Had issues with appdata that was deleting line and not storing recipes in the appdata file on the server. Because of this, it was apparently not able to recognize recipes through the url detector in Mealie Kelly was able to fix this via adding a line back from watching a YouTube video which is linked in report
  * Largent - Had issues systemd, Largent went to the Minecraft wiki for an example systemd. Largent set working directory to where the Minecraft server home directory. Because of this, it allowed for the Minecraft service manager to recognize the directory and run Minecraft and its dependencies from what it seems.
  * Porter - had issues with synchronization. Could not connect from home network to Amazons network because of the web interface. Had to default IP address in the search bar to only allow computer to access own homebase interface, if done out side, tell software to allow connections to the interface. 
* I gave help
* I need to fix
* They need to fix

---

#### 🔐 **Security**

Add nested item under each category to evaluate their firewall setup - this is a mix of Network ACLs, Security Groups, and / or system firewalls, per their setup.  
Nested bullets should be in form: `* Last name - describe your evaluation of POOR or GOOD`

* Firewalls rules reflect GOOD requirements around protected ports
  * Kelly - Has solid inbound and outbound requirements for SSH protocol through home network and Wright State network that will allow Kelly to SSH through Powershell, also has Custom ports confiugured for 9925 which hosts the Mealie application, and has both the school network and home network access via the custom TCP port. Good setup!
  * Largent - Has 3 inbound rules associated with the instance, one for TCP on the 22 port for SSH via Wright State network, Largent, also has a UDP port active for all ranges, this port is open for the server queries for Minecraft, port is open to all ports. Also a good setup!
  * Porter - Has SSH port 22 capabilities on for an inbound rule, also allowed, TCP and UDP from home network on home network for access, chose UDP because it is required for the protocol. 22000is sending data when sending file to another system. Also has access on 21027 port on UDP and 8384, all four also applies to school network for access at Wright State. Also has all outbound connections going outward to send data out. Cool stuff!!

* Firewalls rules reflect GOOD requirements around open access ports
  * Kelly - Inbound for HTTP at port 80 , Outbound as all so outbount ports can reach all possible devices
  * Largent - All traffic is allowed outbound for the security group, TCP and UDP on the port 25565 also have open access ports for incoming traffic from the Minecraft server
* Firewalls rules reflect POOR requirements around protected ports
* Firewalls rules reflect POOR requirements around open access ports

#### ⚠️**Vulnerability Vectors**

Add nested item under each category to evaluate their hosted software for potential vulnerability vectors. If it is not on this list add a new bullet.  
Nested bullets should be in form: `* Last name - describe how software has this vulnerability`

* Left default username / password
* Exposed admin interfaces
* Insecure configurations
  * Kelly - Has no HTTP encryption, Kelly tried but could not successfully do so because it failed to establish a secure connection with the certifications and keys created. This may be because the location of the key may not have been created properly or have ownership or modification commands setup properly (chmod, chown 660). Admin username is also something simple such as a word then a number
  * Largent - Most of his ports are open access from any IP Address, especially inbound which can be seen as potentially problematic. Susceptible to potential DDoS attacks on a Minecraft server!!!! uh oh! So far the only noted vulnerabilities that Largent noted
  * Porter - Peer to Peer relay servers to move data between servers which can be seen as a vulnerability.
* Users have destructive permissions to software assets
* Outdated versions of software or dependencies

* 



1. Alec Kelly - Mealie


  Q: How is the site connected?
  A: Access via HTTP, no HTTPS, attempted to get HTTPS, but could not get HTTPS figured out.

  Q: What port is 9925? For the Mealie
  A: runs on a custom port via Docker seemingly

  Q: Why might CIDR /23 be able to go further for subnet CIDRs
  A: Because /23 has such a wide range of network IP's, for such a small network, Alec figured that maybe, he could go further with a /24 CIDR block for subnets, he based his CIDR, off of lecture notes, and then seeing the projected costs, he overestimated the data of how much costs can be and figured he could shrink with IP's

  Q: What were your projected forecasted costs for the AWS server?
  A: Alec's costs appear to be $10.72 for the forecasted month end costs, these appear to be for instance at $0.0464  USD per hour
  EC2 appeared to be $5.66 at an average monthly cost
  VPC was about $3 per month

  
