pipeline {
agent any

```
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
```

