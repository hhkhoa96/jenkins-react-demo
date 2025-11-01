pipeline {
  agent {
    docker {
      image 'node:18'
      args '-u root:root'
    }
  }

  environment {
    CI = 'true'                     // ensure react-scripts runs in CI mode
    NODE_OPTIONS = '--max_old_space_size=4096'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
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
  }

  post {
    always {
      echo "Pipeline finished. See console log for details."
      // keep workspace for debugging if needed
      stash includes: '**', name: 'workspace'
    }
    failure {
      mail to: 'dev-team@example.com', subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}", body: "See ${env.BUILD_URL}"
    }
  }
}