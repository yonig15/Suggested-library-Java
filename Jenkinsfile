pipeline {
    agent any
    
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
