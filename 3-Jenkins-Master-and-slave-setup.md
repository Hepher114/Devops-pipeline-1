# Jenkins Master and Slave Setup: A Step-by-Step Guide

In Jenkins, a master-slave architecture allows for distributed builds, making it easier to manage large-scale projects by running builds on different machines. In this article, we'll guide you through the process of setting up Jenkins Master and Slave nodes. This setup can improve build performance, increase scalability, and help manage complex build environments.

## Step 1: Add Credentials

Before adding a slave node to Jenkins, we first need to set up SSH credentials that Jenkins will use to connect to the slave machine.

### How to Add Credentials in Jenkins:
1. **Navigate to Jenkins Dashboard**  
   Go to **Manage Jenkins** from the left-hand menu.

2. **Manage Credentials**  
   Select **Manage Credentials** from the next screen.

3. **System**  
   In the **Manage Credentials** page, select **(global)** under the **Stores scoped to Jenkins** section, then click **Global credentials**.

4. **Add Credentials**  
   On the right-hand side, click on **Add Credentials** to open the credentials form.

5. **Fill in the Credential Information**  
   Provide the following information to add the SSH credentials:
   - **Kind**: Select `SSH Username with private key`.
   - **Scope**: Set to `Global` (this makes the credentials available throughout Jenkins).
   - **ID**: Enter `maven_slave` (this ID will help you identify the credentials).
   - **Username**: Enter the SSH username used on your slave machine. For example, use `ec2-user` for an EC2 instance.
   - **Private Key**: Paste the content of the private key (usually found in the `.pem` file you use for SSH access).

6. **Save the Credentials**  
   Once the information is filled in, click **OK** to save the credentials.

Now that the credentials are set up, Jenkins will use these credentials to communicate with the slave node.

## Step 2: Add a New Node (Slave)

Now that the credentials are ready, the next step is to add the slave node. The slave node will handle the execution of Jenkins jobs, allowing the master to delegate tasks to it.

### How to Add a Slave Node:
1. **Go to Manage Jenkins**  
   From the Jenkins dashboard, click on **Manage Jenkins**.

2. **Manage Nodes and Clouds**  
   Select **Manage Nodes and Clouds** from the available options.

3. **Create a New Node**  
   In the **Manage Nodes** screen, click on **New Node** to create a new slave node.

4. **Provide Node Information**  
   Fill in the necessary details for your slave node:
   - **Node Name**: Provide a name for your slave node. For example, `maven_slave`.
   - **Node Type**: Select **Permanent Agent**.
   
5. **Configure Node Details**  
   Provide the following information in the node configuration page:
   - **Number of Executors**: Set this to `3`. This will allow the slave node to run up to three simultaneous jobs.
   - **Remote Root Directory**: Enter `/home/ec2-user/jenkins`. This is the directory on the slave machine where Jenkins will store its files and configurations.
   - **Labels**: Use a label like `maven` to identify the type of work this node is dedicated to.
   - **Usage**: Set to `Use this node as much as possible`, meaning Jenkins will prefer using this slave node when executing jobs.
   
6. **Launch Method**:  
   Choose **Launch agents via SSH**. This tells Jenkins how to connect to the slave machine.
   - **Host**: Enter the private IP address of the slave machine. For example, `<Private_IP_of_Slave>`.
   - **Credentials**: Select the `maven_slave` credentials that you previously added.
   - **Host Key Verification Strategy**: Select `Non verifying Verification Strategy`. This setting tells Jenkins not to verify the SSH host key, which is useful when connecting for the first time.
   
7. **Availability**:  
   Set the availability option to **Keep this agent online as much as possible**. This ensures that the slave node stays online and ready for job execution as much as possible.

8. **Save the Configuration**  
   Once all the information is filled in, click **Save** to add the slave node.

## Conclusion

After following the steps above, you have successfully set up a Jenkins master-slave architecture. Your master node will now be able to delegate tasks to the slave node, utilizing its resources to execute builds. This setup improves performance by distributing the load across multiple machines and increases Jenkins’ scalability for larger projects.

By adding SSH credentials and configuring a slave node in Jenkins, you ensure a smooth and efficient distributed build process, making Jenkins more capable and adaptable to your project's needs.
