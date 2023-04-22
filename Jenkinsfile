pipeline {
    agent any
    
    tools {
        maven 'maven-3.6.2' 
    }
    stages {
        stage('Calculate & Set Version') {
            when {
                branch 'release/*'
            }
            steps {
                script {
                    def branchName = env.BRANCH_NAME
                    def version = branchName.split('/')[1]
                    def pom = readMavenPom file: 'pom.xml'
                    def newVersion = "${pom.version}.${version}"
                    sh "mvn versions:set -DnewVersion=${newVersion}"
                    sh "git commit -m 'Update version to ${newVersion}' pom.xml"
                    sh "git push"
                }
            }
        }
        stage('Bump Version') {
            echo "------------------branch: ${env.BRANCH_NAME}"
            if (env.BRANCH_NAME =~ /^release\/.*$/) {
                def branchName = env.BRANCH_NAME
                def version = branchName.substring(branchName.lastIndexOf('/') + 1)
                sh "mvn versions:set -DnewVersion=${version}"      
                withCredentials([gitUsernamePassword(credentialsId: 'github tokenpass', gitToolName: 'Default')]) {
                    sh "git add ."
                    sh "git commit -m 'Bump version to ${version}'" 
                    sh "git push --force-with-lease origin HEAD:${env.BRANCH_NAME}"
                }         
            }
        }
        stage('Build & Test') {
            steps {
                sh "mvn clean test"
            }
        }
    }
    post {
        success {
            slackSend color: '#36a64f', message: "Build succeeded!",
                attachments: [
                    [
                        channel: '#general',
                        color: '#36a64f',
                        fallback: 'Build succeeded!',
                        title: 'Build Status',
                        text: 'Build succeeded!'
                    ]
                ]
        }
        failure {
            slackSend color: '#ff0000', message: "Build failed!",
                attachments: [
                    [
                        channel: '#general',
                        color: '#ff0000',
                        fallback: 'Build failed!',
                        title: 'Build Status',
                        text: 'Build failed!'
                    ]
                ]
        }
        always {
            sh "mvn checkstyle:checkstyle"
            checkstyle pattern: 'target/checkstyle-result.xml'
        }
    }
}
