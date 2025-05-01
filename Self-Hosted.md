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

# Security Group Rules

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

  Despite these being small requirements, it would be best to select an instance type that offers a decent amount of memory and processing power as a server that will host Grafana. That said, I selected t2.xlarge at the 64 bit architecture for the Instance type, because it offers a decent amount of memory that can help with Apache serving and Grafana hosting simultaneously, and is a relatively cheap option to use via AWS priced at 0.1856 USD per hour.

# Volume Size 
As mentioned previously, storage is a factor with hosting Grafana. It requires that 512 MB of free space exist on an environment in order to be hosted.
That said, the configured storage I had set for the instance is at 30 GB

### 4. Cost estimates
_____________________


### 5. Installation Process
______________________________
For the most important process, installing Grafana and Apache

