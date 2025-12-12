
Step 1: Create VPCs in Two Regions
=
1.	Go to VPC Dashboard.
   
  1.	Create VPC-1 in Region A (e.g., ap-south-1 – Mumbai).
   
             o	CIDR block: 10.0.0.0/16
             o	Create 2 public subnets in different AZs.

  2.	Create VPC-2 in Region B (e.g., us-east-1 – N. Virginia).
   
            o	CIDR block: 10.0.0.0/16
            o	Create 2 public subnets in different AZs.

________________________________________

⚙️ Step 2: Launch EC2 Instances & Auto Scaling
=
1.	Go to EC2 Dashboard in Region A.

2.	Create a Launch Template with:

  	        o	Amazon Linux 2 AMI
            o	Instance type: t2.micro 
            o	Security group: allow HTTP (80), HTTPS (443), SSH (22).
            o	User Data (optional for web app):
            o	#!/bin/bash
            o	yum install -y httpd
                               echo "Welcome to Mumbai Regions" > /var/www/html/index.html
            o	systemctl start httpd --now

3.	Create an Auto Scaling Group (ASG) using this launch template.
   
            o	Attach ASG to 2 subnets in Region A.
            o	Min size = 1, Desired = 2, Max = 4.
  	
4.	Repeat steps in Region B, but update User Data:
                              echo "Welcome to Virginia Region" > /var/www/html/index.html
________________________________________

🌐 Step 3: Setup Target Group and Load Balancers
=
1.	In Region A, create an Target Group.

              select EC2 instances that you want to target.

2.	In Region A, create an Application Load Balancer (ALB).

            o	Attach ALB to the 2 subnets in different AZs.
            o	Target Group → attach the ASG.
            o	Listener → HTTP (80) forward to Target Group.
  	
3.	Repeat the same steps in Region B.
   __________________________

🌍 Step 4: Configure Route 53 for Failover
=

1.	Go to Route 53 → Hosted Zones.

2.	Create a new hosted zone (e.g., myhaapp.com).

3.	Add 2 A-records with failover policy:

            o	Record 1 (Primary) → Alias → ALB DNS name (Region A).
            o	Record 2 (Secondary) → Alias → ALB DNS name (Region B).
            o	Set health check for Region A load balancer.

4.	Test DNS:

            o	If Region A is UP → traffic goes to Region A.
            o	If Region A is DOWN → Route 53 sends traffic to Region B.

________________________________________

🔍 Step 5: Testing Disaster Recovery
=
•	Stop all EC2 instances in Region A → Route 53 will failover to Region B.

•	Restart Region A instances → traffic will return to primary region.

________________________________________

✅ Final Architecture
=
•	2 VPCs across 2 AWS regions.

•	Auto Scaling Groups with 2 EC2 instances each.

•	Load Balancer per region.

•	Route 53 DNS Failover between regions.
