pipeline {
    agent any
    environment {
        EMAIL_BODY = 
         """
            <p> EXECUTED: Job <b> \'${env.JOB_NAME} : ${env.BUILD_NUMBER})\' </b> </p>
            <p>
            View console output here: 
            "<a href="${env.BUILD_URL}">${env.JOB_NAME} :${env.BUILD_NUMBER} </a>" 
            </p>
            <p><i> (Build log is attached.) </i></p>
        """
        EMAIL_SUBJECT_SUCCESS =  "Status: 'SUCCESS' -Job \'${env.JOB_NAME}:${env.BUILD_NUMBER}\'"
        
        EMAIL_SUBJECT_FAILURE =  "Status: 'FAILURE' -Job \'${env.JOB_NAME}:${env.BUILD_NUMBER}\'"
        
        EMAIL_RECEPIENT = 'nicholas.v.ndiki@gmail.com' 
        
    }
    tools{
        gradle 'Gradle-6'
    }
    stages {
        stage('clone repository') {
            steps{
                git 'https://github.com/NdiklasTheCoder/java-todo-jenkins'
            }
        }
        stage ('Build project') {
            steps{
                sh 'gradle build'
            }
        }
        stage('Tests') {
            steps {
                sh 'gradle test'
            }
        }
        stage('SonarScan') {
            environment {
                SCANNER_HOME = tool 'SonarQubeScanner'
                SONAR_TOKEN = credentials('sonar')
                PROJECT_NAME = "java-todo-jenkins"
            }
            steps {
                withSonarQubeEnv(installationName: 'TodoSonarQubeScanner', credentialsId: 'sonar') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.java.binaries=build/classes/java/ \
                    -Dsonar.projectKey=$PROJECT_NAME \
                    -Dsonar.sources=.'''
                }
            }
        }
        stage('Deploy to Heroku') {
            steps {
                withCredentials([usernameColonPassword(credentialsId: 'Ndiki', variable: 'HEROKU_CREDENTIALS' )]){
                    sh 'git push https://${HEROKU_CREDENTIALS}@git.heroku.com/secret-atoll-45927.git master'
                }
            }
        
        }
    }
    post {
        success {
            emailext attachLog: true,
                body: EMAIL_BODY,
                subject: EMAIL_SUBJECT_SUCCESS, 
                to: EMAIL_RECEPIENT
        }
        
        failure {
            emailext attachLog: true,
               body: EMAIL_BODY ,
               subject: EMAIL_SUBJECT_FAILURE, 
               to: EMAIL_RECEPIENT
        }
    }
}
