# Jenkins CI/CD Pipeline for Node.js Application with NGINX Reverse Proxy and SSL
This guide provides step-by-step instructions to set up a full CI/CD pipeline on a Linux server using Jenkins for a Node.js application. It also covers configuring NGINX as a reverse proxy for your app with a domain name and securing it with SSL using Certbot.

## Installing Jenkins on Ubuntu
1. Add Jenkins key and repository:
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
```
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
2. Update package list and install Jenkins:
```
sudo apt-get update
```
```
sudo apt-get install jenkins -y
```
3. Install required Java versions:
```bash
sudo apt update
```
```
sudo apt install fontconfig openjdk-21-jre -y
```
```
sudo apt install openjdk-11-jdk -y
```
```
java -version
```
4. Enable and start Jenkins service:
```bash
# You can enable the Jenkins service to start at boot with the command:
sudo systemctl enable jenkins
```
```
# You can start the Jenkins service with the command:
sudo systemctl start jenkins
```
```
# Check the status of the Jenkins service:
sudo systemctl status jenkins
```

## Installing Dependencies for Node.js Application
1. Install Node.js and npm:
```
sudo apt install nodejs npm -y
```
2. Install PM2 process manager globally:
```
sudo npm install pm2@latest -g
```
3. Configure PM2 to manage apps for the Jenkins user:
```
sudo -u jenkins pm2 save
sudo -u jenkins pm2 startup
```
- `pm2 save:` Saves the current process list so it restarts on reboot.
- `pm2 startup:` Generates and configures startup scripts for automatic restart.
```
sudo -u jenkins pm2 list
```

## Creating a Jenkins Pipeline Job
1. Install necessary Jenkins plugins: Git, Pipeline, Pipeline:Groovy libraries.
2. Create a **New Pipeline Job** in Jenkins UI.
3. Use this Pipeline Script example:
```
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Vaishnavi-M-Patil/node-app.git' 
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install' 
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test' 
            }
        }
        stage('Build and Deploy') {
            steps {
                   sh 'chmod +x ./deploy.sh'
                   sh './deploy.sh'
            }
        }
    }
}
```

- `deploy.sh` Script:
```
#!/bin/bash

echo "Starting deployment..."

# Run build
npm run build

# Restart Node.js app with pm2
pm2 reload pipeline || pm2 start index.js --name pipeline

echo "Deployment finished."

```
4. Save the pipeline and click **Build**.

---

## Accessing Node.js Application Using Domain with NGINX Reverse Proxy and SSL

1. Install Nginx 
```
sudo apt update
sudo apt install nginx -y
```
2. Configure DNS
- Create a public hosted zone in Route 53 (AWS).
- Add your domain's nameservers from Route 53 to the domain registrar (e.g., GoDaddy).
- Add `A` record in Route 53 pointing to your server's public IP.

3. Create NGINX site config
- Now create file `free-domain.shop` in `/etc/nginx/sites-available/`:
```
sudo nano /etc/nginx/sites-available/free-domain.shop  
```
```bash
server {
    listen 80;
    server_name free-domain.shop;

    location / {
        proxy_pass http://54.89.55.171:5000;  # Your Node.js app address
    }
}
```
- Create a symbolic link to enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/free-domain.shop /etc/nginx/sites-enabled/           
```
- Test and reload NGINX:
```bash
sudo nginx -t        
sudo systemctl reload nginx
```
4. Install Certbot and obtain SSL Certificate
```
sudo apt install certbot python3-certbot-nginx -y
```
```
sudo certbot --nginx -d free-domain.shop
```
5. Access your app securely
Open a browser and go to https://free-domain.shop to access your Node.js app via HTTPS.
