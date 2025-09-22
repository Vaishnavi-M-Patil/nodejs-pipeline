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
