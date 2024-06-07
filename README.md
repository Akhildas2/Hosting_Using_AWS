# 🚀 Node.js Project Deployment on AWS EC2 🛠️

This guide, easy to follow for anyone, demonstrates how to deploy your Node.js app on Amazon's cloud (AWS) using an EC2 machine. It will assist you in setting things up and getting your app running!

Steps to deploy a Node.js app using PM2, NGINX as a reverse proxy, and an SSL from Let's Encrypt.

## Step 1: Set Up an AWS EC2 Instance

### 1. Create Free AWS Account or Login
Log in to your AWS account at [https://aws.amazon.com/](https://aws.amazon.com/), using either your root account credentials or IAM user credentials.

### 2. Navigate to EC2 Dashboard
From the AWS Management Console, navigate to the EC2 Dashboard.

### 3. Launch an Instance
- Click on the **"Launch Instance"** button.
- Choose an Amazon Machine Image (AMI) that best suits your project requirements, considering the operating system and software prerequisites, such as Amazon Linux or Ubuntu.
- Select an instance type based on your project's CPU, memory, and storage needs. For example, you can choose the Ubuntu 64-bit operating system and the t2.micro instance type, which is eligible for the AWS Free Tier.
- Proceed with the default configuration or customize instance details as necessary.
- Review and configure additional settings such as storage, tags, and security groups.

### Creating and Downloading Your PEM File
- Navigate to the **"Key Pairs"** section within the EC2 Dashboard. Create a new key pair or select an existing one, and then securely download the corresponding PEM file. This PEM file will be required for SSH access to your EC2 instance.
- You can use a key pair to securely connect to your instance. Ensure that you have access to the selected key pair before launching the instance.

### Configuring Network Settings and Permissions
- Navigate to the **"Network settings"** and **"Create security group"** section within the EC2 Dashboard. Ensure that the security group associated with your instance allows SSH traffic (port 22) from your IP address, as well as HTTP (port 80) and HTTPS (port 443) traffic from the internet.

### Launching the Instance
- After configuring the necessary settings, click **"Launch"** to initiate the instance launch process. Monitor the progress and wait for the instance to reach the "running" state. Once the instance is running, note down its instance ID and public IP address for future reference.


## Step 2: Connect to Your EC2 Instance

1. **Navigate to EC2 Dashboard**
   - Go to the AWS Management Console.
   - Navigate to the EC2 Dashboard.

2. **Select Your EC2 Instance**
   - Click on "Instances" in the EC2 Dashboard.
   - Choose the instance you want to connect to by selecting the checkbox next to it.

3. **Access the Connect Tab (EC2 Instance Connect)**
   - In the lower panel, click on the "Connect" button located at the top of the page.
   - A new window will appear with connection options.

4. **Connect Using EC2 Instance Connect**
   - Select the "EC2 Instance Connect" option if available.
   - Click the "Connect" button in the pop-up window to open an SSH session directly in your browser.

   Alternatively, you can connect using an SSH client:

5. **Connect Using SSH Client**
   - Open an SSH client (such as Terminal on macOS/Linux or PowerShell on Windows).
   - Locate your private key file. The key used to launch this instance is `<your-key-pair>.pem`.
   - Run this command, if necessary, to ensure your key is not publicly viewable:
     ```sh
     chmod 400 "<your-key-pair>.pem"
     ```
   - Connect to your instance using its Public DNS. Replace `<your-instance-public-dns>` with your instance's actual Public DNS:
     ```sh
     ssh -i "<your-key-pair>.pem" ubuntu@<your-instance-public-dns>
     ```

   Example:
   ```sh
   ssh -i "example.pem" ubuntu@ec2-12-34-56-78.compute-1.amazonaws.com
