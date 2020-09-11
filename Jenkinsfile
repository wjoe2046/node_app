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
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'docker-hub-pw', usernameVariable: 'docker-hub-login')]) {
                    sh "docker login -u ${docker-hub-login} -p ${docker-hub-pw}"
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
