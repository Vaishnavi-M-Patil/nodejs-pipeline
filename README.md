# CI/CD Pipeline with Jenkins
To set up a CI/CD pipeline with Jenkins on a Linux server, begin by installing Jenkins, and then configure a pipeline job to pull code from Git, install dependencies, run tests, and deploy the application.

## Installing Jenkins on Linux
1. Install Jenkins on ubuntu
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
```
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
```
sudo apt-get update
```
```
sudo apt-get install jenkins -y
```
2. Also we need java
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
3. Start and enable Jenkins
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

## Install required packages for nodejs application
```
sudo apt nodejs npm -y
```
```
sudo npm install pm2@latest -g
```
```
sudo -u jenkins pm2 save
sudo -u jenkins pm2 startup
```
#### Purpose of these commands
- pm2 save: Saves the current process list so PM2 will remember which applications to start on reboot.
- pm2 startup: Generates and configures a startup script/systemd service so PM2 and your Node.js apps restart automatically when the machine boots.
```
sudo -u jenkins pm2 list
```

## Creating a Jenkins Pipeline Job
1. Install Git, Pipeline, Pipeline:Groovy libraries Plugins
2. Create a New Pipeline Job
3. Pipeline Script Example:
```
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Vaishnavi-M-Patil/node-js-sample.git' 
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
`deploy.sh`:
```
#!/bin/bash

echo "Starting deployment..."

# Change to app directory if needed
# cd /var/lib/jenkins/workspace/pipeline/


# Install/update dependencies
# npm install

# Run any database migrations or build commands if required
# npm run migrate
npm run build

# Restart the application with a process manager like pm2
pm2 reload pipeline || pm2 start index.js --name pipeline

echo "Deployment finished."
```
4. Now save and apply the pipeline
5. Click on Build.
