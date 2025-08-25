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
            git branch: 'main', credentialsId: 'git', url: 'https://github.com/Vaishnavi-M-Patil/node-js-sample.git'
        }
    }
    stage('Install Dependencies') {
       steps {
        dir('/home/ubuntu/workspace/nodejsPipeline/node-js-sample') {
            sh 'npm install'
        }
      }
    }
    stage('Test') {
      steps {
        sh 'npm test || true'
      }
    }
    stage('Build') {
      steps {
        sh 'npm run build || true-'
      }
    }
    stage('Deploy') {
      steps {
        sh '''
          pm2 stop node-js-sample || true
          pm2 start index.js --name node-js-sample --update-env
          sudo systemctl reload nginx
        '''
      }
    }
  }
}