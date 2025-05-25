pipeline {
    agent any
    tools {
        maven 'Maven' // Ensure this matches your Jenkins Maven installation label
        // NodeJS needed for npm/snyk - you may want to add NodeJS tool installation if configured
        // nodejs 'NodeJS' 
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
                    // Optional: skip tests if build time is an issue
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build log4shell-goof') {
            steps {
                dir('goof-service/log4shell-goof') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build todolist-goof') {
            steps {
                dir('goof-service/todolist-goof') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Install Snyk') {
            steps {
                // Retry npm install to avoid flaky failures
                retry(2) {
                    sh 'npm install -g snyk'
                }
            }
        }

        stage('Snyk Scan') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'snyk-token', variable: 'SNYK_TOKEN')]) {
                        // Authenticate Snyk CLI
                        sh 'snyk auth $SNYK_TOKEN'

                        // Run snyk test in each project directory to get detailed output & control
                        def projects = [
                            'goof-service',
                            'goof-service/log4shell-goof',
                            'goof-service/todolist-goof'
                        ]
                        
                        for (project in projects) {
                            dir(project) {
                                // Fail build if vulnerabilities found (snyk exits non-zero)
                                try {
                                    sh 'snyk test --all-projects'
				    sh 'snyk monitor --org=bramarika-me76zkSKhDZ8RmPSSswweu'
				    sh 'snyk monitor --all-projects --debug'
                                } catch (Exception e) {
                                    error("Snyk scan failed for ${project} - vulnerabilities found")
                                }
                            }
                        }
                    }
                }
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
