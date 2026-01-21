pipeline {
    agent any

    environment {
        SONAR_ORG = 'manasvigk'  // Replace with your SonarCloud org
        SONAR_PROJECT = 'Manasvigk_Company-managed-project'  // Replace with your project key
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
            // Add your build commands here
            // Example for Maven: sh 'mvn clean compile'
            // Example for Node.js: sh 'npm install'
            // Example for Python: sh 'pip install -r requirements.txt'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube code analysis...'
                script {
                    // Use the tool installed by Jenkins
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarCloud') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.organization=${SONAR_ORG} \
                            -Dsonar.projectKey=${SONAR_PROJECT} \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=https://sonarcloud.io
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo 'Checking SonarQube Quality Gate...'
                timeout(time: 15, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            // Add your test commands here
            // Example: sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment stage...'
            // Add deployment commands if needed
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
                    <p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
                    <p><strong>Branch:</strong> ${env.GIT_BRANCH}</p>
                    <p><strong>Commit:</strong> ${env.GIT_COMMIT}</p>
                    <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><strong>SonarQube:</strong> Quality Gate Passed ✅</p>
                    <hr>
                    <p>Check the console output at: ${env.BUILD_URL}console</p>
                """,
                to: 'your-email@gmail.com',  // Replace with your email
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                subject: "❌ Jenkins Build FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: """
                    <h2>Build Failed!</h2>
                    <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                    <p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
                    <p><strong>Branch:</strong> ${env.GIT_BRANCH}</p>
                    <p><strong>Commit:</strong> ${env.GIT_COMMIT}</p>
                    <p><strong>Build URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><strong>Failure Stage:</strong> Check console for details</p>
                    <hr>
                    <p>Check the console output at: ${env.BUILD_URL}console</p>
                """,
                to: 'your-email@gmail.com',  // Replace with your email
                mimeType: 'text/html'
            )
        }

        always {
            echo 'Pipeline execution completed.'
            cleanWs()  // Clean workspace after build
        }
    }
}
