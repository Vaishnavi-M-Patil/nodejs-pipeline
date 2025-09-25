# Jenkins CI/CD Pipeline for Node.js Application with NGINX Reverse Proxy and SSL
This guide provides step-by-step instructions to set up a full CI/CD pipeline on a Linux server using Jenkins for a Node.js application. It also covers configuring NGINX as a reverse proxy for your app with a domain name and securing it with SSL using Certbot.

## Installing Jenkins on Ubuntu
1. Install required Java versions:
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
2. Add Jenkins key and repository:
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
```
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
3. Update package list and install Jenkins:
```
sudo apt-get update
```
```
sudo apt-get install jenkins -y
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
                   sh 'npm run build'
                   sh 'pm2 reload pipeline || pm2 start index.js --name pipeline'
            }
        }
    }
}
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

### NodeJS application:
![output1](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/7-output1.png)

## Configuring GitHub Webhook for Auto Deployment
To automate builds when code is pushed to GitHub, configure a webhook.

#### 1. Generate Jenkins API Token:
  - Log into Jenkins UI, go to User Profile → Security, click Add new token under "API Token," name the token (e.g., webhook), click Generate, and immediately copy and save the token securely.

#### 2. Configure GitHub Webhook:
- Go to your GitHub repository.
- Navigate to **Settings → Webhooks → Add webhook**.
- Fill details as follows:
- **Payload URL:** http://<your-server-ip>:8080/github-webhook/
- **Example:** http://18.209.6.170:8080/github-webhook/
- **Content type:** application/json
- **Event trigger:** Select Just the push event
- Check **Active** and click **Add webhook**.

#### 3. Update Jenkins Job for Webhook:
- Open your Jenkins job configuration.
- Under **Build Triggers**, check **GitHub hook trigger for GITScm polling**.
- Save configuration.

Now whenever you push code to GitHub, Jenkins will automatically trigger the pipeline, build, and redeploy your `Node.js` application.

### Jenkins Server:
![instance](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/1-instCreate.png)

### Hosted Zone:
![hostedzone](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/2-createHostedzone.png)

### Jenkins Pipeline:
![pipeline](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/3-createPipeline.png)

### SSL Certificate:
![certificate](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/4-createCertificate.png)

### NGINX site configuration:
![Sitefile](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/5-siteConfig.png)

### PM2 List:
![pm2list](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/6-pm2list.png)

### Webhook API Token:
![webhookapi](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/8-webhookAPI.png)

### Github webhook:
![webhook](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/9-webhook.png)

### Pipeline build by github webhook:
![pipelinesuccess](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/10-pipelineSuccess.png)

### NodeJS application:
![output2](https://github.com/Vaishnavi-M-Patil/nodejs-pipeline/blob/main/cicd/11-output2.png)
