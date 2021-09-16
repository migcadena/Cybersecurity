# Cybersecurity
Cybersecurity Repository
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.


Diagrams/Azure_Cloud_Enviroment.png

These files have been tested and used to generate a live ELK (Elasticsearch, Logstash, and Kibana) deployment on Azure. These files can also be used to either recreate the entire deployment pictured above. Alternatively, select portions of the list of files may be used to install only certain pieces of it, such as Filebeat.

  - DVWA_Webservers-playbook.yml
  - ELK_playbook.yml
  - Filebeat-playbook.yml
  - Metricbeat-playbook.yml

This document contains the following details:

- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application running on Azure's VMs.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.

Load Balancers gives the security of redundancy of the DVWA instances, making it highly available. The more instances of a DVWA on the scope of the Load Balancer, and web-set will increase the availability of the service (DVWA in this case).  We have VMs Web1 and Web2 both running a container of DVWA, seted up on a webset with a public IP of the L.B.
The Jump Box is a secure and effective way for authorized administrators to connect to it, and from there perform desired adiministrative tasks to others VMs. To connect to untrusted enviroments, with out having to expose the entire network, could be another of the many use we can give to a Jump Box.
I used the JumbBox, not only as a jump point from our whitelisted IP address, but also to install the Ansible container, to deploy the configurations to our other VMs on the enviroment.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system configuration and/or data.
-Filebeat acts as a logging agent installed on the machine generating the log files.  It fowards the data to a either Logstah for further advanced processing, or directly into Elasticsearch for indexing.  It is able to pick changes made specific files of the monitored machine, configurations files, and logs changes of those files.  This is to monitor suspicious changes and activity. This server, analyzes data from Web1 and Web2 VMs.
 
-Metricbeat collectes machine's metrics, such as: up time, CPU and memory usage, and count of  in/out bytes of the monitored machines.  These metrics can tell analyst how "healthy" a system status is. On this topology the metrics of the VMs Web1 and Web2 are bing monitored.

The configuration details of each machine on our enviroment may be found below.


| Name       | Function           | Private IP Address | Public IP Address | Operating System   |
|------------|--------------------|--------------------|-------------------|--------------------|
| Jump-Box   | Gateway            | 10.0.0.4           | 58.168.129.14     | Ubuntu 20.04.3 LTS |
| Web1       | Application Server | 10.0.0.5           | None              | Ubuntu 20.04.3 LTS |
| Web2       | Application Server | 10.0.0.6           | None              | Ubuntu 20.04.3 LTS |
| ELK_Server | ELK Stack          | 10.1.0.4           | 52.229.23.37      | Ubuntu 20.04.3 LTS | 

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the ELK_Server (TCP 5601), Jump-Box (SSH), and the Load Balancer (HTTP) can accept connections from the Internet. Access to this machines is only allowed from the following IP addresses: 
 -Whitelisted IP addresses: 127.127.124.15

Machines within the network can only be accessed by the Jump-Box-Provisioner via SSH.
 -The Jump-Box-Provisioner machine with IP 10.0.0.4 was allowed SSH access to the RedTeam Virtual Network.

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses       |
|------------|---------------------|----------------------------|
| Jump Box   | Yes                 | Local.HomeNetwork.PublicIP |
| Web1       | No                  | 10.0.0.4                   |
| Web2       | No                  | 10.0.0.4                   |
| ELK_Server | Yes                 | Local.HomeNetwork.PublicIP |

### Elk Configuration

The Ansible container was used to automate configuration of the ELK server. No configuration was performed manually, which is advantageous because; it reduces worktime by automatization of tasks on a group of hosts being configured, and also, reduces human error as long as the configurations are correct.

The playbook implements the following tasks:

- After the ELK_server VM was created on Azure, it was added to the ansible hosts file, and an elk-playbook.yml was created to configure our new ELK VM.
- The elk-playbook.yml installs: docker.io, python3-pip, and docker.
- The elk-playbook.yml increases the ammount of virtual memory, and sets a configuration line on sysctl.conf file, to set it permanent after restart.  Also sysctl module is used to increase the use of memory.
- Once Docker is installed, the elk-playbook.yml downloads and run the sebp/elk:761 image.
- Once the container is installed and running the docker service is started.

After the elk-playbook.yml file finishes its job, SSH into the ELK server to confirm the ELK container was installed correclty.
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

(Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web1 10.0.0.5
- Web2 10.0.0.6

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeats monitors log files that you specify.  System filebeats collects and parses logs created by the system logging service running on Web1 and Web2 VMs, and can log ssh login events for example.
- Metricbeat collects metrics from the system and services running on the VMs Web1 and Web2.  It can give you specific information of CPU usage, memory usage, and network in/out bytes, with date and time.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

Web1 and Web2 for DVWA.
The playbook that was created to configure the Web1 and Web2 VMs was pentest-playbook.yml.
What this playbook does:
 -Removes apache2 if present in the VM.
 -Installs docker.io, python3-pip and Python Docker Module
 -Downloads and Launches the Web Docker Container DVWA
 -Enables the Docker Service.
After creating on Azure the Web1 and Web2 VMs, from SSH into the Ansible container: 
- Copy and run the pentest-playbook.yml on your ansible container to /etc/ansible/roles. 
- Update the hosts file on /etc/ansible/ to include the IP address of the Web VMs you want to configure, Append ansible_python_interpreter=/usrbin/python3, to make sure correct version of python is being used. You can create groups to specify which services you want to install, to different VMs.  In our case we have a group called "webservers".
To set the DVWA configuration on the Web1 and Web2 VMs run the playbook using the following command:
 'ansible-playbook /etc/ansible/roles/DVWA_Webservers-playbook.yml'
Once installed, the DVWA page should be reachable navigating to http://DVWA.IP.Address(LoadBalancer.IP.in.my.case)/setup.php

ELK:
SSH into the control node and follow the steps below:
- Copy the ELK-playbook.yml file to the Ansible container to /etc/ansible/roles.
- Update the hosts file on /etc/ansible/ to include the IP address of the ELK_server you want to configure, append ansible_python_interpreter=/usrbin/python3, to make sure correct version of python is being used. You can create groups to specify which services you want to install, to different VMs.  In our case we have a group called "elk".
- Run the playbook, and navigate to http://[your.ELK-VM.IP]:5601/app/kibana to check that the installation worked as expected.
 To run the playbook the command is: 'ansible-playbook /etc/ansible/roles/ELK-playbook.yml'
 To install filebeat and metricbeat on the Web VMs that you desire to monitor, the following playbooks were created:
filebeat-playbook.yml
metricbeat-playbook.yml
 And the following files needs to be edited, to include the IP address of the ELK server we have installed previously.
filebeat-config.yml (line #1105 and #1805 Replace IP address with the IP of your ELK machine.)
metricbeat-config.yml (line #62 and #95 Replace IP address with the IP of your ELK machine.)

**Before you run the playbook, make sure you place the config.yml files on a folder inside the same directory of the playbook, and name it "files".  Otherwise, when the playbooks run, they wont find the configuration settings.

 To run each of the playbooks to install filebeat and metricbeat, the commands are: 'ansible-playbook filebeat-playbook.yml' and 'ansible-playbook metricbeat-playbook.yml', respectively. Run one after it finishes the other.


