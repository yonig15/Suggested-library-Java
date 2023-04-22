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
<<<<<<< HEAD

        
        


        
        

    //     stage("Publish") {
    //         when {
    //             anyOf {
    //                 branch "main"
    //                 branch "release/*"
    //             }
    //         }
    //     steps {
    //         configFileProvider([configFile(fileId: 'maven_settings', variable: 'MAVEN_SETTINGS')]) {
    // // some block

    //         sh "mvn -s $MAVEN_SETTINGS deploy"
    //         }
         
    //         }
    //     }

//         stage("Tag") {
//             when {
//                 branch "release/*"
//             }
//             steps {
//                 script {
//                     // sshagent(credentials: ["jenkins-ssh"]) {
//                     withCredentials([[$class: "UsernamePasswordMultiBinding", credentialsId: "gitlab-userpass", usernameVariable: "GL_USER", passwordVariable: "GL_PASS"]]) {
//                         sh "git clean -f -x"
//                         sh "git tag -a ${env.VERSION} -m 'version ${env.VERSION}'"
//                         sh "git push http://${GL_USER}:${GL_PASS}@${env.GIT_URL_HTTP} --tag"
//                     }
//                 }
//             }
//         }

=======
>>>>>>> efe7335 (fix bags)
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