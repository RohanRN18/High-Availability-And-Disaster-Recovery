
🏗️ Step 1: Create VPCs in Two Regions
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


Mumbai Region
<img width="940" height="417" alt="image" src="https://github.com/user-attachments/assets/682d47fd-f77e-4d4e-851c-75dd65217f6e" />

<img width="940" height="433" alt="image" src="https://github.com/user-attachments/assets/d1517e05-a0b6-45c6-8404-53447dc0b815" />
Virginia Region
<img width="940" height="411" alt="image" src="https://github.com/user-attachments/assets/0f44323b-9ad8-4018-af41-284e9b74e73a" />

<img width="940" height="435" alt="image" src="https://github.com/user-attachments/assets/34473966-473b-4a77-8b5d-9d2190606478" />

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
   
Mumbai Region
<img width="940" height="432" alt="image" src="https://github.com/user-attachments/assets/6a54d915-fceb-4a2b-8aab-92fc039c1ab7" />
<img width="940" height="426" alt="image" src="https://github.com/user-attachments/assets/7c0a527c-b795-457d-9f87-0b148c15018d" />

Virginia Region
<img width="940" height="429" alt="image" src="https://github.com/user-attachments/assets/4f571bd9-290e-43a3-b60d-2ec85d7cc5d0" />
<img width="940" height="439" alt="image" src="https://github.com/user-attachments/assets/d5e0fa53-2418-4f99-ae0b-254ae91bcda5" />
________________________________________

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
<img width="940" height="425" alt="image" src="https://github.com/user-attachments/assets/23181bac-f1be-4b35-8e76-e506a01614a0" />

Mumbai region
<img width="940" height="431" alt="image" src="https://github.com/user-attachments/assets/340ca53d-d1e3-48fb-9a95-6864c0cb647c" />

<img width="940" height="407" alt="image" src="https://github.com/user-attachments/assets/44aee759-2ae2-42ce-874c-d8e8893000ca" />

Virginia Region
<img width="940" height="420" alt="image" src="https://github.com/user-attachments/assets/3dedd7e6-80df-42cf-882d-f62376518438" />

<img width="940" height="421" alt="image" src="https://github.com/user-attachments/assets/a22da82d-d5bb-4d00-af21-e2add9a32639" />
________________________________________

🔍 Step 5: Testing Disaster Recovery
=
•	Stop all EC2 instances in Region A → Route 53 will failover to Region B.

•	Restart Region A instances → traffic will return to primary region.

Mumbai Region
<img width="940" height="343" alt="image" src="https://github.com/user-attachments/assets/ad31e783-ff8d-457a-a77c-ca86b663b224" />

Mumbai region stopped getting an error 
<img width="940" height="415" alt="image" src="https://github.com/user-attachments/assets/0545bc5f-3ada-4072-af8e-c72a6c96906b" />

Virginia Region
<img width="940" height="236" alt="image" src="https://github.com/user-attachments/assets/c66d86a5-dd16-4421-a0bd-675209297ad7" />

________________________________________

✅ Final Architecture
=
•	2 VPCs across 2 AWS regions.

•	Auto Scaling Groups with 2 EC2 instances each.

•	Load Balancer per region.

•	Route 53 DNS Failover between regions.
