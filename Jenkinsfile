pipeline{
    agent any
    
    tools{
       dockerTool 'docker'
    }
    
     environment {
    
        imageName = 'amanpatelitprofessional/demo-app'
        dockerHubCredentials = 'dockerhub'
        DOCKER_IMAGE = 'amanpatelitprofessional/demo-app'
        GIT_CREDENTIALS = credentials('githubkey')
        MAX_IMAGES_TO_KEEP = 5
    }
    
      stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/master']],
                        userRemoteConfigs: [[url: 'https://github.com/amanpatelitofficial/demo-app.git']],
                        extensions: [[$class: 'CleanCheckout']],
                        credentialsId: 'GIT_CREDENTIALS'
                    ])
                }
            }
        }

    

        
        stage('Build & Test') {
            steps {
                script {
                    
                    def DOCKER_IMAGE = "${imageName}:${BUILD_NUMBER}"
                    sh "docker build . -t ${DOCKER_IMAGE}"
                
                }
            }
        }
        
        
        stage('Push to Docker Hub') {
            steps {
                script {
                   withCredentials([usernamePassword(credentialsId:"dockerhub",passwordVariable:"dockerhubPass",usernameVariable:"dockerhubUser")]){
                        def DOCKER_IMAGE = "${imageName}:${BUILD_NUMBER}"
                        sh "docker login -u ${env.dockerhubUser} -p ${env.dockerhubPass}"
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        
        
        stage('Deploy Node.js App') {
            steps {
                script {
                    sh "docker-compose down"
                    sh "docker-compose up -d"
                }
            }
        }
        
    }
    
     post {
      always {
        emailext attachLog: true,
           subject: "'${currentBuild.result}'",
           body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
           to: 'amanpatel.itofficial@gmail.com'
        }
      }
}
