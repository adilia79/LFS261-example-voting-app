pipeline {

  agent none

  stages{
      stage("Worker build"){
        when{
            changeset "**/worker/**"
          }

        agent{
          docker{
            image 'maven:3.9.8-sapmachine-21'
            args '-v $HOME/.m2:/root/.m2'
          }
        }

        steps{
          echo 'Compiling worker app..'
          dir('worker'){
            sh 'mvn compile'
          }
        }
      }
      stage("Worker test"){
        when{
          changeset "**/worker/**"
        }
        agent{
          docker{
            image 'maven:3.9.8-sapmachine-21'
            args '-v $HOME/.m2:/root/.m2'
          }
        }
        steps{
          echo 'Running Unit Tets on worker app..'
          dir('worker'){
            sh 'mvn clean test'
           }

          }
      }
      stage("Worker package"){
        when{
          branch 'main'
          changeset "**/worker/**"
        }
        agent{
          docker{
            image 'maven:3.9.8-sapmachine-21'
            args '-v $HOME/.m2:/root/.m2'
          }
        }
        steps{
          echo 'Packaging worker app'
          dir('worker'){
            sh 'mvn package -DskipTests'
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
          }

        }
      }

      stage('Worker docker-package'){
          agent any
          when{
            changeset "**/worker/**"
            branch 'main'
          }
          steps{
            echo 'Packaging worker app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def workerImage = docker.build("adilia79/worker:v${env.BUILD_ID}", "./worker")
                  workerImage.push()
                  workerImage.push("${env.BRANCH_NAME}")
                  workerImage.push("latest")
              }
            }
          }
        }
      stage('Vote build'){ 
            agent{
                docker{
                    image 'python:3.11-slim'
                    args '--user root'
                    }
                    }

            steps{ 
                echo 'Compiling vote app.' 
                dir('vote'){
            
                        sh "pip install -r requirements.txt"

                } 
            } 
        } 
        stage('Vote test'){ 
           
            agent {
                docker{
                    image 'python:3.11-slim'
                    args '--user root'
                    }
                    }
            steps{ 
                echo 'Running Unit Tests on vote app.' 
                dir('vote'){ 
                   
                        sh "pip install -r requirements.txt"
                        sh 'nosetests -v'
                        
                        
                } 
            } 
        } 

      stage('Vote docker-package'){
          agent any
         
          steps{
            echo 'Packaging vote app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  // ./vote is the path to the Dockerfile that Jenkins will find from the Github repo
                  def voteImage = docker.build("adilia79/vote:v${env.BUILD_ID}", "./vote")
                  voteImage.push()
                  voteImage.push("${env.BRANCH_NAME}")
                  voteImage.push("latest")
              }
            }
          }
        }
        stage("Result build"){
            when{
                changeset "**/result/**"
            }
            steps{
                echo 'Compiling result app.'
                dir('result'){
                  sh 'npm install'
                }
            }
        }
        stage("Result test"){
            when{
                changeset "**/result/**"
            }
            steps{
                echo 'Running Unit Tests on result app.'
                dir('result'){
                    sh 'npm install'
                    sh 'npm test'
                } 
            }
        }
        stage('Result docker-package'){
          agent any

          steps{
            echo 'Packaging result app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  // ./result is the path to the Dockerfile that Jenkins will find from the Github repo
                  def resultImage = docker.build("adilia79/result:v${env.BUILD_ID}", "./result")
                  resultImage.push()
                  resultImage.push("${env.BRANCH_NAME}")
                  resultImage.push("latest")
              }
            }
          }
        }
  }

  post{
    always{
        echo 'Building mono pipe is complete.'
    }
  }
}
