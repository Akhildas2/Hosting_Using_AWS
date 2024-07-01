# 🚀 Node.js Project Deployment on AWS EC2 🛠️

This guide, easy to follow for anyone, demonstrates how to deploy your Node.js app on Amazon's cloud (AWS) using an EC2 machine. It will assist you in setting things up and getting your app running!

Steps to deploy a Node.js app using PM2, NGINX as a reverse proxy, and an SSL from Let's Encrypt.

## Step 1: Set Up an AWS EC2 Instance

### 1. Create Free AWS Account or Login

Log in to your AWS account at [https://aws.amazon.com/](https://aws.amazon.com/), using either your root account credentials or I AM user credentials.

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
   ```

## Step 3: Setting Up a Basic Firewall

To ensure the security of your EC2 instance, setting up a basic firewall is essential. Ubuntu 20.04 servers can utilize the UFW (Uncomplicated Firewall) to control incoming and outgoing network traffic. Follow these steps to configure a basic firewall:

### 1. Check Available Applications

First, check the available applications that UFW can manage by name. Applications often register their profiles with UFW upon installation.

```sh
ufw app list
```

- Output

```sh
 Available applications:
  OpenSSH
```

### 2. Allow SSH Connections

Allow SSH connections to ensure you can log back in to your instance next time.

```sh
ufw allow OpenSSH
```

### 3. Enable the Firewall

Once you have configured the firewall rules, enable UFW to start protecting your server.

```sh
ufw enable
```

Type 'y' and press ENTER to proceed. You can verify that SSH connections are still allowed by typing:

```sh
ufw status
```

- Output

```sh
Status: active
To-Actio-From
OpenSSH-ALLOW-Anywhere
OpenSSH(v6)-ALLOW-Anywhere(v6)
```

### 4. Additional Configuration

As the firewall is currently blocking all connections except for SSH, you may need to adjust the firewall settings to allow traffic for additional services you install. For example, if you configure your instance to host a web server, you will need to allow HTTP (port 80) and HTTPS (port 443) traffic.

## Step 4: Set Up Node.js on EC2 Instance

To run your Node.js applications on your EC2 instance, follow these steps to set up Node.js and NPM (Node Package Manager):

### 1.**Update the Package List**

- It’s always a good practice to update the package lists to ensure you’re installing the latest versions of software. Run the following
  command:
  ```sh
  sudo apt update
  ```

### 2. **Upgrade Existing Packages**

- Upgrade all the existing packages to their latest versions:
  ```sh
  sudo apt upgrade -y
  ```

### 3. **Install NVM (Node Version Manager)**

- NVM allows you to manage multiple versions of Node.js. Install NVM with the following command:
  ```sh
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
  ```

### 4. **Source NVM Script**

- After installation, you need to source the NVM script to add it to your shell session:
  ```sh
  source ~/.bashrc
  ```
- To verify that NVM is installed, check the installed version of NVM:
  ```sh
  nvm -v
  ```

### 5. **Install Node.js Using NVM**

- Once NVM is installed, you can use it to install Node.js. First, list the available Node.js versions:
  ```sh
  nvm ls-remote
  ```
- Choose a version to install. For example, to install Node.js version 24.04, which is the current LTS (Long-Term Support) version of Node.js, run:
  ```sh
  nvm install  24.04
  ```
- Verify the installation by checking the versions of Node.js and npm (Node Package Manager):

```sh
node -v
# and
npm -v
```

## Step 5: Clone your Node.js Project to EC2

### 1. **Clone your application from GitHub**

```sh
git clone <your-git-repository-url>
# Replace <your-git-repository-url> with the URL of your Git repository.
```

### 2. **Navigate into the cloned directory:**

```sh
cd your-project-directory
```

To list the files and directories in the current working directory, you can use:

```sh
ls
```

### 3. **Install Dependencies:**

```sh
npm install
# or
npm i
```

### 4. **Setup your .env file:**

- Use nano or vim command to create or directly start typing to modify your file.

```sh
nano .env
# or
vim .env
```

### 5. **Ensure smooth application execution:**

1. **Install PM2:**

   - PM2 is a production process manager for Node.js applications with a built-in load balancer. It allows you to keep applications alive forever, to reload them without downtime, and to facilitate common system admin tasks.

```sh
npm install pm2 -g
```

2. **Start your application with PM2:**

   - Here app.js or index.js may change to your file name.

```sh
pm2 start app.js
# or
pm2 start index.js

```

**Other useful PM2 commands:**

- To check the version of PM2:

```sh
pm2 -v
```

- To stop PM2:

```sh
pm2 stop app
# or this to stop all processes
pm2 stop all
```

- To restart PM2:

```sh
pm2 restart app
# or this to restart all processes
pm2 restart all
```

- To see logs:

```sh
pm2 log
```

- To clear logs of all processes:

```sh
pm2 flush
# To clear a certain process log, use its id. Replace <id> with the id of your process to clear its log
pm2 flush <id>

```

### 6. **Add Git functionality to streamline updates:**

**Pull latest changes from the repository:**

```sh
git pull origin master
```

## Step 6: Step-by-Step Guide to Install and Configure NGINX

1. **Install NGINX**
   Run the following command to install NGINX:

```sh
sudo apt update
# After update add this comment for install nginx
sudo apt install nginx
```

2. **Configure NGINX to Proxy Pass to your Node.js Application**

   - Edit the default NGINX configuration file:

```sh
sudo nano /etc/nginx/sites-available/default

```

Clear the contents (Ctrl + K) and add the following configuration:

```sh
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;// Please change the port to whatever port your app runs on to place 3000.
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}


```

Make sure to change the port number in proxy_pass http://localhost:3000; to match the port your Node.js application is running on.

3. **Check NGINX Configuration**
   Verify the NGINX configuration for any syntax errors:

```sh
 sudo nginx -t

```

If there are any errors, resolve them before proceeding.

4. **Restart NGINX**

Restart the NGINX service to apply the new configuration:

```sh
sudo systemctl restart nginx

```

5.  **Check NGINX Status**

To check if NGINX is running correctly, you can use:

```sh
sudo systemctl status nginx

```

Alternatively, you can reload NGINX without restarting the service:

```sh
sudo nginx -s reload

```

## Step 7: Set up DNS with Route 53 🌐

Before you begin, purchase a domain from a registrar like Hostinger or GoDaddy.

1. **Access Amazon Route 53**

   - Go to the [Amazon Route 53 console](https://console.aws.amazon.com/route53/).

2. **Create a Hosted Zone**

   - Click on "Create Hosted Zone".
   - Enter your domain name (e.g., `yourdomain.com`) and choose a region.
   - Click on "Create".

3. **Obtain Name Servers**

   - After creating the hosted zone, note down the Name Server (NS) records provided by Route 53 (there will be 4 nameservers). These are the authoritative name servers for your domain.

4. **Update Domain Registrar's Name Servers**

   - Log in to your domain registrar's website (e.g., GoDaddy, Hostinger).
   - Navigate to the DNS management or name server settings.
   - Replace the existing name servers with the ones provided by Route 53.

5. **Create Record Sets**

   - In the Route 53 dashboard, select the hosted zone you created.
   - Click on "Create Record Set".
   - For an A record (IPv4):
     - **Name**: (Leave it empty for the root domain).
     - **Type**: A
     - **Alias**: No
     - **Value**: Enter the IPv4 address of your EC2 instance or other resource.
   - For a CNAME record (Canonical Name):
     - **Name**: (Enter a subdomain like `www` if needed).
     - **Type**: CNAME
     - **Alias**: Yes
     - **Alias Target**: Enter the canonical name of your resource (e.g., `your-load-balancer-url.elb.amazonaws.com`).
   - Click on "Create" to save the record set.

6. **Verify DNS Configuration**

   - It may take some time (up to 48 hours) for DNS changes to propagate globally.

7. **Test Your Domain**

   - Open a web browser and navigate to your domain (e.g., `http://example.com`). Ensure that it resolves to the correct AWS resource.

## Step 8: Set Up SSL Certificate (HTTP to HTTPS)

1. **Install Certbot**

   ```bash
   sudo add-apt-repository ppa:certbot/certbot
   ```

   ```bash
   sudo apt-get update
   ```

   ```bash
   sudo apt-get install python3-certbot-nginx
   ```

2. **Adjust Firewall Settings**

   Ensure that your firewall allows HTTPS traffic (port 443) to your server. If using `ufw` (Uncomplicated Firewall), you can enable HTTPS traffic with:

   ```bash
   sudo ufw allow 'Nginx Full'
   ```

   Replace `Nginx Full` with your actual Nginx profile if it differs. If you previously allowed only HTTP traffic, you can remove that rule:

   ```bash
   sudo ufw delete allow 'Nginx HTTP'
   ```

3. **Obtain SSL Certificate**

   Replace `example.com` and `www.example.com` with your actual domain names. Follow the prompts to complete the certificate issuance process. Certbot will automatically update the Nginx configuration to use the obtained SSL certificate.

   ```bash
   sudo certbot --nginx -d example.com -d www.example.com
   ```

   If you have only one domain, specify only one `-d` parameter.

4. **Automatic Renewal**

   Certbot certificates are valid for 90 days. Test the renewal process to ensure it works correctly. You can do this using:

   ```bash
   sudo certbot renew --dry-run
   ```

   Ensure to set up a cron job or systemd timer for automatic renewal as recommended by Certbot.

5. **Verify SSL Configuration**

   After obtaining and installing the SSL certificate, verify that HTTPS is working correctly on your site. Access your site using `https://yourdomain.com` and ensure the connection is secure.

6. **Update Site Links**

   Update any links in your website configuration, code, or content from `http://` to `https://` to ensure all resources are loaded securely.

7. **Monitor Certificate Expiry**

   Set up monitoring to alert you when your SSL certificate is nearing expiry, so you can renew it in time.

By following these steps, you can secure your Nginx web server with HTTPS using Certbot, ensuring your firewall allows HTTPS traffic for proper functionality.

# Successfully Hosted Node.js Project on AWS

Congratulations! I have successfully hosted my Node.js project on AWS. Here are links to connect with me:


[![LinkedIn](https://img.shields.io/badge/LinkedIn-Akhil%20Das-blue?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/akhil-das-2-aki/)

[![Instagram](https://img.shields.io/badge/Instagram-akhildas___2___aki-%23bc2a8d?logo=instagram&logoColor=white)](https://www.instagram.com/akhildas___2___aki/)

Feel free to reach out and connect!

### Additional Commands

#### NGINX COMMANDS

- **Stop:** `sudo systemctl stop nginx` - Stops the Nginx service.
- **Start:** `sudo systemctl start nginx` - Starts the Nginx service.
- **Restart:** `sudo systemctl restart nginx` - Restarts the Nginx service.
- **Reload:** `sudo systemctl reload nginx` - Reloads the Nginx configuration.
- **Disable:** `sudo systemctl disable nginx` - Disables automatic startup of Nginx.
- **Enable:** `sudo systemctl enable nginx` - Enables automatic startup of Nginx.
- **Check status:** `sudo systemctl status nginx` - Checks the status of Nginx service.

#### PM2 COMMANDS

- **pm2 start:** Starts a new process.
- **pm2 list:** Lists all running processes.
- **pm2 stop:** Stops a running process.
- **pm2 restart:** Restarts a running process.
- **pm2 reload:** Reloads a running process without downtime.
- **pm2 delete:** Deletes a process from the PM2 process list.
- **pm2 logs:** Displays the logs for a running process.
- **pm2 monit:** Opens a real-time monitoring dashboard for all running processes.
- **pm2 save:** Saves the current process list to a file.
- **pm2 startup:** Configures PM2 to run as a daemon service.

Example: `pm2 start <processid>` or `pm2 start all`.

#### LINUX COMMANDS

- **ls:** Lists directories.
- **pwd:** Prints the current working directory.
- **cd:** Changes directory.
- **mkdir:** Creates directories.
- **mv:** Moves or renames files.
- **cp:** Copies files.
- **rm:** Deletes files or directories.
- **touch:** Creates blank/empty files.
- **sudo:** Executes a command as the superuser.
- **clear:** Clears the terminal.
- **wget:** Downloads files from the internet.
- **sudo apt-get update:** Updates the package lists from repositories.
- **sudo apt-get upgrade:** Installs the latest versions of installed packages.


