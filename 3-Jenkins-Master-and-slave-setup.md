# Set up of Jenkins Master and Slave 

 In this article, we'll guide you through the process of setting up Jenkins Master and Slave nodes. T

## Add Credentials

Before adding a slave node to Jenkins, we first need to set up SSH credentials that Jenkins will use to connect to the slave machine.


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
   - **Scope**: Set to `Global` .
   - **ID**: Enter `maven-slave-credential` for example .
   - **Description**: Enter `maven-slave-credential` for example .
   - **Username**: Enter the SSH username used on your slave machine. For example, use `ubuntu` for an EC2 instance.
   - **Private Key**: Paste the content of the private key (usually found in the `.pem` file you use for SSH access).
![Screenshot 2025-03-25 000746](https://github.com/user-attachments/assets/d5cb2a2d-f384-43eb-8212-3871333b442b)

6. **Save the Credentials**  
   Once the information is filled in, click **OK** to save the credentials.

Now that the credentials are set up, Jenkins will use these credentials to communicate with the slave node.

## Add a New Node (Slave)

Now that the credentials are ready, the next step is to add the slave node. The slave node will handle the execution of Jenkins jobs, allowing the master to delegate tasks to it.

### How to Add a Slave Node:
1. **Go to Manage Jenkins**  
   From the Jenkins dashboard, click on **Manage Jenkins**.

2. **Nodes**  
   Select **Nodes** from the available options.

3. **Create a New Node**  
   In the **Manage Nodes** screen, click on **New Node** to create a new slave node.

4. **Provide Node Information**  
   Fill in the necessary details for your slave node:
   - **Node Name**: Provide a name for your slave node. For example, `Maven_slave`.
   - **Node Type**: Select **Permanent Agent**.
   - **Create**: Then click on create
   
5. **Configure Node Details**  
   Provide the following information in the node configuration page:
   - **Number of Executors**: Set this to `3`. This will allow the slave node to run up to three simultaneous jobs.
   - **Remote Root Directory**: Enter `/home/ubuntu/jenkins`. This is the directory on the slave machine where Jenkins will store its files and configurations.
   - **Labels**: Use a label like `Maven` to identify the type of work this node is dedicated to.
   - **Usage**: Set to `Use this node as much as possible`, meaning Jenkins will prefer using this slave node when executing jobs.
   
6. **Launch Method**:  
   Choose **Launch agents via SSH**. This tells Jenkins how to connect to the slave machine.
   - **Host**: Enter the private IP address of the slave machine..
   - **Credentials**: Select the `maven_slave` credentials that you previously added.
   - **Host Key Verification Strategy**: Select `Non verifying Verification Strategy`. This setting tells Jenkins not to verify the SSH host key, which is useful when connecting for the first time.
   
7. **Availability**:  
   Set the availability option to **Keep this agent online as much as possible**. This ensures that the slave node stays online and ready for job execution as much as possible.

8. **Save the Configuration**  
   Once all the information is filled in, click **Save** to add the slave node.

![Screenshot 2025-03-25 002712](https://github.com/user-attachments/assets/c304e0f4-6b09-48ac-bd0d-b8db8bb69672)

## Verifying the Connection and Jenkins Directory

After you have added the slave node in Jenkins, you can verify that the connection is successful and the node is active.

1. **Check the Connection on the Maven Node**  
   Go to the **Maven node** in Jenkins and verify that the status shows it is connected. This indicates that Jenkins is successfully communicating with the slave node.
![Screenshot 2025-03-25 005047](https://github.com/user-attachments/assets/92dbe916-d3f1-4872-b61d-363d416b82aa)

2. **Verify the Jenkins Directory on the Slave Server**  
   Now, go to your slave server. If you navigate to the `/home/ubuntu` directory (or the directory you specified for the remote root in the slave configuration), you should find a folder named `jenkins`. This folder is where Jenkins stores its necessary files and configurations for the slave node.

![Screenshot 2025-03-25 005148](https://github.com/user-attachments/assets/a9eee1a2-312a-485b-9012-c08791b5a22b)

# Setting Up a Jenkins Pipeline with Git Checkout on a Slave Node

Now we need to create a Jenkins pipeline that runs on the slave node and checks out a Git repository.
## Before You Begin: Important Note

Before you proceed with setting up the Jenkins pipeline, there are a few key things you should ensure:

### Replace the Example GitHub Repository URL

 you'll need to replace the example GitHub repository with your own repository URL that contains:

A pom.xml file (for Maven projects)

A src/ folder (with your source code)

Make sure your repository:

Is publicly accessible 

Contains the proper Maven project structure

## Create a New Jenkins Pipeline

### Steps to Create the Pipeline:

1. **Navigate to Jenkins Dashboard**
   
2. **Click New Item** from the left-hand menu.

3. **Configure the Pipeline Job**

   - Enter a name .
   - Select **Pipeline** as the job type.
   - Click **OK**.

4. **Define the Pipeline Script**

   Scroll to the **Pipeline** section.

   Paste the following script (ensure the label matches your slave node's label):

   ```groovy
   pipeline {
       agent {
           node {
               label 'Maven'  // Must match your slave node's label
           }
       }
       stages {
           stage('Git Checkout') {
               steps {
                   git branch: 'main', 
                   url: 'YOUR-GITHUB-REPO-URL-HERE'  // ← REPLACE THIS with your repo
               }
           }
       }
   }



