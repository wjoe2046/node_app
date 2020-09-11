pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build docker image'){
            steps{
                sh "docker build . -t wjoe2046/nodeapp:${DOCKER_TAG}"
            }
        }
        stage('Push to Dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerpw')]) {
                    sh "docker login -u wjoe2046 -p ${dockerpw}"
                    sh "docker push wjoe2046/nodeapp:${DOCKER_TAG}"
                }
            }
        }
    }
    
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
