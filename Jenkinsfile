pipeline {
    agent {
        node {
            label 'BUILD'
        }
    }
    
    environment {
        TYPE = "build"
        PROJECT = "webapp"
        PHASE = "dev"
        GIT_URL = "https://github.com/auswls810/test.git"
        ARTIFACTS = "build/libs/**"
    }
    
    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: "30", artifactNumToKeepStr: "30"))
        timeout(time: 120, unit: 'MINUTES')
        timestamps()
        ansiColor('xterm')
    }
    
    tools {
        jdk "jdk8-latest"
        nodejs "node-latest"
        gradle "gradle-latest"
    }
    
    stages {
        stage('Set Environment') {
            steps {
                script {
                    if (PHASE == 'dev') {
                        BRANCH = 'develop'
                    } else if (PHASE == 'prod') {
                        BRANCH = 'master'
                    }
                }
            }
        } 
        stage('Checkout') {
            steps {
                git url: "${GIT_URL}", branch: "${BRANCH}", poll: true, changelog: true
            }
        }
        stage('Builds') {
			failFast true
            parallel {
                stage('[Build] NPM') {
                    steps {
                        sh """
                            cd ${WORKSPACE}
                            npm set progress=false
                            yarn install
                            yarn run ${NPM_BUILD_MODE}
                        """
                    }
                }

                stage('[Build] Gradle') {
                    steps {
                        sh "gradle clean build -x test --stacktrace"
                    }
                }
            }
        }
    }
    
    post {
        success {
            archiveArtifacts artifacts: "${ARTIFACTS}"
            slackSend channel: '#ops-room',
                color: 'good',
                message: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
        }
        failure {
            mail to: 'team@example.com',
             subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}