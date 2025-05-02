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
-Private 10.0.20.0/24 (typically used for more internal instances)

# Route Table Rules  
When creating the Route Tables for the Grafana instance to function, I added route tables to all of the subnets that were created
for routing the tables. Routing tables are imperative because they determine where to tell the network where to send potential data packets to.
In the case of Routing Tables I had selected only two IP addresses
- 10.0.0.0 (this standard IP address would allow for internal communications with other devices throughout the network)
- 0.0.0.0 (this is an anywhere IP address, it would be exclusively used for outbound traffic going towards the internet gateway)

# Network ACL Rules
Arguably, one of the most important aspects of setting up a server, is adding the network access control lists. These are important for dictating what goes in and what goes out of the network. When implementing the Network ACL's I wanted to make sure that only approved IP addresses can get in or out. With that said, I added.

- SSH rules for my home computers, IP 70.92.103.7/24 since I will want to be the only person that is able to shell into the server and manage 
- HTTPS Rules that allow for access from 10.0.0.0/24 which are computers in the subnets and should be able to communicate with HTTP
- HTTP Rules that allow access from 10.0.0.0/24 which are computers in the subnets and should be able to communicate with HTTPS (hypothetically, HTTPS not present here)
- HTTPS rules that allow access from my personal computer at 70.92.103.0/24
- HTTP rules that allow access from my personal computer at 70.92.103.0/24
- TCP Port 3000 - Restricted to only me for access purposes and sensitve information that only admins should have the authority and power to view 70.92.103.0/24
  

The HTTPS and HTTP will only allow for those specific IP addresses to reach the website, and serve as importance since I wish for Apache to server the Grafana service from the Ubuntu server

For my outbound rules, I had created 3 rules,  

-HTTP Rule that allows for traffic from any IP address to reach out bound 
-HTTPS rule that allows for traffic from any IP to reach outbound
-and SSH from my IP address 70.92.103.7/24 that is able to reach outbound connections.

These rules were created with security for the VPC in mind, as they will restrict all other IP addresses from everything except any of the subnets or my personal public IP address


# Security Group Rules
For the security group, the formula is still the same. I included the following

Inbound
- HTTP Port 80 - 10.0.0.0/24 which will allow access inbound for all of the devices that are on either the public or private subnets
- HTTP Port 443 - 10.0.0.0/24 which will allow access inbound for all of the devices that are on either the public or private subnets
- HTTP Port 80 - 70.92.103.0/24 which will allow access for me inbound if needed to access via HTTP 
- HTTP Port 443 - 70.92.103.0/24 which will allow access for me inbound if needed to access via HTTPS (HTTPS is not here but in a hypothetical scenario)
- SSH Port 22 - 70.92.103.0/24 I will be the only person who will be able to SSH into the instance if needed, as admin in this case.
- TCP Port 3000 - 70.92.103.0/24 As an admin, I should be the only person throughout the network able to access the Grafana environment for making changes, creating queries for data viewing, and managing databases that are managed through Prometheus
- TCP Port 9090 - 70.92.103.0/24, As an admin, it should be only me who is able to access and manage the Prometheus database, as unauthorized access can lead to disasters in accidents in mismanaged data, exposure to hostnames and IP's and other sensitive business information
- TCP Port 9100 - 70.92.103.0/24 - Whilst this port is for the example of using node-exporter, it still is valid practice to restrict access to only me so that only I can scrape metrics, and see what ports may or may not be open.
  (information suggested about such by ChatGPT)

  Outbound
  All traffic - 0.0.0.0 All traffic leaving the instance should be able to make its way to the internet, hence the allowing any IP address for outbound being the most realistic to me.

  ![image](https://github.com/user-attachments/assets/88ad721e-f2b6-4c13-8f84-008d45963568)

  ![image](https://github.com/user-attachments/assets/809c3e7e-8362-41dd-a5cb-ce12bb93daed)


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

# Cost of S3 Standards - Potential 3-2-1 Backup Plan component that is potentially suggested.
According to https://aws.amazon.com/s3/pricing/ the pricing for the S3 is as follows
  First 50 TB / Month	$0.023 per GB
Next 450 TB / Month	$0.022 per GB
Over 500 TB / Month	$0.021 per GB

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

After setting up Grafana, in order to be able to see information be presented in the Grafana interface, I need to set up a datasource, for my data source, I chose Prometheus, I installed this on my current server that I am hosting. The steps to install are as follows. The source I used to install the Promethesus data source was from like the previous section dealing with installing Grafana from CherryServers at https://www.cherryservers.com/blog/install-prometheus-ubuntu
Lets install Prometheus

- Step 1 Update packages

  sudo apt update

  Like with most instance of installing new software on Debian based machines, it is important to first and foremost issue a sudo apt update command to ensure that the most recent packages are available and up to date.

- Step 2 Create a System User for Prometheus

  sudo groupadd --system prometheus
  sudo useradd -s /sbin/nologin --system -g prometheus prometheus\

These two commands are responsible for creating both system groups, such as in the first command and users such as in the second command. It will essentially have Prometheus run as user and a group simultaneously

- Step 3 Create directories for Prometheus

sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus

These commands are responsible for creating libraries and directories for storing configuration files for the Prometheus software. They also serve as data storage for the software too.

- Step 4 Download Prometheus

  wget https://github.com/prometheus/prometheus/releases/download/v2.43.0/prometheus-2.43.0.linux-amd64.tar.gz

  This command is straightforward. It reaches out to the GitHub repo that stores the files for Prometheus and installs and extracts them to the server. To extract the files from the tar file, the following command that the instructions said to use were 

tar vxf prometheus*.tar.gz

- Step 5 Navigate and Configure the Prometheus Directory

  cd prometheus*/
  sudo mv prometheus /usr/local/bin
sudo mv promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool

First, I needed to move to the Prometheus directory from the file I just extracted. Using the change directory command, I was able to move over there and set ownership to the users and groups for Prometheus that were created in the previous steps. The groups and users needed ownership in order for Prometheus to read and write data to.

- Step 6 Move Configuration Files and Set Ownership

sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus

sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus

These files needed to be changed to have proper ownership and moved to proper locations. So in that case Prometheus is able to have access to these said files.

- Step 7 Create Prometheus Systemd Service

  sudo nano /etc/systemd/system/prometheus.service

  This command issues a creation in Nano for creating a system service file for Prometheus. The following is what is required to paste into the service file.
  [Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target

According to ChatGPT, the configuration allows for Prometheus to run securely and reliably
For example the entire concept of having prometheus as its own user is on the basis of running as a non login system user. This can limit file access and reduce risk which is good for security.

- Step 8 Reload, restart and check Prometheus status

  sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus

These are simple basic post install commands that will require for the software to be reloaded and restarted after a fresh install. This asserts that the software has been completed succesfully during the installation process. I issued a status command to ensure that it is infact running properly and seems to do so.

- Step 9 Allow firewalls for Prometheus

  sudo ufw allow 9090/tcp

  Simple firewall security commands that essentially permit traffic that is flowing towards prometheus 9090 port for basic entry. These firewalls are ultimately important because they direct the flow of traffic that is suitable to enter the network, whilst at the same time, blocking traffic that is unwanted.

  ![image](https://github.com/user-attachments/assets/56db8188-39da-4db0-b26e-785a4a4958ba)

  # Showing data

  Inserting data onto the Dashboard via Prometheus is a useful tool for monitoring. There are many different ways data can be insertted into Grafana!

  - Importing panels
  - Importing a dashboard via grafana.com
  - Creating visualizations
 
  For example, I added Node Exporter to my Grafana, which according to ChatGPT is a simple tool used for hardware measurements and analysis. I used the website https://developer.couchbase.com/tutorial-node-exporter-setup/
  Here is how I installed it.
- Step 1 Download Node Exporter
  wget \
  https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz

  - Step 2 Create User
 sudo groupadd -f node_exporter
sudo useradd -g node_exporter --no-create-home --shell /bin/false node_exporter
sudo mkdir /etc/node_exporter
sudo chown node_exporter:node_exporter /etc/node_exporter

These users are created for prometheus to take control of and modify in order to view queries 

- Step 3 Unpack Node Exporter Binary
  tar -xvf node_exporter-1.0.1.linux-amd64.tar.gz
mv node_exporter-1.0.1.linux-amd64 node_exporter-files

These commands will unzip the files that are obtained in order for node exporter to function and will move them to their proper directory

- Step 4 Install Node Exporter
  sudo cp node_exporter-files/node_exporter /usr/bin/
sudo chown node_exporter:node_exporter /usr/bin/node_exporter

Copies the node exporter binary to /usr/bin directory and will change the ownership for Prometheus

- Step 5 Setup Node Exporter Service

sudo vi /usr/lib/systemd/system/node_exporter.service

Edit and configures the service file for Node Exporter
Also adds the following configuration 
[Unit]
Description=Node Exporter
Documentation=https://prometheus.io/docs/guides/node-exporter/
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
ExecStart=/usr/bin/node_exporter \
  --web.listen-address=:9100

[Install]
WantedBy=multi-user.target

then I exited using :wq, saved the file then executed a chmod command to change permissions of a file to be executable via Prometheus

- Step 6 Reload systemd and Start Node Explorer
  sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl status node_exporter
sudo systemctl enable node_exporter.service

will restart and reload and enable the node exporter service to start at server boot up everytime, I also initiated a sudo ufw allow 9100/tcp command to allow traffic to the 9100 port for node exporter

![image](https://github.com/user-attachments/assets/a839c9e1-c66b-4a82-8495-3e6eebad5528)
Above: image of NodeExporter functioning, while it was idle at the time of the snapshot, it proves that it can capture data.


### 6. Security
______________________________
The security aspect of the web hosting is extremely important for data protection, privacy and disaster potentiality. There are many ways to configure security methods on Grafana. Not only that, but prior mentioned, server and instance access is limited throughout the AWS. On the basis of system administration, security is ultimately one of (if not) the biggest game factors that are a big obstacle when working in the field. In many ways, security is important because

- Keeping integrity data: You always want to ensure that your dat is trustworthy and accurate
- Disasters and Risks: In the event of something like a ransomeware attack, there is much importance in developing security countermeasures from a physical and network standpoint
- Managing User Access: Resides in the role of an admin, managing users in the workplace and also handling how nonusers seek and perceive company data as an asset that can be acquired unethically

# Server Access
The restriction that is deployed on the server is both of software level and on hardware level of deployment. To start off, on the basis of Grafana, 
Grafana has access to determine which users are able to have admin privileges to make changes in the Grafana interface
![image](https://github.com/user-attachments/assets/4cb4eeb9-5b5e-48ed-9e89-3446d861b73b)
Above is an example of the User Access control

Referring to the NACL and SG created as above, the reasoning for selecting my personal IP address for access to is because I, as the admin am the person who wants to have total access control to the Grafana interface. I deem that Grafana can infact show sensitive data 

- Metrics releating to corporate network
- Potential vulnerabilites and wormholes
- Exposing potential IP Address
- Exposes logs and fixes and database layouts that exist in the network

  For Prometheus, it is essentially the same issue. There are numerous amounts of sensitive data that not everyone, including the users in the subnets should have access to. The private one may have access to sensitive information at times, but not all the time, where as the public one hypothetically may not have access to it. 

  As for the subnets, they were created to be separated from each other, via private on one, public on another. This is because it can segemnt and separate the flow of traffic from one device to another, esesentially ensuring that it receives the correct route at most times.

# Restriction of Access in AWS

Security Groups, which are essentially instance based control menus that control what goes in and what goes out. As mentioned before, I have developed both NACLs and Security Groups for both the VPC and the Instance. The devices on the subnet are given access to such limited information on both the NACL and the SG. 

In the terminal, I also configured firewall privileges, allowing access for the following ports
sudo ufw allow 22/tcp port for allowing access via SSH

sudo ufw allow 3000/tcp port for access to Grafana

sudo ufw allow 9090/tcp port for allowing Prometheus to function

sudo ufw allow 9100/tcp port for node-exporter functionality

Firewalls can benefit for a multitude of reasons, as firewalls are a determining factor that act as a shield or filter for incoming and outcoming traffic that is either trustable or untrustable, and when these commands are defined, the firewalls knows what packets it should be looking for in the ports. For one firewalls themselves, tightly control who can access the server remotely, and who can potentially make changes to the server remotely. And this goes for all of the ports mentioned above.

![image](https://github.com/user-attachments/assets/a47cfcdf-b40c-4efc-bf06-6ead9e5006b7)


### 7. Software Features
______________________________
As mentioned previously. Grafana - it is a multi-purpose and multi-platform analysis software that is designed for data visualization and data monitoring that can assist with the following per the Grafana website at https://grafana.com/

- Easily managing incident repsonses
- Hardware and Network monitoring
- Reducing costs
- User Management Dashboards
- Performance Tests
- Managing Alert systems
  and a plethora of useful tools that can server of great benefit for business and IT management desks alike

![image](https://github.com/user-attachments/assets/859f321a-2dc3-4da4-80b1-c4ca7e2824b9)
Systemm Processes Graph

![image](https://github.com/user-attachments/assets/27d045c0-d8d6-40c6-bd10-7caad9564c06)
Examples of Queries being ran in Grafana

![image](https://github.com/user-attachments/assets/6f09d63f-bdfe-4615-b25e-a34d980853e7)
Prometheus recognizing node-exporter

![image](https://github.com/user-attachments/assets/c2792f02-2ccc-4216-874d-61d32bf4e480)
Access Control List Usage in Grafana


### 8. Backup Policy
______________________________
While backups are hypothetical in this case, in real events, backups can be a crucial part of maintaining an AWS server environment and will help the server run more efficiently. 

#Good Backups will Consist of?

Good backups in the event of potential data loss that may effect both loss of Grafana and Prometheus. Grafana's suggestion is to consider backing up important information according to their website at https://grafana.com/docs/grafana/latest/administration/back-up-grafana/. such important information can be 

- Back up custom configuration file $WORKING_DIR/custom.ini (depending on where stored)
- Back up the defaults cofiguration file $WORKING_DIR/custom.ini
- Back up the default location for SQLite data in a binary or source installation: $WORKING_DIR/data/grafana.db
- MySQL can also be backed up using the commmands mysqldump -u root -p[root_password] [grafana] > grafana_backup.sql per according to the website
- Data assets and web assets used for building dashboards (Also good for Prometheus as well)
- All other important data files, like documents, photos, videos, emails, executionable, 7z files that may exist on the server

# Prometheus good backups?
Prometheus, another software that should also highly considered for backups. According to https://prometheus.io/docs/prometheus/latest/storage/ , Prometheus includes a local on-disk time series database, but also optionally integrates with remote storage systems. Meaning that it can be stored on both the server itself and on Prometheus itself too. I also found that Prometheus data storages can be conducted via a cloud service storage opportunity known as a S3 bucket.

# Good backups with consist of?
According to ChatGPT, this list may be a selection of good files to consider backing up for the Prometheus software

- Configuration file for Prometheus /etc/prometheus
- TSDB data files library  /var/lib/prometheus/ stores all time data series that is collected by Prometheus
- Created rules file that dictate recording and data rules for Prometheus
- Data assets and web assets used for building dashboards
- Service Discovery JSON or or YAML files used for performance optimied metrics and alert triggers via Prometheus 

  # Estimated Backup Time and Recovery incase of failure
  When performing the back up for Grafana, Prometheus, and most importantly the EC2 instance. All three are going to liekly have a dedicated backup time that will be based upon how much is stored onto each software at the current moment the backup is conducted.

  # Grafana

- Amount of Data: Since our Grafana instance here is relatively new, and not a lot of data is present, According to the Grafana website, the free version of Grafana can retain data for up to 14 days via https://community.grafana.com/t/metrics-and-data-retention/106507 I would suggest that the amount of data that can be that can be backed up and set for recovery is around

- 10 - 100 MB of assets and Grafana data

- Estimated Recovery Time: For long as the data that persists in the Grafana interfact and for long as the server has a stable internet connection, in the event of 

- Time to backup: For long as the server has a stable and decent internet connection or there is no corruption among the files. It can also vary for how much is stored on the actual services themselves, for example Prometheus may usually take longer than Grafana as it may hold lots of TSDB files and a TSDB snapshot that are important for the databases and is the operating system to function. the time to backup in the case of recovery is suggested to be aboutT

- 15~30 mins (according to ChatGPT for Grafana)

- Roughly 40mins ~ 2 hours (rough estimate, depending on how much backed up data exists)

- In the event of a failure, it would be much longer as there may be other steps to take in order to mitigate the failure.
According to ChatGPT a decent data plan could be to 

- First the Failure would need to be detected

- Then a Disaster Recovery Plan would need to be introduced, such as what to do, how data would be recovered in the event of a failure or disaster

- Launch new EC2 instance

-Attach same EBS volume (if intact) or new volume

-Reinstall Prometheus binary or use your AMI

-Reapply security groups, IAM roles, etc.

- Copy all backed up files on either Prometheus or Grafana over to the newly implemented libraries and directories for ability to reaccess
  For example, if a backup was conducted via S3, an entire directory that was backed up can be re copied back over to the software via a command like aws s3 sync s3://your-bucket/prometheus-tsdb/ /var/lib/prometheus/


   # 3-2-1 Go Backup Plan!
  One of the best countermeasures for data recovery is to establish a well defying and easy to use data backup plan. This is beneficial to have multiple access spots for data backup  to choose from if more than one option for data backup tends to not work in the event of failure or disaster. The 3-2-1 Backup Rile is an effective rule that will assist in the preservation and reliaility of data that is able to be recoverd determining on whether if it has been successfully backed up. For this rule, there are supposed to be "3 copies, 2 different media sources and 1 offsite copy"

  # 3 Copies: Starting with the 3 possibilities of where copies of data can be located for both the Instance, the Grafana service and the Prometheus service, there is
  
  - Stored elsewhere on the main EC2 instance. Aside from where the data for the EC2, Grafana and Prometheus can be normally stored, it could be wise to have one of my backups to be in a directory that exists on my server instance itself. It can contain all of the files needed to be backed up as previously mentioned. For example, AWS itself has a setting where it is able to setup a cloning process of data that exists on the server, essentially creating a backup drive
  - Stored on another server or computer. Despite the fact another server or computer is not infact set up, in the future of running these services, it could be beneficial to build another server or use a high capacity storage laptop in order to store data files previously mentioned
  - External storage device. Though, this may be basic, but it seems it can be a prevalent and good option to store data on in the event of a data loss, as the data can be stored on an external device and may also be readily available and easily accessible, not mention, external drives are encryptable, just like the server and the laptop
 
# 2 Two Different Types of Media Storage: Possibility of using a different storage media devices to store the information is a good practice 

- HDD/SSD's: Though, these may probably fit in with the External storage drive section from the 3 section, HDD's and SSD's external or even internal can serve of great beneficiary. Multiple drives in a storage unit could allow for Disk mirroring or RAID 1 which will allow for the replication of all data among a drive to be separated towards all the other drives that are available on the server.  Not mention, if we want a drive that has fast read and write speeds, fast performance overall, it would be wise to use a SSD, now the size, considering the amount of data that is possible to exist on these services, a 1TB could be worth while.

- Optical Drives: Though, these can be considered weaker in comparison to the external/internal HDD's/SSD's. They make a good solution for data backups accross a server. It is important to note that flash drives that have a storage capacity above 1TB do exist, and it would most certainly be worth while. It should not be too hateful, especially if the flash drive will only be holding data files from the services. Also, very portable, if needed to resume Grafana, EC2 or Prometheus on a laptop, it can be plugged in with ease and continued from there

  # 1 Offsite Copy: There should atleast be one area where the data mentioned is stored offsite

- Cloud Storage: For this, preferably, I would recommend using AWS S3, which is essentially an AWS Storage bucket designed for AWS connectivity that is able to act as a container in order to store various types of data. It is a decent method of storing and organizing data that is available in the EC2 instance, and as mentioned, is readily available on the instance as it is an AWS service. For example, going into the S3 function in AWS, you can create a bucket that can be attached to a VPC that is able to add security countermeasures such as bucket policies and access control lists that can dictate what can and cannot access the bucket. There are also other cloud options such as Google Drive, OneDrive Backblaze and IBM Cloud Services.

  


### 9. Troubleshooting Issues Encountered
______________________________

The only big issues that I encountered when isntalling Grafana and attempting to do a HTTPS certification on the server website. 
Starting with the when attempting to install Prometheus, I ran into issues when trying to use a sudo apt command.

It had conflicted with where my package for Grafana GPG key was located and installed in
![image](https://github.com/user-attachments/assets/8f1e61df-aeb9-473c-8417-bc05c21f7a8e)

 I used Copilot to help resolve the issue, and these were the steps taken as follows

 - Step 1 Updating the repository:
   echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list

   I ran this commmand to ensure that the APT would instead use only one key file. Then I wanted to remove the already pre-existing key that was causing conflicting issues 

- Step 2 Removing old key that existed on my end
  sudo rm /usr/share/keyrings/grafana.key

  There was a conflicting key that existed when I was initially downloading and installing on Grafana. Likely, a sheer accident I was able to remove this and re add the correct key which is performed in the next step/

- Step 3 Re adding the correct key
 curl -fsSL https://apt.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

- Step 4 pushing a sudo apt update regardless of the need
  After I issued all of these commands, I found that I was able to successfully push an apt command that would go through successfully

# Issue 2

This next issue I had was rather short and simple. When I was installing Grefana, I found that Grefana was in fact set up and running 
akin to command 
sudo systemctl status grafana-server 

However, I noticed that I could not for the life of me log into the hosted service using my Elastic IP Address associated with my instance.

I tried 

- Reinstalling Grefana
- Changing around NACL and Security Group IP Addresses
- Using Ryan Coy ( a colleague) advice to allow for Port 3000m access in both the NACL and SG
- Running a curl command at http://98.85.211.221:3000
- Running sudo systemctl status grafana-server command

An hour later, I found out that it was simply because the changes I was making to my NACL and SG were not actually pointed towards the VPC and Instance I was using. Surely enough, I associated them both and I was able to get into my Grafana hosting service with ease


 
