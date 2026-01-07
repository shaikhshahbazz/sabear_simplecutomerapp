pipeline {
    agent any
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'master')
        choice(name: 'ENV', choices: ['dev', 'qa'])
    }
    environment {
        APP_NAME = 'simplecustomerapp'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH_NAME}",
                    url: 'https://github.com/shaikhshahbazz/sabear_simplecutomerapp.git'
            }
        }
        stage('Verify Workspace') {
            steps {
                sh '''
                    pwd
                    ls -l
                    echo "---- inside SimpleCustomerApp ----"
                    cd SimpleCustomerApp
                    pwd
                    ls -l
                    ls -l pom.xml
                '''
            }
        }
        stage('Build') {
            steps {
                dir('SimpleCustomerApp') {
                    sh 'mvn clean compile'
                }
            }
        }
        stage('Test') {
            steps {
                dir('SimpleCustomerApp') {
                    sh 'mvn test'
                }
            }
        }
        stage('Package') {
            steps {
                dir('SimpleCustomerApp') {
                    sh 'mvn package'
                }
            }
        }
    }
    post {
        success {
            echo ":white_tick: BUILD SUCCESS for ${APP_NAME} in ${params.ENV}"
            sh 'ls -l SimpleCustomerApp/target/'
        }
        failure {
            echo ":x: BUILD FAILED for ${APP_NAME}"
        }
    }
}
