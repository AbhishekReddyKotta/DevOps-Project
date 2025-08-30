pipeline {
    agent any
    stages {
        stage('Cloning Repo') {
            steps {
                echo 'Checking repository status...'
                script {
                    if (!fileExists('vprofile-project/.git')) {
                        echo 'No local repo found. Cloning...'
                        sh 'git clone https://github.com/hkhcoder/vprofile-project.git'
                    } else {
                        dir('vprofile-project') {
                            sh 'git fetch origin'
                            def changes = sh(script: "git status -uno | grep 'Your branch is behind'", returnStatus: true)
                            if (changes == 0) {
                                echo 'Local repo is behind. Pulling latest changes...'
                                sh 'git pull origin main'
                            } else {
                                echo 'Local repo is up-to-date. Using existing copy.'
                            }
                        }
                    }
                }
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
        stage('Code Analysis with Checkstyle') {
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