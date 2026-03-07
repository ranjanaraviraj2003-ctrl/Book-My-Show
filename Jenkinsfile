pipeline {
    agent any

    tools {
        nodejs 'nodejs'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'ranjanaraviraj2003/bms-app:latest'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'ranjana-feature', url: 'https://github.com/ranjanaraviraj2003-ctrl/Book-My-Show.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('bookmyshow-app') {
                    sh 'npm install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('bookmyshow-app') {
                    withSonarQubeEnv('sonar-server') {
                        sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=BookMyShow \
                        -Dsonar.projectName=BookMyShow \
                        -Dsonar.sources=src \
                        -Dsonar.exclusions=node_modules/**,build/**,dist/**
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('bookmyshow-app') {
                    sh 'docker build -t ranjana10703/bms-app:latest .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
                        sh 'docker push ranjana10703/bms-app:latest'
                    }
                }
            }   
        } 

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop bms-container || true
                docker rm bms-container || true
                docker run -d -p 3000:3000 --name bms-container $DOCKER_IMAGE
                '''
            }
        }

    }

    post {

        success {
            emailext(
                subject: "Jenkins Build SUCCESS",
                body: """
Pipeline executed successfully.

Project: BookMyShow
Build Number: ${env.BUILD_NUMBER}
Job: ${env.JOB_NAME}

Application deployed successfully.
""",
                to: "ranjanaraviraj2003@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "Jenkins Build FAILED",
                body: """
Pipeline execution FAILED.

Project: BookMyShow
Build Number: ${env.BUILD_NUMBER}
Job: ${env.JOB_NAME}

Check Jenkins console logs.
""",
                to: "ranjanaraviraj2003@gmail.com"
            )
        }

        always {
            echo "Pipeline finished."
        }
    }
}
