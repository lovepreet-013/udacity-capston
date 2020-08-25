# Create an IAM User with Admin Permissions
1.	Navigate to IAM > Users.
2.	Click Add user.
3.	Set the following values:
a.	User name: k8-admin
b.	Access type: Programmatic access
4.	Click Next: Permissions.
5.	Select Attach existing policies directly.
6.	Select Administrator Access.
7.	Click Next: Tags > Next: Review.
8.	Click Create user.
9.	Copy the access key ID and secret access key, and paste them into a text file, as we'll need them in the next step.
# LAUNCH AND CONFIGURE ADMIN MACHINE
1.	Navigate to EC2 > Instances.
2.	Click Launch Instance.
3.	On the AMI page, select the Amazon Linux 2 AMI.
4.	Leave t2.micro selected, and click Next: Configure Instance Details.
5.	On the Configure Instance Details page:
a.	Network: Leave default
b.	Subnet: Leave default
c.	Auto-assign Public IP: Enable
6.	Click Next: Add Storage > Next: Add Tags > Next: Configure Security Group.
7.	Click Review and Launch, and then Launch.
8.	In the key pair dialog, select Create a new key pair.
9.	Give it a Key pair name of "mynvkp".
10.	 Click Download Key Pair, and then Launch Instances.
11.	Click View Instances, and give it a few minutes to enter the running state.
12.	Once the instance is fully created, check the checkbox next to it and click Connect at the top of the window.
13.	In the Connect to your instance dialog, select EC2 Instance Connect (browser-based SSH connection).
14.	Click Connect.
15.	In the command line window, check the AWS CLI version:
a.	aws --version
b.	It should be an older version.

16.	Download v2:
a.	curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
17.	Unzip the file:
a.	unzip awscliv2.zip
18.	See where the current AWS CLI is installed:
a.	which aws
b.	It should be /usr/bin/aws.
19.	Update it:
a.	sudo ./aws/install --bin-dir /usr/bin --install-dir /usr/bin/aws-cli --update
20.	Check the version of AWS CLI:
a.	aws --version
b.	It should now be updated.
21.	Configure the CLI:
a.	aws configure
22.	For AWS Access Key ID, paste in the access key ID you copied earlier.
23.	For AWS Secret Access Key, paste in the secret access key you copied earlier.
24.	For Default region name, enter us-east-1.
25.	For Default output format, enter json.
26.	Download kubectl:
a.	curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/kubectl
27.	Apply execute permissions to the binary:
a.	chmod +x ./kubectl
28.	Copy the binary to a directory in your path:
a.	mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
29.	Ensure kubectl is installed:
a.	kubectl version --short --client
30.	Download eksctl:
a.	curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
31.	Move the extracted binary to /usr/bin:
a.	sudo mv /tmp/eksctl /usr/local/bin
32.	Get the version of eksctl:
a.	eksctl version
33.	See the options with eksctl:
a.	eksctl help

# Provision an EKS Cluster
Provisioning an EKS Cluster
1.	Provision an EKS cluster with three worker nodes in us-east-1:

eksctl create cluster --name dev --version 1.16 --region us-east-1 --nodegroup-name standard-workers --node-type t3.micro --nodes 3 --nodes-min 1 --nodes-max 4 –managed --asg-access

It will take 10–15 minutes since it's provisioning the control plane and worker nodes, attaching the worker nodes to the control plane, and creating the VPC, security group, and Auto Scaling group.

1.	In the AWS Management Console, navigate to CloudFormation and take a look at what’s going on there.

2.	Select the eksctl-dev-cluster stack (this is our control plane).

3.	Click Events, so you can see all the resources that are being created.

4.	We should then see another new stack being created — this one is our node group.

5.	Once both stacks are complete, navigate to Elastic Kubernetes Service > Clusters.

6.	Click the listed cluster.

7.	Click the Compute tab, and then click the listed node group. There, we'll see the Kubernetes version, instance type, status, etc.

8.	Click dev in the breadcrumb navigation link at the top of the screen.

9.	Click the Networking tab, where we'll see the VPC, subnets, etc.

10.	Click the Logging tab, where we'll see the control plane logging info.

The control plane is abstracted — we can only interact with it using the command line utilities or the console. It’s not an EC2 instance we can log into and start running Linux commands on.

11.	Navigate to EC2 > Instances, where you should see the instances have been launched.

12.	Close out of the existing CLI window, if you still have it open.

13.	Select the original t2.micro instance, and click Connect at the top of the window.

14.	In the Connect to your instance dialog, select EC2 Instance Connect (browser-based SSH connection).

15.	Click Connect.

16.	In the CLI, check the cluster:
eksctl get cluster

17.	 Enable it to connect to our cluster:
aws eks update-kubeconfig --name dev --region us-east-1


# DEPLOY A KUBERNETES DASHBOARD USING METRIC SERVER
https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html




# DEPLOYING THE REACT APP TO KUBERNETES CLUSTER
1.	You’ll find the source code on this git repository https://github.com/denyshubh/eks-demo
2.	On you Jenkins user, you should be able to run the kubectl commands, so please verify it using 
$ kubectl version
3.	Add your AWS CRED that you created above for your Jenkins user or use WithCredentials() option in Jenkins.
4.	Create a Jenkins Multi-Branch project and add the source as git and give the git repo name.
5.	Once your pipeline is build, run the following command on the terminal to get the ALB endpoint, to access your application
 
6.	Go to the External IP to see your web application live and running.
