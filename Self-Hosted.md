### 1. Project Description
_________________________

# What Software will be setup?
The software that I decided that could be interesting and fun to setup for this assignment is the Grafana application

# What the Software Is?
Grafana - a multi-purpose and multi-platform analysis software that is designed for data visualization and data monitoring that can assist with the following per the Grafana website at https://grafana.com/

- Easily managing incident repsonses
- Reducing costs
- User Management Dashboards
- Performance Tests
- Managing Alert systems
  and a plethora of useful tools that can server of great benefit for business and IT management desks alike

  # What this Document Covers?
  This document will cover all of the steps that were taken when installing and setting up Grafana on an AWS VPC / EC2 experiencing using an Ubuntu server. It will cover
  - Features of VPC and EC2
  - Specifications of Instance Running
  - Costs
  - Security and Privacy Countermeasures
 
 ### 2 .VPC Setup
_________________________
 
 # VPC Block
 The VPC Block for Grafana was created with a private IPv4 CIDR address of 10.0.0.0/16 that allows for a range of 65,536 IP addresses to be created
 A 16 CIDR block can be good for a multitude of reasons per https://www.novelvista.com/blogs/cloud-and-aws/cidr-basic
 Such as
 - Scalability with available addresses
 - Plenty of IPs to allocate 
 - Leaves little IP Addresses wasted
 - Easy maintaining of networking environment

# Subnet Block
The Subnet block for Grafana was created using the same IP addresses however, these subnets use a /24 CIDR block. I created three subnets
-Public 10.0.10.0/24 (typically used for mainly instances that regard using 
-Private 10.0.20.0/24 (typically used for more internal instances such as 
-Server 10.0.30.0/24 (used for hosting instances, such as for hosting Grafana)

# Route Table Rules  
When creating the Route Tables for the Grafana instance to function, I added route tables to all of the subnets that were created
for routing the tables. Routing tables are imperative because they determine where to tell the network where to send potential data packets to.
In the case of Routing Tables I had selected only two IP addresses
- 10.0.0.0 (this standard IP address would allow for internal communications with other devices throughout the network)
- 0.0.0.0 (this is an anywhere IP address, it would be exclusively used for outbound traffic going towards the internet gateway)

# Network ACL Rules
Arguably, one of the most important aspects of setting up a server, is adding the network access control lists. These are important for dictating what goes in and what goes out of the network. When implementing the Network ACL's I wanted to make sure that only approved IP addresses can get in or out. With that said, I added.

- SSH rules for my home computers, IP 70.92.103.7/24 since I will want to be the only person that is able to shell into the server
- HTTPS Rules that allow for access from 10.0.0.0/24 which are computers in the subnets
- HTTP Rules that allow access from 10.0.0.0/24 which are computers in the subnets
- HTTPS rules that allow access from my personal computer at 70.92.103.0/24
- HTTP rules that allow access from my personal computer at 70.92.103.0/24

The HTTPS and HTTP will only allow for those specific IP addresses to reach the website, and serve as importance since I wish for Apache to server the Grafana service from the Ubuntu server

For my outbound rules, I had created 3 rules,  

-HTTP Rule that allows for traffic from any IP address to reach out bound 
-HTTPS rule that allows for traffic from any IP to reach outbound
-and SSH from my IP address 70.92.103.7/24 that is able to reach outbound connections.

These rules were created with security for the VPC in mind, as they will restrict all other IP addresses from everything except any of the subnets or my personal public IP address

# Security Group Rules
For the security group, the formula is still the same. I included 


### 3. AWS Instance Setup
________________________

# Instance Type 
The instance I chose to host the Grafana service is the Ubuntu server. I chose this because Ubuntu is a service I feel confident using and an environment that I have experience in not just from this class, but throughout my entire school career. Grafana is also absolutely compatible with Ubuntu and installing Grafana on a Linux flavored environment is an easy and straightforward experience to achieve.

# AMI (Amazon Machine Image)
The Amazon Machine Image I determined was the best fit for running Grefana was Ubuntu Server 24.04 LTS
I chose this Machine Image for a multitude of reasons 
According to a ZDNET article I found and the official Ubuntu website, reasons to generally choose this interface for

-LTS is a long term service, set to be supported for atleast 12 years, meaning potentially, server hardware may need to be upgraded before the machine image ever does
- Built in machine image firmware updater including dedicated support for peripheral hardware updates
- Empowers open source developers
- Emphasis on security being a recent machine image constant updates
https://www.zdnet.com/article/ubuntu-24-04-same-as-it-ever-was-but-with-5-big-improvements/
https://ubuntu.com/blog/ubuntu-desktop-24-04-noble-numbat-deep-dive

The plan is to also use Apache HTTPS to host access to Grafana
Benefits of Apache according to https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations
- Flexibilty
- Power
- Near Universal Support
- Also handles TLS encryption

When also reviewing the system requirements for Grafana at https://grafana.com/docs/grafana/latest/setup-grafana/installation/
The minimum system requirements for installing Grafana on a server 
- Minimum Recommended Memory: 512 MB
- Minimum recommended CPU: 1 Core

  Despite these being small requirements, it would be best to select an instance type that offers a decent amount of memory and processing power as a server that will host Grafana. That said, I selected t2.xlarge at the 64 bit architecture for the Instance type, because it offers a decent amount of memory that can help with Grafana hosting, and is a relatively cheap option to use via AWS priced at 0.1856 USD per hour.

# Volume Size 
As mentioned previously, storage is a factor with hosting Grafana. It requires that 512 MB of free space exist on an environment in order to be hosted.
That said, the configured storage I had set for the instance is at 30 GB with a general purpose SSD.

Storage Management will also be handled through the S3 Bucket cloud environment, however, this will be detailed in depth at a later point in the documentation


### 4. Cost estimates
_____________________
Cost estimates of Grafana will rely on how much Grafana utilizes and costs, and how much the AWS service utilizes and uses. They will typically be based upon factors that we have in this case such as
- Elastic IPs
- Instance Pricing
- Amazon Machine Imaging
- Amazon Web Service charges
As of April 25, the costs of using an EC2 is charged at
- $11.14
- $0.23 for the computing aspect

For a VPC, the costs of using a VPC is charged at 
- $10.79

# Projected Dashboard Costs
According to the Amazon Web Service dashboard, a projected cost for running the service on an Ubuntu layered server 
![image](https://github.com/user-attachments/assets/b97b58c1-3187-44b5-a8ff-9b9154817424)

Total FORECASTED for this assignment is roughly around $25.17 for the current month costs of using the VPC and EC2 service to host the Grafana application.

# Cost of Instance Type
As mentioned previously, the instance selected in this case is a t2.large of Ubuntu Server 24.04 LTS with SSD Volume Type. According to AWS, this instance is priced in at about 

-0.0116 USD Per Hour

# Cost of Elastic IP
Elastic IP's are important in the AWS environment, as they are essentially static IP addresses that are specifically designed to be associated with an EC2 instance in order for connectivity. The Elastic IP's come with a cost however, they do not go into effect until they are in use. They are priced at according to https://aws.amazon.com/ec2/pricing/on-demand/ for a t2.large at

- $0.0928 on demand hourly rate

# Cost of Amazon Machine Image
Again, as mentioned, this is an Ubuntu Server 24.04 LTS AMI, this is priced

- Free tier!

  There is no additonal charge for this

  

### 5. Installation Process
______________________________
For the most important process, installing Grafana and Apache.
Starting with the Grafana process, the process of installing Grafana is rather straight forward, once all of the required security countermeasures for the VPC and the Security Groups for the instance are properly setup and ready to go.
For the installation process of Grafana, I used information from the website https://www.cherryservers.com/blog/install-grafana-ubuntu-2404 to help with the install, the process is as follows.

- Step 1 Update system packages: This command will update all of the system packages and upgrade any existing important packages
  sudo apt-get update && sudo apt-get upgrade -y

- Step 2 Install required dependencies: This command will obtain any of the requirements that are needed for Grafana to function
  sudo apt-get install -y apt-transport-https software-properties-common wget
  Here's what each package does:
    apt-transport-https: It lets you use HTTPS repositories. This ensures secure communication.
    software-properties-common: It provides scripts to add and remove software repositories.
    wget: A command-line utility used to download files from the internet.

- Step 3 Import the Grafana GPG key:
Needed to verify the authenticity of the Grafana packages that are required for install

  sudo mkdir -p /etc/apt/keyrings/  
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

It will need to create a directory in order to store keyrings that are associated with storing secure keys
this command will reach out to the website that provides the said key and will install them without signature or key errors

- Step 4 Add the Grafana APT Repository - allows for Grafana to be installed via apt package manager:

  echo  "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

It essentially tells the server to reach out to the Grafana website to obtain the repository

- Step 5 Update the package lists again:

sudo apt-get update

Will obtain the most recent packages that would be compatible and work with the requirements for Grafana to function properly

- Step 6 Install Grafana: Now that all packages are updated, the server has obtained the key and the repository for Grafana, now Grafana can be installed onto a server for launch and hosting

sudo apt-get install grafana -y

sudo apt-get install grafana-enterprise

Note: the first command would typically be the one that would be used in this case since I am just installing it, it is the basic, free and open source version of Grafana where as the latter enterprise is paid and features more functionality in comparison to the free version. Enterprise features license capabilities, premium uses of plugins and features

- Step 7: Start and Enable the Grafana service: Now that Grafana is installed without errors, it benefits and is convenient to have it open at start up every single time. This is done using the

  - sudo systemctl enable grafana-server.service
 
  To simply start the service, simply put in the command

  - sudo systemctl start grafana-server

- Step 8: Adjust Firewall Settings: Though, more security focused, on my end, I enabled firewall capabilities on my server, therefore it will benefit to push commands that enable the flow of traffic to port 3000 of TCP which is where Grafana is hosted on.

- sudo ufw status

- sudo ufw allow 3000/tcp

- sudo ufw reload

status determines the state of the firewall rules on the server and reload, will refresh the state of the firewall after the firewalls have been implemented

Step 9: Access it!!! For the final step, confirm it is running, this can be done so by heading to the link of the elastic IP address, in this case http://98.85.211.221:3000 and I was successfully able to get in! The default username and passwords were both admin, once logging in, I had setup my own username and password for security.
![image](https://github.com/user-attachments/assets/d995b106-f94c-4cbb-a8a7-b8893150519a)

# Data Source Install 

After setting up Grafana, in order to be able to see information be presented in the Grafana interface, I need to set up a datasource, for my data source, I chose Prometheus, I installed this on my current server that I am hosting. The steps to install are as follows

### 6. Security
______________________________
The security aspect of the web hosting is extremely important for data protection, privacy and disaster potentiality. There are many ways to configure security methods on Grafana


### 7. Software Features
______________________________
As mentioned previously. Grafana - it is a multi-purpose and multi-platform analysis software that is designed for data visualization and data monitoring that can assist with the following per the Grafana website at https://grafana.com/

- Easily managing incident repsonses
- Reducing costs
- User Management Dashboards
- Performance Tests
- Managing Alert systems
  and a plethora of useful tools that can server of great benefit for business and IT management desks alike
