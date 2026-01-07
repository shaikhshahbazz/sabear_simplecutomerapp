pipeline {
    agent any

    parameters {
        string(
            name: 'BRANCH_NAME',
            defaultValue: 'master',
            description: 'Git branch to build'
        )
        choice(
            name: 'ENV',
            choices: ['dev', 'qa'],
            description: 'Target environment'
        )
    }

    environment {
        APP_NAME = 'simplecustomerapp'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out branch: ${params.BRANCH_NAME}"
                git branch: "${params.BRANCH_NAME}",
                    url: 'https://github.com/shaikhshahbazz/sabear_simplecutomerapp.git'
            }
        }

        stage('Build') {
            steps {
                echo "Building ${APP_NAME}"
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests"
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo "Packaging application"
                sh 'mvn package'
            }
        }
    }

    post {
        success {
            echo "BUILD SUCCESSFUL for ${APP_NAME} in ${params.ENV} environment"
        }
        failure {
            echo "BUILD FAILED for ${APP_NAME}"
        }
    }
}
