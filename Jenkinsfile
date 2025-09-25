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
