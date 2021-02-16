## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/ELK%20project%20Topography.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.

[Filebeat-Playook](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/Filebeat%20Playbook.txt)  

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damm Vulnerable Web Application.

Load balancing ensures that the application will be highly reliable, in addition to restricting access to the network.

- Load balancers protect against DoS attacks.
- This ensures the accuracy of the applications as well as restricting access into the network. 
- The advantage of "Jump-Box-Provisioner" is limiting access to one administrator.


Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the machine and system logs.
- **Filebeat** monitors for information on the log files, examples would be: which files have been changed and when.
- **Metricbeat** records information every so often about the operating system and services running on the VM. These statistics are then interpreted to an ouput specified. In this case we are using Elasticsearch.  

The configuration details of each machine may be found below.

```git
| Name                 | Function         | IP Address  | Operating System |
|----------------------|------------------|-------------|------------------|
| Jump-Box-Provisioner | Gateway          | 10.0.0.1    | Linux            |
| Web-1                | DVWA Containers  | 10.0.0.10   | Linux            |
| Web-2                | DVWA Containers  | 10.0.0.11   | Linux            |
| Web-3                | DVWA Containers  | 10.0.0.12   | Linux            |
| Project-ELK          | Configuration VM | 10.1.0.4    | Linux            |
````
### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the "Jump-Box-Provisioner" machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 0.0.0.0 = My 'personal' machine

Machines within the network can only be accessed by **DVWA** container, within "Jump-Box-Provisioner" VM.
- Only my personal machine will have access to the "Project-ELK" machine, through port: 5601. This IP is: 0.0.0.0 <'personal' IP>

A summary of the access policies in place can be found in the table below.

```git
| Name                 | Publicly Accessible | Allowed IP Addresses |
|----------------------|---------------------|----------------------|
| Jump Box Provisioner | Yes                 | 10.0.0.1, 0.0.0.0    |
| Web-1                | No                  | 10.0.0.1             |
| Web-2                | No                  | 10.0.0.1             |
| Web-3                | No                  | 10.0.0.1             |
| Project-ELK          | No                  | 10.0.0.1, 0.0.0.0    |
````
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- The main advantage of automating configuration with Ansible, is that it will eliminate human error from manually configuring machines seperately.
- Running limited services 
- Installation and updates can be stremlined
- Process will be more simple to replicate

The playbook implements the following tasks:
- Install Docker.io
- Install pip3
- Install Docker python module
- Increase virtual memory
- Download and launch a docker elk container

The following screenshot displays the result of running `docker ps -a` after successfully configuring the ELK instance.

![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/docker%20ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.10 = Web-1
- 10.0.0.11 = Web-2
 -10.0.0.12 = Web-3

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:

- **Filebeat** will monitor specifically indicated log files, gather log events, and forward them to Elasticsearch or Logstash for cataloging. One example of what to expect to see is "system log events" on the dashboard.
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Filebeat.png)

- **Metricbeat** will periodically retrieve data from services running on the server, and from the operating system. Metricbeat then gathers this data and distributes them based on the specified output. These outputs include: Logstash or Elasticsearch. One example of what to expect to see  is memory usage.
![](https://github.com/kobsequio/ElkStack-Project1/blob/main/Diagrams/Metricbeat.png)

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured:

SSH into the control node and follow the steps below:
- Copy the [Filebeat-playbook.yml](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/Filebeat%20Playbook.txt) file to /etc/ansible/roles
- Update the ['hosts'](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/hosts.txt) file to include **[webservers]** & **[elk]**
- Run the playbook, and navigate to Kibana to check that the installation worked as expected.
- Navigate to : http://<Host IP>/app/kibana#/home

FOR REFERENCE:
- [Filebeat-playbook.yml](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/Filebeat%20Playbook.txt)
- [Metricbeat-playbook.yml](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/Metricbeat%20Playbook.txt)
- [ELK-playbook](https://github.com/kobsequio/ElkStack-Project1/blob/main/Ansible/ELK-playbook.txt)

