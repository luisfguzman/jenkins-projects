#!/user/bin/env groovy
// For Global Shared Library: @Library('jenkins-shared-library@tag[optional]')

library identifier: 'jenkins-shared-library@master', retriever: modernSCM([
    $class: 'GitSCMSource',
    remote: 'https://github.com/foundry-vault/jenkins-shared-library.git',
    credentialsId: 'github-credentials'
])

def gv

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }

        stage("webhook test") {
            steps {
                script {
                    echo 'Testing webhook integration with Github'
                }
            }
        }

        stage("build jar") {
            steps {
                script {
                    buildJar()
                }
            }
        }

        stage("build and push image") {
            steps {
                script {
                    buildImage 'foundryvault/demo-app:jma-3.1'
                    dockerLogin()
                    dockerPush 'foundryvault/demo-app:jma-3.1'
                }
            }
            
        }

        stage("deploy") {
            steps {
                script {
                   gv.deployApp() 
                }
            }
        }
    }
}