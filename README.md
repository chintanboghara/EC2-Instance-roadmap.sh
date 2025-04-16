# EC2 Instance: Deploy a Static Website on AWS

This project guides you through creating an AWS EC2 instance, setting up an Ubuntu Linux server, and deploying a simple static website using Nginx.

## Repository Structure

```
EC2-Instance-roadmap.sh/
├── website/
│   └── index.html      # Static website HTML file
└── README.md           # Project documentation
```

## Setup Instructions

### Step 1: Create an AWS Account
- If you don’t already have an AWS account, [sign up here](https://aws.amazon.com). The AWS Free Tier includes the `t2.micro` instance used in this project.
- Log in to the [AWS Management Console](https://console.aws.amazon.com).

### Step 2: Launch an EC2 Instance
1. In the AWS Console, go to **EC2** under the **Compute** section.
2. Click **Launch Instance** and configure the instance:
   - **Choose an Amazon Machine Image (AMI)**: Select **Ubuntu Server 20.04 LTS** or **22.04 LTS** (ensure it’s marked "Free Tier Eligible").
   - **Choose an Instance Type**: Select `t2.micro` (Free Tier eligible).
   - **Configure Instance Details**: Use the default settings (default VPC and subnet).
   - **Add Storage**: Keep the default 8 GB General Purpose SSD.
   - **Add Tags** (optional): Add a tag like `Name: My Ubuntu Server` for easy identification.
   - **Configure Security Group**:
     - Create a new security group (e.g., "WebServerSG").
     - Add rules:
       - **SSH**: Type: `SSH`, Port: `22`, Source: `My IP` (or `0.0.0.0/0` for unrestricted access, though less secure).
       - **HTTP**: Type: `HTTP`, Port: `80`, Source: `0.0.0.0/0` (allows public website access).
   - **Review and Launch**:
     - Click **Launch**.
     - In the key pair dialog, select **Create a new key pair**, name it (e.g., `ubuntu-key`), and download the `.pem` file. Store it securely.
     - Click **Launch Instances**.
3. Wait for the instance to start (status: "Running"). Note its **Public IPv4 Address** (e.g., `54.123.45.67`).

### Step 3: Connect to the EC2 Instance
1. On your local machine, open a terminal (macOS/Linux) or an SSH client like PuTTY (Windows).
2. Set permissions for your key file:
   ```bash
   chmod 400 /path/to/ubuntu-key.pem
   ```
3. Connect to the instance via SSH:
   ```bash
   ssh -i /path/to/ubuntu-key.pem ubuntu@<public-ip-address>
   ```
   - Replace `<public-ip-address>` with your instance’s IP.
   - If prompted, type `yes` to confirm the connection.

**Troubleshooting**:
- Ensure the instance is running.
- Verify the security group allows SSH (port 22) from your IP.
- Check that the key file permissions are correct.

### Step 4: Install and Configure Nginx
1. Update the system packages:
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```
2. Install Nginx:
   ```bash
   sudo apt install nginx -y
   ```
3. Start Nginx and enable it to run on boot:
   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```
4. Verify Nginx is running:
   ```bash
   sudo systemctl status nginx
   ```
   - Look for "active (running)" in the output.

### Step 5: Deploy the Static Website
1. **Option 1**: Clone this repository (if hosted on GitHub):
   - Install Git: `sudo apt install git -y`
   - Clone the repo: `git clone https://github.com/chintanboghara/EC2-Instance-roadmap.sh.git`
   - Copy the HTML file: `sudo cp EC2-Instance-roadmap.sh/website/index.html /var/www/html/index.html`

2. **Option 2**: Manually create the HTML file:
   - Edit the default web file:
     ```bash
     sudo nano /var/www/html/index.html
     ```
   - Add this content:
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
     ```
   - Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

3. Ensure Nginx is running:
   ```bash
   sudo systemctl status nginx
   ```

### Step 6: Access the Website
1. Open a web browser on your local machine.
2. Enter `http://<public-ip-address>` in the address bar.
3. You should see the "Hello, World!" page.

**Troubleshooting**:
- If the page doesn’t load, check:
  - Nginx is running (`sudo systemctl status nginx`).
  - The security group allows HTTP traffic (port 80).
  - The `index.html` file is in `/var/www/html/`.

## Cleanup
To avoid unnecessary AWS charges:
1. Go to the EC2 dashboard in the AWS Console.
2. Select the instance.
3. Click **Instance State** > **Terminate Instance**.
