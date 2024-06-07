# 🚀 Node.js Project Deployment on AWS EC2 🛠️

This guide, easy to follow for anyone, demonstrates how to deploy your Node.js app on Amazon's cloud (AWS) using an EC2 machine. It will assist you in setting things up and getting your app running!

Steps to deploy a Node.js app using PM2, NGINX as a reverse proxy, and an SSL from Let's Encrypt

# Step 1: Set Up an AWS EC2 Instance

# 1. Create Free AWS Account or Login

Log in to your AWS account at https://aws.amazon.com/, using either your root account credentials or IAM user credentials.

# 2. Navigate to EC2 Dashboard

From the AWS Management Console, navigate to the EC2 Dashboard.

# 3. Launch an Instance:

- Click on the "Launch Instance" button.
- Choose an Amazon Machine Image (AMI) that best suits your project requirements, considering the operating system and software prerequisites. such as Amazon Linux or Ubuntu.
- Select an instance type based on your project's CPU, memory, and storage needs. For example, you can choose the Ubuntu 64-bit operating system and the t2.micro instance type, which is eligible for the AWS Free Tier.
- Proceed with the default configuration or customize instance details as necessary.
- Review and configure additional settings such as storage, tags, and security groups.

# Creating and Downloading Your PEM File:

- Navigate to the "Key Pairs" section within the EC2 Dashboard. Create a new key pair or select an existing one, and then securely download the corresponding PEM file. This PEM file will be required for SSH access to your EC2 instance.

- You can use a key pair to securely connect to your instance. Ensure that you have access to the selected key pair before launching the instance.

# Configuring Network Settings and Permissions:

- Navigate to the "Network settings" and "Create security group" section within the EC2 Dashboard. Ensure that the security group associated with your instance allows SSH traffic (port 22) from your IP address, as well as HTTP (port 80) and HTTPS (port 443) traffic from the internet.

# Launching the Instance:

- After configuring the necessary settings, click "Launch" to initiate the instance launch process. Monitor the progress and wait for the instance to reach the "running" state. Once the instance is running, note down its instance ID and public IP address for future reference
