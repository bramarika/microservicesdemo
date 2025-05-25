pipeline {
    agent any
    tools {
        maven 'Maven' // Ensure this matches your Jenkins Maven installation label
    }
    environment {
        SNYK_TOKEN = credentials('snyk-token') // Jenkins secret
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build goof-service') {
            steps {
                dir('goof-service') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build log4shell-goof') {
            steps {
                dir('goof-service/log4shell-goof') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build todolist-goof') {
            steps {
                dir('goof-service/todolist-goof') {
                    sh 'mvn clean package'
                }
            }
        }
	stage('Install Snyk') {
            steps {
                sh 'npm install -g snyk'
            }
        }

        stage('Snyk Scan') {
            steps {
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
