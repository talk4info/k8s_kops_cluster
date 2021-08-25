# k8s_kops_cluster using Jenkins

**Below are the steps to create CICD pipeline using KOPS and Jenkins on AWS cloud provider**
===========================================================================================

1. Create a New job in Jenkins and provide the github location where your kops_cluster.yaml located along with password if not set already
2. update the branch as master or main based on your git repo
3. check the Poll SCM option along with schedule to enable git polling
4. Choose post build action execute shell command 
5. saved the below details and choose your cluster name and replace KOPS_STATE_STORE=my-cluster.test.com
---------------------
 export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
 export AWS_SECRET_ACCESS_ID=$(aws configure get aws_secret_access_id)
 export KOPS_STATE_STORE=my-cluster.test.com
 curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
 chmod +x ./kubectl
 sudo mv ./kubectl /usr/local/bin/kubectl
 curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
 chmod +x kops-linux-amd64
 sudo mv kops-linux-amd64 /usr/local/bin/kops
 kops version
 pwd
 sed -i "s/KOPS_STATE_STORE/$KOPS_STATE_STORE/g" kops_cluster.yaml 
 ls -al kops_cluster.yaml
 aws s3 mb s3://$KOPS_STATE_STORE
 kops create -f kops_cluster.yaml 
 ls -al kops_cluster.yaml
 rm -rf ~/.ssh/id_rsa*
 ssh-keygen -b 2048 -t rsa -f ~/.ssh/id_rsa
 kops create secret --name $KOPS_STATE_STORE sshpublickey admin -i ~/.ssh/id_rsa.pub
 
 kops update cluster $KOPS_STATE_STORE --yes
 
 kops validate cluster --wait 2m
 kubectl get nodes
--------------------------------------------------------------------------

6. save the job 
7. We are ready for our first kubernetes build , you can validate your kops_cluster.yaml file for any resource level changes like vpc , subnet, master, nodes details and its count and storage size , linux image type and finally the clustername which needs to be updated.

8. NOTE : if you are using existing kops_cluster.yaml configuration and you are still in Free tier subscription from AWS then you would be charged for route53 (may be 1 GBP for a day).

9 . Lets run the job to create our Kubernetes cluster on AWS in less than 5mins 
10. Login to AWS console and validate all details are as per requirements
11. ssh to Master box from jenkins box to list all nodes and cluster details.







**UPDATE Kubernets Cluster when there is a change in kops_cluster.yaml  settings**
========================================================================================
1. Create a new jenkins job 
2. update the git location 
3. select branch as master/main as per your brandh settings
4. select post build action as excute the shell and save below details , Here select the cluster details and replace it with your KOPS_STATE_STORE=my-cluster.test.com

-------------------------------------------
export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
export AWS_SECRET_ACCESS_ID=$(aws configure get aws_secret_access_id)
export KOPS_STATE_STORE=my-cluster.test.com
aws s3 mb s3://$KOPS_STATE_STORE
sed -i "s/KOPS_STATE_STORE/$KOPS_STATE_STORE/g" kops_cluster.yaml 
kops replace -f kops_cluster.yaml --force
kops update cluster $KOPS_STATE_STORE --yes
kops rolling-update cluster $KOPS_STATE_STORE --yes
kops rolling-update cluster $KOPS_STATE_STORE --cloudonly --yes
-----------------------------------------

5. save the jobs and its ready for build.
6. After successfully execution you would see new kubernetes details are being reflected in AWS console , here we can change autoscalling group master and nodes count and also other setting to instantly reflect ( hardly takes 5mins ) in the console




**DELETE Kubernetes Cluster**
================================
1. Create a Job along with git location
2. select post build action and select execute shell and save the below values
-----------------------------------------
export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
export AWS_SECRET_ACCESS_ID=$(aws configure get aws_secret_access_id)
export KOPS_STATE_STORE=my-cluster.test.com
aws s3 mb s3://$KOPS_STATE_STORE
kops delete cluster $KOPS_STATE_STORE --yes
-----------------------------------------------------

3. replace the KOPS_STATE_STORE=my-cluster.test.com with your cluster name 
4. save and ready for build
5. check the right cluster set in the job , once you are sure run the job to delete kubernets from AWS 
6. Login to AWS console and within 2mins you would see all the kubernets resources been deleted ...





