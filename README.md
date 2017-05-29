Prerequisites
=============
1) Set up and AWS account.
2) Set up Dockerhub account to upload and share docker images between swarm nodes.
3) Create an AWS user or role with sufficient permissions to create the following:         
       * VPC     
       * Security groups    
       * EC2 instances    
       * EFS    
4) Set up the Ansible server along with the dependencies mentioned above. To automatically do this end user can use the EC2-userdata script found here : ./Code/crossover/ec2-userdata/userdata.sh      
5) Use the access and secret keys on the Ansible server per these instructions : http://docs.aws.amazon.com/aws-sdk-php/v2/guide/credentials.html#credential-profiles).     
6) An S3 private bucket is required to place confidential files like :       
       * Ansible custom configuration     
       * PEM file that allows access to new instances created for the stack.     
       * Credentials file that stores access and secret access key.     
7) Implementing all these steps above will be described in detail in the Deployment document.     


Ansible Support
================
Playbooks have been tested on the following Ansible versions.      

    * [Ansible 2.2.2.0](http://releases.ansible.com/ansible/ansible-2.2.2.0.tar.gz)      
    * [Ansible 2.2.1.0](http://releases.ansible.com/ansible/ansible-2.2.1.0.tar.gz)       


Deliverables
=============
The following are what you can expect to find out-of-the-box in the zipped package.     

1) EC2 Userdata file to configure Ansible server on ec2 instance.      
2) Ansible playbook(s) that do the following :     
       * Create a VPC      
       * Create 2 subnets (in separate zones) within the VPC.     
       * Create 2 security groups. One for the swarm nodes and one for the Mysql container.      
       * The security group that hosts the mysql container allows incoming requests on port 3306 only from the security group hosting the swarm nodes.     
       * Create an AWS EFS file system drive to share persistent data between nodes.     
       * Create 1 EC2 instance in the subnet designated for Mysql.       
       * Configure and launch the Mysql container.       
       * Create 2 EC2 instances in the subnet designated for the Docker swarm.        
       * Configure Docker swarm on 2 EC2 instances.        
       * Mount EFS on the swarm nodes for common upload/download location between nodes and containers.       
       * Modify Application settings to point to the Mysql container.        
       * Creates a docker image of the Application and pushes it to a dockerhub account.        
       * Launch Service (running the given application) with 4 replicas on the swarm.        
       * Allows user to switch between "production" and "development" environments.          
         to change default behaviour of the App. (explained in next section).          
       * The group vars file ./Code/crossover/group_vars/all contains several variables that allows end user to customize the  deployment. Adequate notes have been provided within this file.      


Development/Production environment
==================================    
* In the variables file on line 7 user can opt for "development" or "production" settings.       
* If "development" is used, the application image is created as is.              
* This default behaviour causes the database to be recreated everytime a new container hosting the application comes up.      
* I attempted to change the setting {spring.jpa.hibernate.ddl-auto=create} to {spring.jpa.hibernate.ddl-auto=update}. While this avoids creating the schema again, it still deletes/creates data again.          
* If "production" is used, then Ansible first imports the schema and data provided within the application (./Code/crossover/roles/servicestarter/files/docker/app/Code/src/main/resources/*.sql)        
* It next removes the {pring.jpa.hibernate.ddl-auto} parameter from the "application.properties" file.            
* The production environment image, does not recreate schemas/data and therefore no matter how many
  times containers are destroyed or created, mysql data is not affected.          
