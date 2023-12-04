def gv

pipeline {
    agent any

    tools {
        maven 'maven-3.9'
    }

    stages {
        stage('increment java app version') {
            steps {
                script {
                    echo '[LOG] Incrementing the application version'
                    sh "mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit"
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }

        stage('build java app') {
            steps {
                script {
                    echo '[LOG] Building the application'
                    sh 'mvn clean package'
                }
            }
        }

        stage('build and push docker image') {
            steps {
                script {
                    echo '[LOG] Building the docker image'
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh "docker build -t foundryvault/demo-app:${IMAGE_NAME} ."
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push foundryvault/demo-app:${IMAGE_NAME}"
                    }
                }
            }
        }

        stage('deploy java app') {
            steps {
                script {
                    echo '[LOG] Deploying the application'
                }
            }
        }

        stage('commit version update') {
            steps {
                script {
                    echo '[LOG] Commiting the new pom.xml'
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        //sh 'git config --global user.email "jenkins@foundryvault.com"'
                        //sh 'git config --global user.name "Jenkins"'                     
                        sh 'git status'
                        sh 'git branch'
                        sh 'git config --list'
                        echo '[LOG] adding remote'
                        //sh "git remote set-url origin https://${USER}:${PASS}@github.com/foundry-vault/java-maven-app.git"
                        sh 'git add .'
                        sh 'git commit -m "[Jenkins] version increment in pom.xml"'
                        sh 'git push origin HEAD:master'
                    }
                }
            }
        }
    }
}