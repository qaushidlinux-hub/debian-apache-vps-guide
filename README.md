# Secure Debian + Apache2 VPS Deployment Guide

This repository contains my personal, industry-standard documentation for deploying, hardening, and troubleshooting an Apache2 web server on a Debian Linux VPS. 

I maintain this guide to demonstrate my foundational knowledge in Linux Systems Administration (L1 Support / Junior Developer level).

---

## 🛠️ Step 1: System Update & User Security
Before installing any packages, I update the local package index and create a non-root deployment user with sudo privileges to avoid working as root.

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Create a deployment user and add to sudo group
sudo adduser deployer
sudo usermod -aG sudo deployer
```

---

## 🛡️ Step 2: Firewall Hardening (UFW)
To secure the VPS, I implement a strict firewall policy using UFW. Server se permanently lock out hone se bachne ke liye firewall enable karne se **pehle** SSH allow karna zaroori hai.

```bash
# Install UFW
sudo apt install ufw -y

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow specific services (Using application profiles is safer)
sudo ufw allow OpenSSH
sudo ufw allow 'Apache Full'

# Enable firewall
sudo ufw enable
```

---

## 🌐 Step 3: Apache2 Installation & Virtual Hosts
I install the Apache2 web server daemon, configure a clean directory structure for hosting custom domains, and disable the default landing page.

```bash
# Install Apache2
sudo apt install apache2 -y

# Verify service status
sudo systemctl status apache2

# Create directory for the website
sudo mkdir -p /var/www/://yourdomain.com
sudo chown -R deployer:deployer /var/www/://yourdomain.com

# Enable the new site configuration and disable the default one
# (Assuming yourdomain.com.conf has been created in sites-available)
sudo a2ensite yourdomain.com.conf
sudo a2dissite 000-default.conf

# Check configuration syntax before restarting (Crucial for safety!)
sudo apachectl configtest

# Restart Apache to apply changes
sudo systemctl restart apache2
```

---

## 🔒 Step 4: Let's Encrypt SSL Setup via Certbot
I secure web traffic by generating and configuring automated Let's Encrypt SSL certificates using Certbot.

```bash
# Install Certbot and the Apache plugin
sudo apt install certbot python3-certbot-apache -y

# Run Certbot to automatically configure SSL and redirects
# sudo certbot --apache -d yourdomain.com -d ://yourdomain.com

# Test the auto-renewal cron job to prevent certificate expiration
sudo certbot renew --dry-run
```

---

## 🔒 Step 5: Production Security Hardening
To further secure the server, I hide the Apache version token and OS details. Yeh attackers ko specific software vulnerabilities scan aur identify karne se rokta hai.

```bash
# Edit the security configuration file:
# sudo nano /etc/apache2/conf-available/security.conf
# Ensure the following directives are set:
ServerTokens Prod
ServerSignature Off

# Restart service to apply security patches
sudo systemctl restart apache2
```

---

## 🔍 Step 6: Troubleshooting Procedures
When diagnosing server drops, configuration failures, or permission issues, I systematically inspect the following logs:
* **Apache Global Error Log:** `/var/log/apache2/error.log`
* **Systemd Journal Logs:** `sudo journalctl -u apache2.service`
* 
