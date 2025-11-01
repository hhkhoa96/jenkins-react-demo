pipeline {
  agent any

  tools {
    nodejs 'NodeJS 22'              // use NodeJS installation named 'NodeJS_16' in Jenkins global config
  }

  environment {
    CI = 'true'                     // ensure react-scripts runs in CI mode
    NODE_OPTIONS = '--max_old_space_size=4096'
    AWS_REGION = 'ap-southeast-1'        // replace with your AWS region
    S3_BUCKET = 'hhkhoa-jenkins-react-demo'  // replace with your S3 bucket name
    AWS_CREDENTIALS = 'aws-credentials' // ID of AWS credentials in Jenkins
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Info') {
      steps {
        // print node and npm versions
        sh 'node -v && npm -v'
      }
    }

    stage('Install') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Test') {
      steps {
        // run tests non-interactively
        sh 'npm test -- --watchAll=false'
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
      }
    }

    stage('Archive') {
      steps {
        archiveArtifacts artifacts: 'build/**', fingerprint: true
      }
    }

    stage('Deploy to S3') {
      steps {
        withAWS(credentials: env.AWS_CREDENTIALS, region: env.AWS_REGION) {
          // Sync build directory to S3 bucket
          sh """
            aws s3 sync build/ s3://${env.S3_BUCKET}/ --delete
            echo "Deployed to http://${env.S3_BUCKET}.s3-website-${env.AWS_REGION}.amazonaws.com"
          """
        }
      }
    }
  }

  post {
    always {
      echo "Pipeline finished. See console log for details."
      // keep workspace for debugging if needed
      stash includes: '**', name: 'workspace'
    }
    failure {
      mail to: 'hhkhoa96@gmail.com', subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}", body: "See ${env.BUILD_URL}"
    }
  }
}