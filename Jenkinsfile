pipeline {
  agent any
  tools { nodejs 'node18' }      // puts node/npm on PATH

  options { timestamps(); skipDefaultCheckout(true) }

  stages {
    stage('Checkout') {
      steps { git branch: 'main', url: 'https://github.com/Cyrus0107/8.2CDevSecOps2.git' }
    }
    stage('Install Dependencies') { steps { sh 'npm ci || npm install' } }
    stage('Run Tests')            { steps { sh 'npm test || true' } }
    stage('Generate Coverage')    { steps { sh 'npm run coverage || true' } }
    stage('NPM Audit (Security)'){ steps { sh 'npm audit || true' } }
  }
  post { always { archiveArtifacts artifacts: 'coverage/**', allowEmptyArchive: true } }
}
