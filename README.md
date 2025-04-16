# EC2 Instance

This project demonstrates how to create an AWS EC2 instance, set up a Linux server (Ubuntu), and deploy a simple static website using Nginx.

## Repository Structure
```
EC2-Instancee-roadmap.sh/
├── website/
│   └── index.html      # Static website HTML file
└── README.md           # Project documentation
```

## Setup Instructions

### Step 1: Create an AWS Account
1. Log in to the [AWS Management Console](https://console.aws.amazon.com).

### Step 2: Launch an EC2 Instance
1. Navigate to **EC2** in the AWS Console.
2. Click **Launch Instance** and configure:
   - **AMI**: Ubuntu Server 20.04 LTS or 22.04 LTS (Free Tier eligible).
   - **Instance Type**: `t2.micro`.
   - **Network**: Default VPC and subnet.
   - **Storage**: 8 GB (default).
   - **Security Group**: Create a new group with:
     - SSH (port 22, restrict to your IP or `0.0.0.0/0`).
     - HTTP (port 80, source `0.0.0.0/0`).
   - **Key Pair**: Create a new key pair (e.g., `ubuntu-key`) and download the `.pem` file.
3. Launch the instance and note its **Public IPv4 Address** once running.

### Step 3: Connect to the EC2 Instance
1. Set permissions for your key file:
   ```bash
   chmod 400 /path/to/ubuntu-key.pem
   ```
2. SSH into the instance:
   ```bash
   ssh -i /path/to/ubuntu-key.pem ubuntu@<public-ip-address>
   ```

### Step 4: Install and Configure Nginx
1. Update packages:
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```
2. Install Nginx:
   ```bash
   sudo apt install nginx -y
   ```
3. Start and enable Nginx:
   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

### Step 5: Deploy the Static Website
1. Clone this repository or copy the \`website/index.html\` file to the instance:
   ```bash
   sudo cp index.html /var/www/html/index.html
   ```
   Alternatively, create `/var/www/html/index.html\` manually:
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>My Static Website</title>
   </head>
   <body>
       <h1>Hello, World!</h1>
       <p>This is my simple static website hosted on AWS EC2.</p>
   </body>
   </html>
   \`\`\`
2. Ensure Nginx is running:
   ```bash
   sudo systemctl status nginx
   ```

### Step 6: Access the Website
1. Open a browser and navigate to `http://<public-ip-address>\`.
2. You should see the "Hello, World!" page.

## Cleanup
To avoid charges:
1. Go to the EC2 dashboard.
2. Select your instance and choose **Instance State** > **Terminate Instance**.
