pipeline {
  agent { label 'nginx' }

  stages {
    stage('Checkout Pipeline Repo') {
      steps {
        // This may be optional if Jenkins already checks out the pipeline repo automatically
        checkout scm
      }
    }
    stage('pull'){
        steps{
            dir('node-js-sample') {
              git branch: 'master', url: 'https://github.com/Vaishnavi-M-Patil/node-js-sample.git'
            }
        }
    }
    stage('Install Dependencies') {
       steps {
          dir('node-js-sample') {
            sh 'npm install'
          }
      }
    }
    stage('Setup PM2') {
      steps {
        sh 'npm install -g pm2'
      }
    }
    stage('Deploy') {
      steps {
          dir('node-js-sample') {
            sh '''
                npm install
                pm2 stop node-js-sample || true
                pm2 start index.js --name node-js-sample --update-env
                sudo systemctl reload nginx
            '''
          }
      }
    }
  }
}