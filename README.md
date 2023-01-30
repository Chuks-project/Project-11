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

  
     ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
  


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

#### Step 4 – Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:


```                                                                                                                            
  eval `ssh-agent -s`
   
  ssh-add <path-to-private-key>
```  

-  Confirm the key has been added with the command below, you should see the name of your key
  
      ` ssh-add -l `
  
- Now, ssh into your Jenkins-Ansible server using ssh-agent
  
    `ssh -A ubuntu@public-ip`
  
- Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.


- Update your inventory/dev.yml file with this snippet of code:
  
   ```                                                                `
       [nfs]
       <NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

       [webservers]
       <Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
       <Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

       [db]
       <Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

      [lb]
      <Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'  

  ```  
      
      
      
CREATE A COMMON PLAYBOOK

 Step 5 – Create a Common Playbook
It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

- In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

- Update your playbooks/common.yml file with following code:
  

---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

```       


use git commands to add, commit and push your branch to GitHub.

   ```
    git status

    git add <selected files>

    git commit -m "commit message"
   
   ```   
      
   
   
- Create a Pull request (PR)

- Wear a hat of another developer for a second, and act as a reviewer.

- If the reviewer is happy with your new feature development, merge the code to the master branch.

- Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

- Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to            /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.


![build one](https://user-images.githubusercontent.com/65022146/215465215-5b8f2aef-3485-4448-8cfa-bafde9fba56c.png)

     
     
     
### RUN FIRST ANSIBLE TEST

- Now, it is time to execute ansible-playbook command and verify if your playbook actually works:
  
```    
    cd ansible-config-mgt
    
    ansible-playbook -i inventory/dev.yml playbooks/common.yml

```
    

- If the ansible palybook is successful, you will see an output like the screenshot below:
    
    ![Ansible Playbook succesful2](https://user-images.githubusercontent.com/65022146/215464819-706385e9-c64f-4d6a-a897-a7ed7c5d5576.png)



- You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

  ![which wireshark](https://user-images.githubusercontent.com/65022146/215465988-b005d6ec-4a43-4e56-a8e3-4eef2f064250.png)

     
                                                                                                                             
