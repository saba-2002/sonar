pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'Sonarqube'  // Define your SonarQube server ID as configured in Jenkins
        SONARQUBE_PROJECT_KEY = 'mynewprojforgit'  // Your SonarQube project key
        SONARQUBE_PROJECT_NAME = 'mynewprojforgit'  // Your SonarQube project name
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code from the current branch
                    checkout scm
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Perform SonarQube analysis
                    withSonarQubeEnv('SonarQube') {
                        // Ensure the SonarQube Scanner is correctly configured in Jenkins
                        sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=${SONARQUBE_PROJECT_KEY} -Dsonar.projectName=${SONARQUBE_PROJECT_NAME}'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for SonarQube Quality Gate
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "SonarQube Quality Gate failed: ${qualityGate.status}"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                // Run your tests here
                sh 'mvn test'
            }
        }

        stage('Deploy to QA') {
            when {
                branch 'qa'
            }
            steps {
                // Deploy to QA environment
                echo 'Deploying to QA environment...'
                // Add your deployment commands here
            }
        }

        stage('Approval for Production') {
            when {
                branch 'qa'
            }
            steps {
                script {
                    input message: 'Approve deployment to Production?', ok: 'Deploy'
                }
            }
        }

        stage('Deploy to Production') {
            when {
                branch 'production'
            }
            steps {
                // Deploy to Production environment
                echo 'Deploying to Production environment...'
                // Add your deployment commands here
            }
        }
    }

    post {
        always {
            // Actions that are performed after the pipeline run, regardless of success or failure
            cleanWs()
        }
    }
}
