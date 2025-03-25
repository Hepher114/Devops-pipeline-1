
# **Setting Up Ansible for Jenkins and worker on Ubuntu 22.04**

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
4. The `ubuntu` is the default user for AWS EC2 instances running ubuntu. If you're using a different Linux distribution or have a different username, replace `ubuntu` accordingly.
5. `/home/ubuntu` is the initial directory where the file will be copied. Then, you will move it to `/opt/`.
### Note:
- I used `server-key.pem` for all servers. It is the private key that will allow access to the server and is required for Ansible configurations or any other operations that need secure communication between the local machine and the server.

### Use `scp` to copy the file

```sh
scp /path/to/local/server-key.pem server-key.pem ec2-user@<ansible-public-ip>:/home/ubuntu/
```
### move the key to /opt
```hcl
sudo mv /home/ubuntu/server-key.pem /opt/server-key.pem
```

# Add Jenkins master and slave as hosts

Add the Jenkins master and slave private IPs in the inventory file (create a hosts file). In this case, we are using `/opt` as our working directory for Ansible.

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
This is an Ansible inventory file for managing a Jenkins setup with a master and worker node. It defines the private IP addresses for both the Jenkins master and worker, along with the SSH user (`ubuntu`) and private key (`/opt/server-key.pem`) used for authentication. The master and worker nodes are listed under separate groups, `[jenkins]` and `[worker]`, with corresponding configuration details to allow Ansible to manage these servers.


# Test the Connection

After you've set up Ansible and configured your inventory file with the Jenkins master and slave nodes, it's important to verify that Ansible can successfully communicate with the servers. This can be done using the ping module, which allows you to test the connection between your Ansible control node (the machine running Ansible) and the managed nodes (your Jenkins master and slave servers).

To test the connection, use the following command:

```sh
ansible -i hosts all -m ping
```
The output should confirm whether Ansible is able to reach the servers and communicate with them successfully. If the connection is successful, you will see a response similar to the one shown below:

![Screenshot 2025-03-23 193005](https://github.com/user-attachments/assets/a2b37316-cad7-4e3a-b52f-ffe573393602)

# Install jenkins master using ansible playbook
##  Add the Jenkins Repository Key
The first step is to add the Jenkins repository key to the target server. This ensures that the packages you install are verified and trusted. We will use the apt_key module in Ansible to achieve this.

Visit the official Jenkins website to confirm the current steps: https://pkg.jenkins.io/debian/. You can also refer to the Ansible documentation for the apt_key module here: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_key_module.html.

Here’s how to add the key using an Ansible playbook:
```hcl
- name: Add Jenkins repository key
  hosts: jenkins
  become: true
  tasks:
    - name: Add Jenkins key
      ansible.builtin.apt_key:
        url:  https://pkg.jenkins.io/debian/jenkins.io-2023.key
        state: present
```

## Add the Jenkins Apt Repository
Next, we need to add the Jenkins repository to the list of package sources.  You can also refer to the Ansible documentation for the apt_key module here: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html and https://pkg.jenkins.io/debian/

Add the following task to your playbook:
```hcl
    - name: Add Jenkins repository
      ansible.builtin.apt_repository:
        repo: "deb https://pkg.jenkins.io/debian binary/"
        state: present
```
## Update the Local Package Index
Before installing Jenkins, it’s a good practice to update the local package index to ensure you have the latest package information.

Add the following task to your playbook:
```hcl
    - name: Update apt package index
      ansible.builtin.apt:
        update_cache: yes
```
## Install Jenkins
```hcl
    - name: Install Jenkins
      ansible.builtin.apt:
        name: jenkins
        state: present
```
## Install Java
```hcl
    - name: Install Java
      ansible.builtin.apt:
        name: openjdk-17-jre
        state: present
```
## Start and Enable Jenkins Service
 You can also refer to ansible.builtin.service module: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html
```hcl
    - name: Start Jenkins service
      ansible.builtin.service:
        name: jenkins
        state: started

    - name: Enable Jenkins service on boot
      ansible.builtin.service:
        name: jenkins
        enabled: yes
```
## Final version 
```hcl
---
- name: Install Jenkins Master
  hosts: jenkins
  become: true
  tasks:
    # Add the Jenkins Repository Key
    - name: Add Jenkins repository key
      ansible.builtin.apt_key:
        url: https://pkg.jenkins.io/debian/jenkins.io-2023.key
        state: present

    # Add the Jenkins Apt Repository
    - name: Add Jenkins repository
      ansible.builtin.apt_repository:
        repo: "deb https://pkg.jenkins.io/debian binary/"
        state: present

    # Update the Local Package Index
    - name: Update apt package index
      ansible.builtin.apt:
        update_cache: yes

    # Install Java (Jenkins dependency)
    - name: Install Java
      ansible.builtin.apt:
        name: openjdk-17-jre
        state: present

    # Install Jenkins
    - name: Install Jenkins
      ansible.builtin.apt:
        name: jenkins
        state: present

    # Start and Enable Jenkins Service
    - name: Start Jenkins service
      ansible.builtin.service:
        name: jenkins
        state: started

    - name: Enable Jenkins service on boot
      ansible.builtin.service:
        name: jenkins
        enabled: yes
```

Once you've created the playbook, paste your configuration inside the jenkins-setup.yaml file ( replace it with the name of your yaml file )and then run it using the following command:
```hcl
ansible-playbook -i /opt/hosts /opt/jenkins-setup.yaml
```
You should get an output similar to this:

![Screenshot 2025-03-23 223512](https://github.com/user-attachments/assets/e2b9a29d-21aa-42e6-9869-e2f9db362f4f)
The playbook will install both Jenkins and Java on the Jenkins master server
# Configuring Jenkins Master
Access Jenkins: Open a browser and go to `http://<jenkins-ip>:8080`.

![Screenshot 2025-03-23 231831](https://github.com/user-attachments/assets/d1a7b98b-30e2-4a76-ae12-23f1a891fe1b)
- Unlock Jenkins: Retrieve the initial admin password from the server and enter it in the web interface.
- Install Plugins: Choose Install suggested plugins to set up essential plugins.
- Create Admin User: Set up an admin account with a username, password, and email.
- Access Dashboard: Complete setup and access the Jenkins dashboard.


# Install Maven using ansible playbook 

Now that Jenkins is fully configured and operational on the master node, the next step is to prepare the worker node to handle builds using Maven. To streamline this process, we will use an Ansible playbook to automate the installation and configuration of Maven on the worker node. 

Update Ubuntu Repository and Cache
```hcl
---
- hosts: worker
  become: true 
  tasks: 
  - name: update ubuntu repo and cache 
    apt: 
      update_cache: yes 
      cache_valid_time: 3600 

  - name: install java 
    apt: 
      name: openjdk-17-jre
      state: present

  - name: download maven packages 
    get_url:
      url: https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.tar.gz
      dest: /opt

  - name: extract maven packages 
    unarchive:
      src: /opt/apache-maven-3.9.9-bin.tar.gz
      dest: /opt 
      remote_src: yes
      dest: /opt      
```
Now, you can execute the playbook by running the following command:
```hcl
ansible-playbook -i /opt/hosts worker.yaml
```
Once the playbook has completed successfully, you can verify the installation by logging into the worker server. Navigate to the /opt directory and list its contents using the ls command:
```hcl
cd /opt
ls
```
Here’s an example of what the output might look like:
![Screenshot 2025-03-24 025505](https://github.com/user-attachments/assets/19affc36-a8f8-4fdb-a04d-d05f9047ccea)

## References

- **Download Packages from a URL**:  
  [ Module Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html)  
- **Download Maven**:  
  [Apache Maven Download Page](https://maven.apache.org/download.cgi)  
- **Unarchive Files**:  
  [ Module Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/unarchive_module.html)  
