## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Azure%20Lab%20Enviroment.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

Install-ELK.yml
```
  ---
- name: Configure Elk VM with Docker
  hosts: elkservers
  become: True
  tasks:
 
  - name: Install Packages docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present
 
  - name: Install Packages python-pip
    apt:
      force_apt_get: yes
      update_cache: yes
      name: python3-pip
      state: present
 
  - name: Install docker pip Packages 
    pip:
      name: docker
      state: present

  - name: Set RAM usage
    ansible.posix.sysctl:
      name: vm.max_map_count
      value: '262144'
      state: present

  - name: Download and Configure docker container elk
    docker_container: 
      name: elk
      image: sebp/elk:761
      state: started
      published_ports: 5601:5601,9200:9200,5044:5044
  
  - name: Enable docker sercive
    systemd:
      name: docker
      enabled: yes
 ```
 
 Filebeat-Playbook.yml
``` 
  ---
- name: installing and launching filebeat
  hosts: webservers
  become: true
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

 
  - name: install filebeat deb
    command: sudo dpkg -i filebeat-7.6.1-amd64.deb

  - name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/roles/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: sudo filebeat modules enable system

  - name: setup filebeat
    command: sudo filebeat setup

  - name: start filebeat service
    command: sudo service filebeat start
```

Metricbeat-Playbook.yml
```
  ---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb

    # Use command module
  - name: install metricbeat
    command: sudo dpkg -i metricbeat-7.6.1-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/roles/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: sudo metricbeat modules enable docker

    # Use command module
  - name: setup metric beat
    command: sudo metricbeat setup

    # Use command module
  - name: start metric beat
    command: sudo service metricbeat start
```

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting traffic to the network.


Load balancers procect an organization form DDoS (Denial of Service Attack) attacks. Protect an organizations availability, web traffic, web security. 

The advantage of a jump box is that it allows for out-of-band (OOB) remote access use. Allowing for automation, security, and access control.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.

Filebeat moniters log files or locations that you specify, collets log events, and forwards them either to Elasticsearch or Logstash for indexing.

Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name       | Function     | IP Address | Operating System |
|------------|--------------|------------|------------------|
| Jump Box   | Gateway      | 10.0.0.4   | Linux            |
| Web 1      | Webserver    | 10.0.0.5   | Linux            |
| Web 2      | Webserver    | 10.0.0.6   | Linux            |
| Web 3      | Webserver    | 10.0.0.7   | Linux            |
| SaadELK-VM | Kibana Server| 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Bpx Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 52.247.28.248
          
Machines within the network can only be accessed by SSH Key Connection.

   The machine allowed to access the ELK VM is the Jump Box Provisioner with the IP address of 104.211.28.134 , which then connects to the Ansible container through SSH key, finally allowing connection to the ELK VM. 

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses |
|------------|---------------------|----------------------|
| Jump Box   | Yes                 | 10.0.0.4             |
| Web 1      | No                  | 10.0.0.5             |
| Web 2      | No                  | 10.0.0.6             |
| Web 3      | No                  | 10.0.0.7             |
| SaadELK-VM | Yes                 | 10.1.0.4             |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...

  Primary benifit of Ansible is it allows IT professionals to automate daily processes and tasks.            

The playbook implements the following tasks:

Configure ELK VM with Docker:

1.Install Packages docker.io
2.Install Packages python-pip
3.Install docker pip Packages 
4.Set RAM Usage
5.Download and Configure docker container elk 
6.Enable docker service

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Screen%20Shot%202020-12-14%20at%209.48.15%20PM.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:

Web 1: 10.0.0.5
Web 2: 10.0.0.6
Web 3: 10.0.0.7

We have installed the following Beats on these machines:

-Filebeat

These Beats allow us to collect the following information from each machine:

-Filebeat allows us to collect the changes made and at what time the changes were made. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

-Copy the playbook file to /etc/ansible
-Update the host file to include the webservers and ELK-VM
-Run the playbook, and SSH into the elk vm, then run 'docker ps' to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
 Which file is the playbook?

   -Playbook: install_elk.yml
   -Location: /etc/ansible/install_elk.yml

Copy:
Install-ELK.yml
```
  ---
- name: Configure Elk VM with Docker
  hosts: elkservers
  become: True
  tasks:
 
  - name: Install Packages docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present
 
  - name: Install Packages python-pip
    apt:
      force_apt_get: yes
      update_cache: yes
      name: python3-pip
      state: present
 
  - name: Install docker pip Packages 
    pip:
      name: docker
      state: present

  - name: Set RAM usage
    ansible.posix.sysctl:
      name: vm.max_map_count
      value: '262144'
      state: present

  - name: Download and Configure docker container elk
    docker_container: 
      name: elk
      image: sebp/elk:761
      state: started
      published_ports: 5601:5601,9200:9200,5044:5044
  
  - name: Enable docker sercive
    systemd:
      name: docker
      enabled: yes
 ```
 
Which file do you update to make Ansible run the playbook on a specific machine?
How do I specify which machine to install the ELK server on versus which to install Filebeat on?

-Edit the /etc/ansible/host file to add webserver/elkserver IP addresses


Which URL do you navigate to in order to check that the ELK server is running?

	-http://52.247.28.248:5601/app/kinana

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Screen%20Shot%202020-12-14%20at%2010.00.03%20PM.png)

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Screen%20Shot%202020-12-14%20at%2010.03.05%20PM.png)

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Screen%20Shot%202020-12-16%20at%2010.06.50%20PM.png)

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Screen%20Shot%202020-12-16%20at%2010.07.04%20PM.png)

![Alt text](https://github.com/Ssheikh1234/End-Term-Project/blob/main/Images/Screen%20Shot%202020-12-16%20at%208.39.43%20PM.png)
