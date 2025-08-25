pipeline {
  agent { label 'nginx' }

  stages {
    stage('pull'){
        steps{
            git branch: 'main', url: 'https://github.com/Vaishnavi-M-Patil/node-js-sample.git'
            echo "pull successful"
        }
    }
    // stage('Install Dependencies') {
    //   steps {
    //     sh 'npm install'
    //   }
    // }
    // stage('Test') {
    //   steps {
    //     sh 'npm test || true'
    //   }
    // }
    // stage('Build') {
    //   steps {
    //     sh 'npm run build || true-'
    //   }
    // }
    // stage('Deploy') {
    //   steps {
    //     sh '''
    //       pm2 stop node-js-sample || true
    //       pm2 start index.js --name node-js-sample --update-env
    //       sudo systemctl reload nginx
    //     '''
    //   }
    // }
  }
}