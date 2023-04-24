pipeline {
    agent any
    tools {
    maven "maven-3.6.2"
    jdk "JDK"
}
    stages {
        stage('Calculate & Set Version') {
            steps {
                script {
                    def branchName = env.BRANCH_NAME
                    def version = branchName.substring(branchName.indexOf('/') + 1)
                    def majorMinorVersion = version.substring(0, version.lastIndexOf('.'))
                    def patchVersion = version.substring(version.lastIndexOf('.') + 1)
                    def nextPatchVersion = Integer.parseInt(patchVersion) + 1
                    def newVersion = majorMinorVersion + '.' + nextPatchVersion

                    sh "mvn versions:set -DnewVersion=${newVersion}"
                    sh "git commit -am 'Bump version to ${newVersion}'"
                    sh "git push -u"
                }
            }
        }
        stage('Build & Test') {
            steps {
                sh "mvn test"
                sh "mvn verify"
            }
        }
        stage('Checkstyle') {
            steps {
                sh "mvn checkstyle:checkstyle"
            }
            post {
                always {
                    publishCheckStyle pattern: 'target/site/checkstyle-result.xml'
                }
            }
        }
    }
    post {
        success {
            slackSend(color: 'good', message: "Build succeeded!\n\n*Commit:* ${env.GIT_COMMIT}\n*Branch:* ${env.BRANCH_NAME}\n*Build URL:* ${env.BUILD_URL}")
        }
        failure {
            slackSend(color: 'danger', message: "Build failed!\n\n*Commit:* ${env.GIT_COMMIT}\n*Branch:* ${env.BRANCH_NAME}\n*Build URL:* ${env.BUILD_URL}")
        }
    }
}