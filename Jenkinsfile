pipeline {
    agent any

    tools {
        jdk 'jdk17'
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

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([url: 'https://index.docker.io/v1/', credentialsId: 'dockerhub-cred']) {
                    sh 'docker push $DOCKER_IMAGE'
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
        always {
            echo 'Pipeline finished.'
        }

        success {
            emailext(
                subject: "SUCCESS: Jenkins Pipeline",
                body: "BookMyShow pipeline executed successfully.",
                to: "ranjanaraviraj2003@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "FAILED: Jenkins Pipeline",
                body: "BookMyShow pipeline failed. Please check Jenkins logs.",
                to: "ranjanaraviraj2003@gmail.com"
            )
        }
    }
}
