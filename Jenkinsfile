pipeline {
  agent any
  tools { nodejs 'node18' }     // Manage Jenkins → Tools → NodeJS installations → "node18"

  environment {
    NOTIFY_TO = 'cyruskwok107@gmail.com'
    REPO_URL  = 'https://github.com/Cyrus0107/8.2CDevSecOps2.git'
    BRANCH    = 'main'
  }

  options {
    timestamps()
    skipDefaultCheckout(true)
  }

  stages {
    stage('Checkout') {
      steps { git branch: env.BRANCH, url: env.REPO_URL }
    }

    stage('Install Dependencies') {
      steps { sh 'npm ci || npm install' }
    }

    stage('Run Tests') {
      steps { sh 'npm test || true' }   // don’t fail the pipeline for the task brief
      post {
        always {
          emailext(
            subject: "Jenkins Build #${env.BUILD_NUMBER} — TEST stage: ${currentBuild.currentResult}",
            to: env.NOTIFY_TO,
            body: """<p>Test stage finished with: <b>${currentBuild.currentResult}</b>.</p>
                     <p>Job: ${env.JOB_NAME} — Build: #${env.BUILD_NUMBER}</p>
                     <p>See attached console log.</p>""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Generate Coverage') {
      steps {
        // If your package.json lacks "coverage" this still passes because of '|| true'
        sh 'npm run coverage || true'
      }
      post {
        always {
          archiveArtifacts artifacts: 'coverage/**', allowEmptyArchive: true
        }
      }
    }

    stage('NPM Audit (Security)') {
      steps {
        // Show all vulns in the console so it’s visible for your demo video
        sh 'npm audit --audit-level=low || true'
      }
      post {
        always {
          emailext(
            subject: "Jenkins Build #${env.BUILD_NUMBER} — SECURITY SCAN: ${currentBuild.currentResult}",
            to: env.NOTIFY_TO,
            body: """<p>Security scan (npm audit) finished with: <b>${currentBuild.currentResult}</b>.</p>
                     <p>Job: ${env.JOB_NAME} — Build: #${env.BUILD_NUMBER}</p>
                     <p>Console log attached. Review vulnerabilities listed by severity.</p>""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }
  }

  post {
    failure {
      emailext(
        subject: "Jenkins Build #${env.BUILD_NUMBER} — OVERALL: FAILURE",
        to: env.NOTIFY_TO,
        body: """<p>Build failed.</p>
                 <p>Job: ${env.JOB_NAME} — Build: #${env.BUILD_NUMBER}</p>
                 <p>Console log attached.</p>""",
        attachLog: true,
        compressLog: true
      )
    }
  }
}
