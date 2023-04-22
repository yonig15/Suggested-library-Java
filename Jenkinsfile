pipeline {

    agent any

    tools {
        maven "maven-3.6.2"
    }

    environment {
        GIT_URL_HTTP = "https://github.com/PeretzBatel/lib0.git"
    }

    stages {
        stage("Calculate & Set version") {
            when {
                branch "release/*"
            }
            steps {
                script {
                    majorMinor = env.BRANCH_NAME.split("/")[1]  // x.y
                    withCredentials([[$class: "UsernamePasswordMultiBinding", credentialsId: "gitlab-pass", usernameVariable: "GL_USER", passwordVariable: "GL_PASS"]]) {
                        sh "git fetch http://${GL_USER}:${GL_PASS}@${env.GIT_URL_HTTP} --tags"
                    }
                    previousTag = sh(script: "git describe --tags --abbrev=0 | grep -E '^$majorMinor' || true", returnStdout: true).trim()  // x.y.z or empty string. `grep` is used to prevent returning a tag from another release branch; `true` is used to not fail the pipeline if grep returns nothing.

                    if (!previousTag) {
                        patch = "0"
                    } else {
                        patch = (previousTag.tokenize(".")[2].toInteger() + 1).toString()
                    }
                    env.VERSION = majorMinor + "." + patch

                    sh "mvn versions:set -DnewVersion=$env.VERSION"
                }
            }
        }

        stage("Build & Test") {
            steps {
                sh "mvn verify"
                
            }
        }


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

    }
}
