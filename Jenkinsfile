  pipeline {
  agent any
  tools { nodejs 'node18' }   // Or swap to a docker { image 'node:18' } agent

  environment {
    NOTIFY_TO = "cyruskwok107@gmail.com"
    REPO_URL  = "https://github.com/Cyrus0107/8.2CDevSecOps2.git"
    BRANCH    = "main"
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
      steps {
        // stream to console and also save a file we can attach
        sh 'npm test 2>&1 | tee test.log || true'
      }
      post {
        always {
          emailext(
            subject: "Build #${env.BUILD_NUMBER} — TEST stage: ${currentBuild.currentResult}",
            to: env.NOTIFY_TO,
            body: """<p>Test stage completed with: <b>${currentBuild.currentResult}</b>.</p>
                     <p>Job: ${env.JOB_NAME} — Build: #${env.BUILD_NUMBER}</p>
                     <p>Logs attached.</p>""",
            attachLog: true,           // attach full console log
            compressLog: true,
            attachmentsPattern: 'test.log'  // also attach the stage log file
          )
        }
      }
    }

    stage('Generate Coverage') {
      steps { sh 'npm run coverage || true' }
      post { always { archiveArtifacts artifacts: 'coverage/**', allowEmptyArchive: true } }
    }

    stage('NPM Audit (Security)') {
      steps {
        // JSON is handy for marking; still shows in console via tee
        sh 'npm audit --audit-level=low --json 2>&1 | tee npm-audit.json || true'
      }
      post {
        always {
          emailext(
            subject: "Build #${env.BUILD_NUMBER} — SECURITY SCAN: ${currentBuild.currentResult}",
            to: env.NOTIFY_TO,
            body: """<p>Security scan (npm audit) finished with: <b>${currentBuild.currentResult}</b>.</p>
                     <p>Review the vulnerability list in the console and the attached JSON.</p>""",
            attachLog: true,
            compressLog: true,
            attachmentsPattern: 'npm-audit.json'
          )
        }
      }
    }
  }

  post {
    failure {
      emailext(
        subject: "Build #${env.BUILD_NUMBER} — OVERALL: FAILURE",
        to: env.NOTIFY_TO,
        body: "<p>Build failed. See attached console log for details.</p>",
        attachLog: true, compressLog: true
      )
    }
  }
}
