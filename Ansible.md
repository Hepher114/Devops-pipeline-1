
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
   ```
# Sending the server-key.pem to the ansible server 

To send the `server-key.pem` to the server using `scp` and then move it to the desired directory, follow these steps:

1. Ensure you have the correct file path for your `key.pem` on your local machine.
2. Replace `<server-ip>` with the IP address of the server where Ansible is being set up.
3. Replace `/path/to/local/server-key.pem` with the actual path to your `key.pem` file on your local machine.
4. The `ec2-user` is the default user for AWS EC2 instances running Amazon Linux. If you're using a different Linux distribution or have a different username, replace `ec2-user` accordingly.
5. `/home/ubuntu` is the initial directory where the file will be copied. Then, you will move it to `/opt/`.
### Note:
- I used `server-key.pem` for all servers. It is the private key that will allow access to the server and is required for Ansible configurations or any other operations that need secure communication between the local machine and the server.

### Use `scp` to copy the file

```sh
scp /path/to/local/server-key.pem server-key.pem ec2-user@<server-ip>:/home/ubuntu/
```
### SSH into the server and move the file

First, SSH into the server:

```sh
ssh ec2-user@<server-ip>
### After logging into the server, move the file from `/home/ubuntu` to `/opt/`:

```sh
sudo mv /home/ubuntu/server-key /opt/server-key.pem
```

