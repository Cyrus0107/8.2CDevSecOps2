pipeline {
  agent any
  tools { nodejs 'node18' }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Cyrus0107/8.2CDevSecOps2.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
      post {
        always {
          emailext(
            subject: "Build #${env.BUILD_NUMBER} - Test Stage Result: ${currentBuild.currentResult}",
            body: """<p>Hello Team,</p>
                     <p>The test stage has completed with status: <b>${currentBuild.currentResult}</b>.</p>
                     <p>See attached logs for details.</p>""",
            to: "cyruskwok107@gmail.com",
            attachLog: true
          )
        }
      }
    }

    stage('NPM Audit (Security)') {
      steps {
        sh 'npm audit --audit-level=low || true'
      }
      post {
        always {
          emailext(
            subject: "Build #${env.BUILD_NUMBER} - Security Scan Result: ${currentBuild.currentResult}",
            body: """<p>Hello Team,</p>
                     <p>The security scan stage has completed with status: <b>${currentBuild.currentResult}</b>.</p>
                     <p>See attached logs for details.</p>""",
            to: "cyruskwok107@gmail.com",
            attachLog: true
          )
        }
      }
    }
  }
}
