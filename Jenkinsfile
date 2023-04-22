node {
  def SLACK_CHANNEL = "#general"
  try {
    stage('Checkout') {
      checkout scm
    }        
    
    stage('Build & Test') {
      sh 'mvn verify'
    }

    stage('Calculate & Set Version') {
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

    stage('send success message') {
      slackSend color: 'good', message: "Build successful!", channel: SLACK_CHANNEL
    }       
  } catch(ex) {
    slackSend color: 'danger', message: "Build failed!", channel: SLACK_CHANNEL
  }    node {
  def SLACK_CHANNEL = "#general"
  try {
    stage('Checkout') {
      checkout scm
    }        
    
    stage('Build & Test') {
      sh 'mvn verify'
    }

    stage('Calculate & Set Version') {
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

    stage('send success message') {
      slackSend color: 'good', message: "Build successful!", channel: SLACK_CHANNEL
    }       
  } catch(ex) {
    slackSend color: 'danger', message: "Build failed!", channel: SLACK_CHANNEL
  }    
}
}