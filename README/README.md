## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![TODO: Update the path with the name of your diagram](Images/diagram_filename.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  - Enter the playbook file._
  # install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly responsive, in addition to restricting access to the network.
-  What aspect of security do load balancers protect? What is the advantage of a jump box? 
		Load balancers defend against distributed Denial-of-service attacks by shifting attack traffic from the corporate server to a public cloud provider. A jump box helps by limiting the access people have to your virtual network by requiring the private IPs of the individual machines in order to access them. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
-  What does Filebeat watch for? Filebeat monitors the log files or locations specified, looks for changes, collects log events, and forwards them for indexing.
-  What does Metricbeat record? Metricbeat helps monitor servers by collecting metrics from the system and services running on the server. It takes the metrics and sends them to the output that you specify such as Elasticsearch or Logstash.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.1   | Linux            |
| Web-1 |      Web Server used to run DVWA    |    10.0.0.5  | Ubuntu 18.04.5 LTS gen2 |
| Web-2 |     Web Server used to run DVWA  |    10.0.0.6   | Ubuntu 18.04.5 LTS gen2|
| ELK-SERVER | Run Elk container and Kabana| 10.1.0.4 |  Ubuntu 18.04.5 LTS gen2|

### Access Policies

The machines on the internal network are not exposed to the public Internet.

Only the Jump-Box-Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 68.186.15.98

Machines within the network can only be accessed by Jump-Box-Provisioner VM.
-  Which machine did you allow to access your ELK VM? Jump-Box-Provisioner
What was its IP address? 20.24.219.245

A summary of the access policies in place can be found in the table below.

| Name                 | Publicly Accessible | Allowed IP Addresses |
|----------------------|---------------------|----------------------|
| Jump-Box-Provisioner | Yes                 | 68.186.15.98         |
| Web-1                | No                  | 10.0.0.5             |
| Web-2                | No                  | 10.0.0.6             |
| ELK-SERVER           | Yes (Port 5601)     | 68.186.15.98         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it’s more efficient and less time consuming than manually configuring and allows changes to be made on all machines all at once instead of one by one.
- What is the main advantage of automating configuration with Ansible? Ansible allows you to not have to write custom code to automate your systems, you just have to list what you want done in the playbook and Ansible takes care of it for you.

The playbook implements the following tasks:
- Install docker.io on Elk 
- Install Python on Elk
-install docker python module
-increase virtual memory
-download and launch a docker elk container 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- List the IP addresses of the machines you are monitoring Web-1: 10.0.0.5 and Web-2: 10.0.0.6

We have installed the following Beats on these machines:
- Specify which Beats you successfully installed: Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
- In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc. 
Filebeat monitors the log files or locations that you specify, collects log events, and forwards them to either Elasticsearch or Logstash for indexing. To check the output from Filebeat you would connect to Kibana and check the logs for any changes. Metricbeat helps monitor your servers by collecting metrics from the system and services running on the server. To view the data you would go to Kibana, select the system you’d like to review and then look at the metrics.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml file to /etc/ansible/files/filebeat-config.yml.
- Update the filebeat-config.yml file to include host the elk’s private ip address “10.1.0.4:9200” and setup.kibana host to the elk’s private ip as well “10.1.0.4:5601”
- Run the playbook, and navigate to Kibana and click check data to check that the installation worked as expected.

Answer the following questions to fill in the blanks:
- _Which file is the playbook? Where do you copy it? Filebeat_playbook.yml and you copy it to /etc/ansible/roles/filebeat.playbook.yml
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on? 
The file you update to make Ansible run the playbook is Filebeat-config.yml. In order to specify which machine to install the ELK server on you edit the hosts file to distinguish between the different servers such as ‘webservers’ and ‘elk’. 
- _Which URL do you navigate to in order to check that the ELK server is running? http://20.83.33.24:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc. 
nano playbookname.yml - to create or update the playbook
Ansible-playbook playbookname.yml - to run the playbook

