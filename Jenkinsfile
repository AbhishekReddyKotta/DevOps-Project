pipeline {
    agent any
    tools {
        maven 'maven' // Ensure Maven is configured in Jenkins global tools
    }
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
                dir('vprofile-project') {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }
        stage('Unit Test') {
            steps {
                echo 'Running Unit Tests..'
                dir('vprofile-project') {
                    sh 'mvn test'
                }
            }
        }
        stage('Integration Test') {
            steps {
                echo 'Running Integration Tests..'
                dir('vprofile-project') {
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Code Analysis with Checkstyle') {
            steps {
                echo 'Analyzing with Checkstyle..'
                dir('vprofile-project') {
                    sh 'mvn checkstyle:checkstyle'
                }
            }
        }
        // stage('SonarQube Analysis') {
        //     steps {
        //         echo 'Analyzing with SonarQube..'
        //         dir('vprofile-project') {
        //             sh 'mvn sonar:sonar'
        //         }
        //     }
        // }
        stage('Archiving') {
            steps {
                echo 'Archiving artifacts..'
                dir('vprofile-project') {
                    sh 'mv target/*.war versions/vpro_$BUILD_NUMBER.war'
                }
            }
        }
        stage('Upload to S3') {
            steps {
                echo 'Uploading artifact to AWS S3...'
                dir('vprofile-project') {
                    sh 'aws s3 cp versions/vpro_$BUILD_NUMBER.war s3://elasticbeanstalk-us-east-1-727646465392/vpro_$BUILD_NUMBER.war'
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}