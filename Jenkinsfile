pipeline {
    agent any
    tools{
        maven 'maven 3.9.8'
    }
    stages{
        stage("build"){
            steps{
                echo 'Compiling worker app'
                dir('worker'){
                  sh 'npm install'
                }
            }
        }
        stage("test"){
            steps{
                echo 'Running Unit Tests on worker app'
                dir('worker'){
                    sh 'npm install; npm test'
                } 
            }
        }
    }
    post{
        always{
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            echo 'Building multibranch pipeline for worker is completed..'
        }
            failure{
                slackSend (channel: "#ci-cd", message: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
        }
            success{
                slackSend (channel: "#ci-cd", message: "Build Success: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
        }
    }
 }
