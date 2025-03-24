
# Setting Up Ansible for Jenkins and worker on Ubuntu 22.04

Here we will walk you through the process of setting up Ansible on an Ubuntu 22.04 machine and configuring it to manage Jenkins master and slave nodes. Ansible is a powerful automation tool that allows you to manage your infrastructure, deploy software, and orchestrate tasks across multiple machines. 

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
4. The `ec2-user` is the default user for AWS EC2 instances running ubuntu. If you're using a different Linux distribution or have a different username, replace `ubuntu` accordingly.
5. `/home/ubuntu` is the initial directory where the file will be copied. Then, you will move it to `/opt/`.
### Note:
- I used `server-key.pem` for all servers. It is the private key that will allow access to the server and is required for Ansible configurations or any other operations that need secure communication between the local machine and the server.

### Use `scp` to copy the file

```sh
scp /path/to/local/server-key.pem server-key.pem ec2-user@<ansible-server-ip>:/home/ubuntu/
```
### SSH into the server and move the file
```hcl
sudo mv /home/ubuntu/server-key.pem /opt/server-key.pem
```

# Add Jenkins master and slave as hosts

Add the Jenkins master and slave private IPs in the inventory file (hosts). In this case, we are using `/opt` as our working directory for Ansible.

```hcl
[jenkins]
14.409.28.174  # Replace this with your Jenkins master private IP

[jenkins:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/opt/server-key.pem

[worker]
13.209.18.174   # Replace this with your Jenkins slave private IP

[worker:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/opt/server-key.pem
```
This is an Ansible inventory file for managing a Jenkins setup with a master and worker node. It defines the private IP addresses for both the Jenkins master and worker, along with the SSH user (`ec2-user`) and private key (`/opt/server-key.pem`) used for authentication. The master and worker nodes are listed under separate groups, `[jenkins]` and `[worker]`, with corresponding configuration details to allow Ansible to manage these servers.


# Test the Connection

After you've set up Ansible and configured your inventory file with the Jenkins master and slave nodes, it's important to verify that Ansible can successfully communicate with the servers. This can be done using the ping module, which allows you to test the connection between your Ansible control node (the machine running Ansible) and the managed nodes (your Jenkins master and slave servers).

To test the connection, use the following command:

```sh
ansible -i hosts all -m ping
```
![Screenshot 2025-03-23 193005](https://github.com/user-attachments/assets/a2b37316-cad7-4e3a-b52f-ffe573393602)

