🏷️ AWS VPC Project 3 – Public & Private Subnet + Bastion Host + Nginx
📌 Project Overview

In this project I built a simple but realistic AWS architecture using a custom VPC with public and private subnets.

The goal was to understand how networking works in AWS and how to securely connect a private server using a public one (bastion host).

🛠️ Steps I Followed
1) Creating the VPC

First of all, I created a VPC, which is basically my own private network in AWS.

Name: mohib-vpc
CIDR block: 10.0.0.0/16

I chose /16 because it gives a large IP range. Even if this is just a project, it’s a good practice to start with a bigger range to avoid running out of IPs in the future and to keep scalability.

2) Creating Subnets

Then I created two subnets:

Public subnet → 10.0.1.0/24
Private subnet → 10.0.2.0/24

Each subnet is inside one Availability Zone.

Why:

I used 2 subnets to separate resources → better security
/24 gives 256 IPs → more than enough for a subnet
At this stage, the public subnet still cannot access the internet because we don’t have routing yet
3) Internet Gateway

Then I created an Internet Gateway (mohib-igw) and attached it to my VPC.

This is basically the “door” that allows communication between my network and the internet.

But at this point, it still doesn’t work because we need a route table.

4) Route Table Configuration

I created a route table and associated it with the public subnet.

Then I added:

10.0.0.0/16 → local (internal communication inside VPC)
0.0.0.0/0 → Internet Gateway (internet access)

Now the public subnet can finally access the internet.

The private subnet is NOT associated because it should stay isolated (for now). If needed, we could use a NAT Gateway.

5) Creating EC2 Public Instance

Then I launched an EC2 instance in the public subnet.

Important configurations:

Auto-assign public IP → enabled
Security Group:
SSH (22) → for access from my computer
HTTP (80) → to access the website

This instance will act as both:

Web server (Nginx)
Bastion host
6) Installing Nginx and Deploying Website

I connected to the EC2 instance and:

Updated the system
Installed Nginx
Started and enabled the service

Then I opened the public IP in the browser and saw the Nginx default page → this confirmed everything was working.

After that, I edited the index.html file inside:

/usr/share/nginx/html

I added my own HTML code and refreshed the browser → my custom page was live.

7) Creating Private EC2 + Troubleshooting (Real Experience)

Then I created a second EC2 instance inside the private subnet (no public IP).

The goal was:

👉 Access it ONLY through the public EC2 (bastion host)

At this point I spent a lot of time troubleshooting because SSH was not working.

After debugging step by step, I found the real issue:

👉 The Security Group of the private EC2 was misconfigured

Initially, I allowed SSH from anywhere, but that didn’t work properly in this architecture.

The correct solution was:

Private EC2 Security Group:
Allow SSH (22) ONLY from the Security Group of the public EC2

This means:

👉 Only the public EC2 (bastion) can connect to the private one

After fixing this, the connection finally worked:

ssh -i key.pem ec2-user@10.0.2.xxx

This was a very important part because I learned how real troubleshooting works in AWS.

🧠 Architecture
Internet
   ↓
[ EC2 Public (Nginx + Bastion Host) ]
   ↓
[ EC2 Private ]
🌐 Live Demo
http://13.61.18.59

## 📸 Screenshots

### VPC
![VPC](screenshots/1-vpc.png)

### Subnets
![Subnets](screenshots/2-subnets.png)

### Internet Gateway
![Internet Gateway](screenshots/3-internet-gateway.png)

### Route Table
![Route Table](screenshots/4-route-table.png)

### Route Association
![Route Association](screenshots/5-route-association.png)

### EC2 Public
![EC2 Public](screenshots/6-ec2-public.png)

### Nginx Status
![Nginx Status](screenshots/7-nginx-status.png)

### Nginx Default Page
![Nginx Default](screenshots/8-nginx-default.png)

### Live Website
![Live Website](screenshots/9-nginx-live.png)

### EC2 Private
![EC2 Private](screenshots/10-ec2-private.png)

### Bastion Access
![Bastion Access](screenshots/11-bastion-success.png)

🎯 What I Learned
How to create a VPC from scratch
Difference between public and private subnets
How Internet Gateway works
How Route Tables control traffic
How to deploy a web server with EC2 + Nginx
How Security Groups control access
How to connect to a private EC2 using a bastion host
How to troubleshoot real AWS networking issues
