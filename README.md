# vpc-demo-2-tier-app!
[vpc-demo-2-tier-app](https://github.com/itsmepayal/vpc-demo-2-tier-app/assets/153077886/19288e12-14a7-4712-9a88-8cf1274600ac)

This example demonstrates the creation of a VPC that can be used for servers in a production environment.

To enhance, servers are deployed in two Availability Zones using an auto scaling group and an application load balancer. For additional security, servers are placed in private subnets.

The load balancer handles incoming requests for the servers, while the servers themselves can connect to the internet via a NAT gateway. To ensure high availability, the NAT gateway is deployed in both availability zones.

Overview:

-The VPC has public subnets and private subnets in two Availability zones.
-Each public subnet includes a NAT gateway and a load balancer node.
-Servers are launched and terminated within the private subnets using an auto scaling group.
-They receive traffic from the load balancer.
-Servers can access the internet by through the NAT gateway.

Components:

-Auto scaling group
-Load Balancer
-Target group
-Bastion Host

Now, let’s proceed with the project implementation:

1. Login to the AWS account and navigate to the Dashboard.
2. Go to VPC dashboard.
3. Step-1: Create VPC
      Select VPC or click on “VPC and More”.
      Click on Create VPC
4.Verify that the VPC was successfully created.

Step-2: Auto scaling group

-Navigate to AWS Auto Scaling Group.
-Before creating the auto scaling group, create a launch template with the required configuration.
-Launch template using required configuration
-This is exactly same which we created as a EC2 instances but these is required for auto scaling group.
-Select the AMI and type.
-In network settings, choose the VPC and create a security group and key pair for SSH access.

Important Points:

-We have to follow the security standards here and create a security group to deploy the application.
-Make sure to add the required port usually, you will use port 22 to access
-Add one more SG to access the application inside the instances.
-We can change the port range according to the application we deploy.
-Create a launch template.
-Go back to the auto scaling group and create it, selecting the previously created launch template.
-Choose the VPC and AZ for private subnets.
-Specify the size of the auto scaling group, choosing the desired capacity, minimum two and maximum four desired capacity.
    Minimum — 2
    Maximum — 4

-Once the auto scaling group is created, navigate to the EC2 instances and check if there are two instances created, one in ap-south-1a and one in ap-south-1b.
-The configuration is now complete. Before creating an application load balancer, we need to install the applications inside the servers.
-There is no public IP address generated here.

Step-3:

-Now create an instance called “bastion-host” which will act as a private subnet and a public subnet.
-Open the EC2 instance console. We can see three instances: two in the private subnet and one in the bastion-host.
-Open the Windows terminal and run an SCP command to copy the .pem file from the local system and paste it in the bastion-host instance.

scp -i c:\users\payal\downloads/aws-prod-key.pem /c:/users/payal/downloads/aws-prod-key.pem ubuntu@54.159.129.108/home/ubuntu

-SSH to bastion-host instance using the .pem file and the command prompt.

ssh -i aws-prod-key.pem ubuntu@54.159.129.108

Now SSH with the private IP address. We can see that we are able to login to a specific instance with the private IP address.

Please make sure it is being run as the root user, as the root user has special permissions.

sudo su
sudo apt update

-Create an HTML file in the vim text editor and save it.
-My application is running on one instance by port 8000.
    
python3 -m http.server 8000

we have used the auto scaling group, created an EC2 instance, and created the bastion-host.

-We have logged into only one of the instances. When using a load balancer, we need to demonstrate that traffic is going to a specific instance and getting a response. However, when we try to access another application in a different subnet, we get an error response because either the page or the application is not available. That’s why we install the Python application in one instance and not in the other.This is how we explain the concept of load balancing.

Now we can see that 50% of the traffic will go to one instance and receive a response, while the other 50% should go to another instance and not receive any traffic. Now create a load balancer and attach these instances as target groups, which is the final stage.
Navigate to the load balancer.

The load balancer should be in the public subnet and have direct access from the internet gateway.

Select the VPC that we created earlier

The subnets should be public, and the availability zones should also be public. If we try to keep them private, AWS will give an error.
The load balancer should allow SSH traffic and port 8000 traffic.

Before creating the application load balancer, we need to create a target group to specify which EC2 instances we want to access.
Specify the target group to use HTTP protocol on port 80, and then select the VPC.

-Select the instances to include in the target groups.
-Now we have target groups verifying on port 8000.
-We added a target group here using HTTP on port 80.

Once the load balancer is created, check the security groups to ensure that port 8000 is open and port 80 is added during load balancer creation. When it is done, copy the DNS name as the URL (aws-prod-project-225233959.us-east-1.elb.amazonaws.com) and run it in the browser.Finally, we have implemented the load balancer successfully.
