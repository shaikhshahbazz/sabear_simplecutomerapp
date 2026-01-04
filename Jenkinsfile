pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
    }

    environment {
        SONAR_PROJECT_KEY  = 'Sabear'
        SONAR_PROJECT_NAME = 'Sabear'

        NEXUS_URL  = '23.22.179.90:8081'
        NEXUS_REPO = 'hiring-app'
        NEXUS_CRED = 'NEXUS-SERVER'

        TOMCAT_URL     = 'http://54.227.155.175:8080'
        TOMCAT_CONTEXT = 'simplecustomerapp'
        TOMCAT_CRED    = 'tomcat_credentials'

        SLACK_CHANNEL = '#jenkins-integration'
    }

    stages {

        stage('Notify Build Started') {
            steps {
                slackSend(
                    channel: SLACK_CHANNEL,
                    color: '#439FE0',
                    message: "🚀 *BUILD STARTED*\nJob: ${JOB_NAME}\nBuild: #${BUILD_NUMBER}\n${BUILD_URL}"
                )
            }
        }

        stage('Git Clone') {
            steps {
                git branch: 'feature-1.1',
                    url: 'https://github.com/betawins/sabear_simplecutomerapp.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            options {
                timeout(time: 10, unit: 'MINUTES')
            }
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh '''
                      mvn sonar:sonar \
                      -Dsonar.projectKey=Sabear \
                      -Dsonar.projectName=Sabear
                    '''
                }
            }
        }

        stage('Upload WAR to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: NEXUS_CRED,
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                      WAR_FILE=$(ls target/*.war | head -n 1)

                      curl -u $NEXUS_USER:$NEXUS_PASS \
                      --upload-file $WAR_FILE \
                      http://${NEXUS_URL}/repository/${NEXUS_REPO}/com/javahome/hiring/${BUILD_NUMBER}/hiring-${BUILD_NUMBER}.war
                    '''
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: TOMCAT_CRED,
                    usernameVariable: 'TOMCAT_USER',
                    passwordVariable: 'TOMCAT_PASS'
                )]) {
                    sh '''
                      WAR_FILE=$(ls target/*.war | head -n 1)

                      curl -u $TOMCAT_USER:$TOMCAT_PASS \
                      -T $WAR_FILE \
                      "${TOMCAT_URL}/manager/text/deploy?path=/${TOMCAT_CONTEXT}&update=true"
                    '''
                }
            }
        }
    }

    post {
        success {
            slackSend(
                channel: SLACK_CHANNEL,
                color: 'good',
                message: "✅ *BUILD SUCCESS*\nJob: ${JOB_NAME}\nBuild: #${BUILD_NUMBER}"
            )
        }
        failure {
            slackSend(
                channel: SLACK_CHANNEL,
                color: 'danger',
                message: "❌ *BUILD FAILED*\nJob: ${JOB_NAME}\nBuild: #${BUILD_NUMBER}"
            )
        }
    }
}
