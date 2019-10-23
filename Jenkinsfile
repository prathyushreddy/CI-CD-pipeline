pipeline {
  agent {
    docker {
      image 'node:10-alpine'
      args '-p 20001-20100:3000'
    }
  }
  environment {
    CI = 'true'
    HOME = '.'
    npm_config_cache = 'npm-cache'
  }
  stages {
    stage('Install Packages') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test and Build') {
      parallel {
        stage('Run Tests') {
          steps {
            sh 'npm run test'
          }
        }
        stage('Create Build Artifacts') {
          steps {
            sh 'npm run build'
          }
        }
      }
    }
    stage('Deployment') {
      parallel {
        stage('Staging') {
          when {
            branch 'prathyushreddy-patch-1'
          }
          steps {
            withAWS(region:'us-east-1',credentials:'AWS_USER') {
              s3Delete(bucket: 'ci-cd-pipeline-patty', path:'**/*')
              s3Upload(bucket: 'ci-cd-pipeline-patty', workingDir:'build', includePathPattern:'**/*');
            }
            mail(subject: 'Staging Build', body: 'New Deployment to Staging', to: 'dommata.huf039@gmail.com')
          }
        }
        stage('Production') {
          when {
            branch 'master'
          }
          steps {
            withAWS(region:'us-east-1',credentials:'AWS_USER') {
              s3Delete(bucket: 'ci-cd-pipeline-patty', path:'**/*')
              s3Upload(bucket: 'ci-cd-pipeline-patty', workingDir:'build', includePathPattern:'**/*');
            }
            mail(subject: 'Production Build', body: 'New Deployment to Production', to: 'dommata.huf039@gmail.com')
          }
        }
      }
    }
  }
}
