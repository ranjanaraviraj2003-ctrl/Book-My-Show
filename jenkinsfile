pipeline {
    agent any

    tools {
        nodejs 'nodejs'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'ranjana10703/book-my-show'
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
                        sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=BookMyShow \
                        -Dsonar.projectName=BookMyShow \
                        -Dsonar.sources=.
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('bookmyshow-app') {
                    sh 'docker build -t ranjana10703/book-my-show .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-cred']) {
                    sh 'docker push ranjana10703/book-my-show'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop bookmyshow || true
                docker rm bookmyshow || true
                docker run -d -p 3000:3000 --name bookmyshow ranjana10703/book-my-show
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
