pipeline {
  agent any

  tools {
    nodejs 'NodeJS 22'              // use NodeJS installation named 'NodeJS_16' in Jenkins global config
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