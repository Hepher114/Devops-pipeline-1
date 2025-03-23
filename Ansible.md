
# Setting Up Ansible for Jenkins and worker on Ubuntu 22.04

In this article, we will walk you through the process of setting up Ansible on an Ubuntu 22.04 machine and configuring it to manage Jenkins master and slave nodes. Ansible is a powerful automation tool that allows you to manage your infrastructure, deploy software, and orchestrate tasks across multiple machines. 

We will guide you through the following steps:
- Installing Ansible on Ubuntu 22.04
- Sending the private key (`server-key.pem`) to the server using `scp`
- Configuring the Jenkins master and slave nodes in Ansible's inventory file
- Testing the connection to ensure Ansible is correctly configured

For a detailed guide on installation and configuration, we also refer to the official [Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html). Let’s get started!

# Setup Ansible
1. Install ansible on Ubuntu 22.04  
   For detailed installation instructions, refer to the official [Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html).  
   To install Ansible, run the following commands:
   ```sh 
   sudo apt update
   sudo apt install software-properties-common
   sudo add-apt-repository --yes --update ppa:ansible/ansible
   sudo apt install ansible
