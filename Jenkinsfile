pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Building..."
            }
        }
    }

    post {
        success {
            emailext(
                subject: "Test Email from Jenkins via Gmail",
                body: """<p>Hello,</p>
                         <p>This is a <b>test email</b> sent from Jenkins using Gmail SMTP.</p>
                         <p>If you see this, Gmail SMTP is configured correctly 🎉</p>""",
                to: "cyruskwok107@gmail.com"
            )
        }
    }
}

  
