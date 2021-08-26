# k8s_kops_cluster using Jenkins

Pre-requisites: 
1. Jenkins server
2. All required plugins including extensible and deploy plugins 
3. Code repository access and details
4. AWS cloud access key and secret
5. An IAM role with full access to EC2, Route53, Loadbalancer, s3 bukets.
6. A cluster DNS name 
7. A hosted zone details
8. VPC and subnet details


**Below are the steps to create CICD pipeline using KOPS and Jenkins on AWS cloud provider**
===========================================================================================

1. Create a New freestyle job in Jenkins
2. select this "This project is parameterised " and update below values along with extensible text
   choice parameter : 
   Name : choice
   Value:
   create
   update
   delete
   
   next select Extensible choice
   Name : value
   select check box : Editable
   
4. provide the github location where your kops_cluster.yaml located along with password if not set already
5. update the branch as master or main based on your git repo
6. check the Poll SCM option along with schedule to enable git polling
7. Choose post build action execute shell command 
8. Copy the commands from Jenkins_script file and save it 
9. We are ready for our first kubernetes build , you can validate your kops_cluster.yaml file for any resource level changes like vpc , subnet, master, nodes details and its count and storage size , linux image type and finally the clustername which needs to be updated.

10. NOTE : if you are using existing kops_cluster.yaml configuration and you are still in Free tier subscription from AWS then you would be charged for route53 (may be 1 GBP for a day).

11 . Lets run the job to create our Kubernetes cluster on AWS in less than 5mins 
 select build with Parameter 
 choice : create
 value : " Input your cluster Name "
 finally execute the job to create a infrastructure 
12. Login to AWS console and validate all details are as per requirements
13. ssh to Master box from jenkins box to list all nodes and cluster details.



**UPDATE Kubernets Cluster when there is a change in kops_cluster.yaml  settings**
========================================================================================
Lets run the job to update our Kubernetes cluster on AWS in less than 5mins 
 select build with Parameter 
 choice : update
 value : " Input your cluster Name "
 finally execute the job to update kubernetes infrastructure 
 Login to AWS console and validate all details are as per requirements
ssh to Master box from jenkins box to list all nodes and cluster details.




**DELETE Kubernetes Cluster**
================================
 Lets run the job to delete our Kubernetes cluster on AWS
 select build with Parameter 
 choice : delete
 value : " Input your cluster Name "
 finally execute the job to delete infrastructure 
Login to AWS console and within 2mins you would see all the kubernets resources been deleted ...





