pipeline {
    agent any
    stages {
        stage('Cloning Repo') {
            steps {
                echo 'Cloning repository..'
                sh 'git clone https://github.com/hkhcoder/vprofile-project.git'
                sh 'cd vprofile-project'
                sh 'git checkout awsrefactor'
            }
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'mvn clean install -DskipTests'
            }
            
        }
        stage('Unit Test') {
            steps {
                echo 'Running Unit Tests..'
                sh 'mvn test'
            }
        }
        stage('Integration Test') {
            steps {
                echo 'Running Integration Tests..'
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage(Code Analysis with Checkstyle) {
            steps {
                echo 'Analyzing with Checkstyle..'
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                echo 'Analyzing with SonarQube..'
                sh 'mvn sonar:sonar'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying..'
                sh 'mvn deploy'
            }
        }
    }
}