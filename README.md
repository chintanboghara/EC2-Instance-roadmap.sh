# Deploy a Static Website on AWS EC2

launching an AWS EC2 instance, setting up an Ubuntu Linux server, and deploying a simple static website using Nginx.

## Prerequisites
- An AWS account (sign up at [aws.amazon.com](https://aws.amazon.com) if needed).
- Basic familiarity with terminal commands (on macOS/Linux) or an SSH client like PuTTY (on Windows).
- A web browser to test the site.

**Note**: While this project uses Free Tier-eligible resources (e.g., t2.micro instance), monitor your AWS usage to avoid unexpected charges. AWS Free Tier limits apply for the first 12 months.

## Repository Structure

```
EC2-Instance-roadmap.sh/
├── website/
│   └── index.html      # Sample static website HTML file
└── README.md           # This documentation file
```

## Setup Instructions

### Step 1: Launch an EC2 Instance
1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Navigate to **EC2** under the **Compute** section.
3. Click **Launch Instances** and configure as follows:
   - **Name and tags**: Add a name like `My-Ubuntu-Server` for easy identification.
   - **Application and OS Images (AMI)**: Search for and select **Ubuntu Server 22.04 LTS** (or 20.04 LTS; ensure it's "Free tier eligible").
   - **Instance type**: Choose `t2.micro` (Free Tier eligible).
   - **Key pair (login)**: Create a new key pair (e.g., `ubuntu-key`), select `.pem` format, and download it. Store securely.
   - **Network settings**:
     - Use default VPC and subnet.
     - Create a new security group (e.g., `WebServerSG`).
     - Add inbound rules:
       - **SSH**: Type: SSH, Protocol: TCP, Port: 22, Source: My IP (for security; use `0.0.0.0/0` if testing from multiple locations, but this is less secure).
       - **HTTP**: Type: HTTP, Protocol: TCP, Port: 80, Source: `0.0.0.0/0` (for public web access).
   - **Configure storage**: Keep the default 8 GiB gp2 (General Purpose SSD).
4. Review and launch the instance.
5. Wait for the instance status to show "Running" in the EC2 dashboard. Note the **Public IPv4 address** (e.g., `54.123.45.67`).

### Step 2: Connect to the EC2 Instance
1. On your local machine, open a terminal (macOS/Linux) or PuTTY (Windows).
2. Set restrictive permissions on your key file:
   ```bash
   chmod 400 /path/to/ubuntu-key.pem
   ```
3. Connect via SSH:
   ```bash
   ssh -i /path/to/ubuntu-key.pem ubuntu@<public-ip-address>
   ```
   - Replace `<public-ip-address>` with your instance's IP.
   - Accept the host key fingerprint if prompted (`yes`).

**Troubleshooting**:
- Ensure the instance is running and reachable.
- Verify security group rules allow SSH from your IP.
- Check key file permissions (must be 400).
- If using PuTTY, convert `.pem` to `.ppk` format.

### Step 3: Install and Configure Nginx
1. Update package lists and upgrade installed packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
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
4. Verify Nginx status:
   ```bash
   sudo systemctl status nginx
   ```
   - Look for "Active: active (running)".

**Troubleshooting**: If installation fails, retry `sudo apt update`. Ensure you're connected as the `ubuntu` user.

### Step 4: Deploy the Static Website
Choose one option below.

#### Option 1: Clone This Repository (Recommended)
1. Install Git:
   ```bash
   sudo apt install git -y
   ```
2. Clone the repository:
   ```bash
   git clone https://github.com/chintanboghara/EC2-Instance-roadmap.git
   ```
   (Adjust the URL if your repo differs.)
3. Copy the sample HTML file to Nginx's web directory:
   ```bash
   sudo cp EC2-Instance-roadmap/website/index.html /var/www/html/index.html
   ```

#### Option 2: Manually Create the HTML File
1. Edit the default index file:
   ```bash
   sudo nano /var/www/html/index.html
   ```
2. Paste the following content:
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
3. Save and exit (in nano: Ctrl+O, Enter, Ctrl+X).

4. Restart Nginx to apply changes:
   ```bash
   sudo systemctl restart nginx
   ```

### Step 5: Access the Website
1. Open a web browser.
2. Visit `http://<public-ip-address>`.
3. You should see the "Hello, World!" page.

**Troubleshooting**:
- Page not loading? Check Nginx status, security group (HTTP port 80 open), and file location (`/var/www/html/index.html`).
- Use `curl http://localhost` on the instance to test locally.
- If the IP changes (e.g., after reboot), check the EC2 dashboard for the new public IP.

## Cleanup
To avoid charges:
1. In the EC2 dashboard, select your instance.
2. Choose **Instance state** > **Terminate instance**.
3. Confirm termination. This deletes the instance and stops billing (data on the root volume is lost unless snapshotted).
