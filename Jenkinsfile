pipeline {
    agent any
    tools {
        maven 'M3' // Make sure 'M3' matches what you named it in Jenkins UI
    }
    environment {
        // Reference the Jenkins credential ID for the Snyk token
        SNYK_TOKEN = credentials('snyk-token')
	
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Assuming you are using Maven for Java build
                sh 'mvn clean package'
            }
        }

        stage('Snyk Scan') {
            steps {
                // Authenticate Snyk CLI and run scan
                sh 'snyk auth $SNYK_TOKEN'
                sh 'snyk test --all-projects'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and scan successful!'
        }
        failure {
            echo 'Build or scan failed.'
        }
    }
}
