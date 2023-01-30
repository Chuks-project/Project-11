## ANSIBLE CONFIGURATION MANAGEMENT

This Project will make use of DevOps tools such as Ansible by making most of the routine tasks automated with Ansible Configuration Management, at the same time you will become confident at writing code using declarative language such as YAML.


### Ansible Client as a Jump Server (Bastion Host)

A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface.
In this project, we will develop Ansible scripts to simulate the use of a Jump box/Bastion host to access our Web Servers.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![Bastion host-Jump server](https://user-images.githubusercontent.com/65022146/215438779-4fe9718a-dfb5-4c87-824b-87a4b04a0804.png)




### INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

- Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
- In your GitHub account create a new repository and name it ansible-config-mgt.
- Instal Ansible

                               
     ` sudo apt update`
     
     `sudo apt install ansible `
     
- Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.

- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

- Configure Webhook in GitHub and set webhook to trigger ansible build.

- Configure a Post-build job to save all (**) files, like you did it in Project 9.

- Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

  `
     ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
  `


### Step 2 – Prepare your development environment using Visual Studio Code

First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC), you can get it here.

- After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

- Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

   `
    git clone <ansible-config-mgt repo link>
   `    
   
  
  ### BEGIN ANSIBLE DEVELOPMENT
  
- In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.
- Checkout the newly created feature branch to your local machine and start building your code and directory structure
- Create a directory and name it playbooks – it will be used to store all your playbook files.
- Create a directory and name it inventory – it will be used to keep your hosts organised.
- Within the playbooks folder, create your first playbook, and name it common.yml
- Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging,     uat, and prod respectively.
