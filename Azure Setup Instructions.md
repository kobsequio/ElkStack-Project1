# **CREATING** : 
AZURE CLOUD ENVIRONMENT

# 1) 
## Design the Cloud Environment
-   Create a Resource Group
    - name your resource group and set region 
    
    Create Vnet (Virtual Network)
    - create a new virtual network
    - name it something memorable ex; "RedNet"
    - make sure to select the identical resource group and region
    - use the default ip and subnet settings

    Create the Network Security Group (NSG)
    - create a network security group that is part of the same resource group
    - name NSG ex; "My-Firewall"
    - ensure NSG is identical to everything else previously created

    Add Inbound Security Rules 
    - create a rule that blocks incoming traffic, with a high priority number

# 2)
## Design the Virtual Machines
- Create Jump Box VM
    - log into Azure account
    - click on virtual machines box, click "Add" , click "Virtuaul Machine"
    - set resource group to resource group created from Cloud Environment
    - name jump box vm something memorable ex; "Jump-Box-Provisioner"
    - use Ubuntu server with atleast 1GB of memory
    - use public SSH key from your local machine and give it a username that is memorable ex; "azureuser"
        >use 'ssh-keygen' to produce generate key.

- Set up Docker.io on the Jump Box VM
    - SSH into your jump-box VM using public it's public IP address
        >ensure VM is turned ON 
        >'ssh azureuser@(public IP)'

    - Once logged in, run the following commands:
        >'sudo apt install docker.io'
        >'sudo docker pull cyberxsecurity/ansible'
        >launch Ansible container, run: 'docker run -ti cyberxsecurity/ansible:latest bash' to ensure its working
        >run 'exit'
  
  - Create 3 Virtual Machines
    - setup three additional virtual machines
    - name them something memorable ex; "Web-1 Web-2 Web-3"
    - match following criteria:
        - allow no public IP address
        - create new availability set, that will be set for the following three VMs
        - name it something memorable ex; "WEB-SET"
        - connect the VMs to the Virtual Network (VNet) and to the Network Security Group (NSG) created in the previous steps
    - use the public SSH key from the jump-box VM docker container
    - set username to something memorable ex; "azureuser"
        >use 'ssh-keygen' to produce public key if needed on jump-box VM
    - ensure that SSH ports are open          
        - ssh into jump-box-vm
        - start and attach your docker container: run 'sudo docker start <DVWA>' && 'sudo docker start attach <DVWA>'
        - once in container, SSH into each Web-VM to ensure ports are wokring
            - 'ssh azureuser@10.0.0.10'
            - 'ssh azureuser@10.0.0.11'
            - 'ssh azureuser@10.0.0.12'

- Here is what your VMs should look like at this point:
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-virtual-machines.png)

- Config and  Hosts File
    - 'cd' into the '/etc/ansible/' directory 
    - run 'nano ansible.cfg'
    - scroll to "remote-user" section and update to include new username "azureuser"
    - run 'nano /etc/ansible/hosts' 
        >uncomment the [webservers] header
        >under the header 
        >add the internal IP addresses of the three VMs with description: 
        - 10.0.0.10 ansible_python_interpreter=/usr/bin/python3
        - 10.0.0.11 ansible_python_interpreter=/usr/bin/python3
        - 10.0.0.12 ansible_python_interpreter=/usr/bin/python3   

# 3)
## Design Load Balancer
- Create new Load Balancer
    - create new Load Balancer in Azure
    - select static IP address and set identical Resource Group and region
    - select Create New Public address
    - name Load Balancer something memorable ex; "Red-Team-Pool"
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-load-balancer.png)
    
- Add new Health Probe 
    - use default settings
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-health-probe.png)

- Create new Backend Pool and add 3 Web-VMs to it
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-backend-pools.png)

# 4)
## Logging into Jump-Box-Provisioner
- Log into Azure and turn ON Jump-Box-Provisioner VM
    - 'ssh azureuser@(public IP)'
    - public IP will change everytime you turn on your Jump-Box-Provisioner VM

# 5)
## Starting Docker
- Check which containers you have
    - 'sudo docker container list -a'
    - check under "IMAGE" column for 'cyberxsecurity/ansible:latest'
    - check which "NAMES" lines up for the "IMAGE"

    - to start container, use following commands:
        > 'sudo docker start <DVWA name>'
        > 'sudo docker attach <DVWA name>'
    - to ensure connection run: 'whoami'

- Network Security Group Rules
    - here is an overview of all inbound rules for Network Security Group:
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Network-security-groups.png)

## 6)
# Install ELK Stack
- Design brand new VNet (Virtual Network)
    - create new VNet that is in the same Resource Group, BUT in a different region ex; "East US"
    - name this VNet something memorable ex; "Project-Elk"
    ![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-virtual-networks.png)

- Create a peer connection between the two VNets (RedNet & EastNet)
    - in the EastNet page, click "Peerings"
    - click "Add New"
    - create connection from Project-Elk(VNet) to RedNet(VNet)
    - name it something memorable ex; "Elk-to-Red"
    - create a new connection from RedNet(Vnet) to Project-Elk(VNet)
    - name it something memorable ex; "Red-to-Elk"
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-peerings.png)

- Design New Virtual Machine
    - create with Ubuntu VM : 4GB minimum RAM size 
    - IP address will be public 
    - time zones will be set to your brand new VNet (Project-Elk) ex; East US
    - VNet will be set to Project-Elk
    - Resource Group will be set identical to other VMs, RedNet
    - name this VM something memorable ex; "Project-ELK"

    - SSH into VM to ensure connection

- Download and Configure the container
    - add the new (Project-ELK) VM IP address to the hosts file in the ansible container (DVWA)
        >run : 'cd /etc/ansible'
        >run : 'nano hosts'
    - add "Project-ELK" ip under new [elk] group
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/hosts-file.png)

    Create a playbook that will Configure the "Poject ELK" VM. 
    - ![elk-playbook](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/ELK-playbook.txt)

- Launch and Expose the Container
    - Run created Elk Playbook 
    - 'ansible-playbook elk-playbook.yml'
    - SSH into "Project-ELK" VM 
    - run 'docker ps -a'
    - note that you can see the Container was created succesfuly

- Identity and Access Management
    - We will restrict access to "Project-ELK" VM through "Project-Elk-nsg" (NSG)
    - in NSG for "Project-ELK" , add an inbound rule that will allow the access from local machine to "Project-ELK" on port:5601
    - add additional security rule that will restrict all access to "Project Elk" VM with a higher priority
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Azure%20Instructions/Azure-Elk-NSG.png)
- Verify login into the server through Web Browser
    - (ELK public ip):5601/app/kibana
    - note public IP will alwats change upon restart

# 7)
## Install Filebeat
## What is Filebeat?
- We use Filebeat to collect, parse, and interpret ELK logs in one command
    - this will allow for efficient tracking of our security goals
    
- Install FIlebeat on the DVWA Container
    - start all VMs
    - access the Kibana page and ensure connection
    - start DVWA container through jump-box-provisioner VM
        >run : 'sudo docker start (DVWA)'
        >run : 'sudo docker attach (DVWA)'
    - switch to the Kibana page and find DEB page for creating a system log and use this guide to create our filebeat-playbook.yml

- Create Filebeat Configuration file
    - create a folder called files in '/etc/ansible' directory of the container
        >run: 'mkdir files'
    - download filebeat-config.yml
        >run: 'curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/files/filebeat-config.yml'
    - open filebeat-config.yml and add the "Project-ELK" VM private IP address in two areas

- Create Filebeat Installation Playbook
    - create the filebeat-playbook.yml in '/etc/ansible/roles'
    - using the DEB page, add the needed commands

    - run the playbook when complete

- Verify Installation and Playbook
    - back on the Kibana webpage, click "Check data" on the instruction page
    - review data to ensure connection

# 8)
## Create a Playbook to install Metribeat
- Create a Config file for Metricbeat using the same steps as Filebeat
- Add "Project-ELK" VMs private IP address in the two areas


- Under the /roles directory
- Create the Metricbeat-playbook.yml

## Once Both Playbooks are created, Run Them. 

## After running both Playbooks succesfully 
    
- check back to the Kibana webpage 
- click "Check data"
- review data to ensure connection
    