pipeline {
    agent any

    environment {
        SONAR_ORG = 'manasvigk'
        SONAR_PROJECT = 'Manasvigk_Company-managed-project'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube code analysis...'
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarCloud') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.organization=${SONAR_ORG} \
                            -Dsonar.projectKey=${SONAR_PROJECT} \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=https://sonarcloud.io \
                            -Dsonar.qualitygate.wait=true
                        """
                        // The flag above waits for the result. 
                        // If it fails on SonarCloud, this step will fail here.
                    }
                }
            }
        }

        // REMOVED the separate 'Quality Gate' stage because 
        // 'sonar.qualitygate.wait=true' handles it in the previous stage.

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment stage...'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "✅ Jenkins Build SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: """
                    <h2>Build Successful!</h2>
                    <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                    <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><strong>SonarQube:</strong> Quality Gate Passed ✅</p>
                """,
                to: 'kombademanasvi@gmail.com',
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                subject: "❌ Jenkins Build FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: """
                    <h2>Build Failed!</h2>
                    <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                    <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><strong>Note:</strong> Check if the Quality Gate failed or a build error occurred.</p>
                """,
                to: 'kombademanasvi@gmail.com',
                mimeType: 'text/html'
            )
        }

        always {
            echo 'Pipeline execution completed.'
            cleanWs()
        }
    }
}