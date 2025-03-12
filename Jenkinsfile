pipeline {
    agent any

    stages {
        stage('One') {
            steps {
                echo 'Step 1'
                sleep 3
            }
        }
        stage('Two') {
            steps {
                echo 'Step 2'
                sleep 9
            }
        }
        stage('Three') {
	    when{
                branch 'main'
                changeset "**/worker/**"
            }
            steps {
                echo 'Step 3'
                sleep 5
            }
        }
    }
    post {
            always {
                echo 'This pipeline is completed.'
        }
            failure{
                slackSend (channel: "#ci-cd", message: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
        }
            success{
                slackSend (channel: "#ci-cd", message: "Build Success: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
        }
    }
}

