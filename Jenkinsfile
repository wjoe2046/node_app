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
        stage('Deploy to K8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops-machine']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml app-deployment.yml ubuntu@34.220.252.148:/home/ubuntu"
                    script{
                        try{
                            sh "ssh ubuntu@34.220.252.148 kubectl apply -f app-deployment.yml"
                            sh "ssh ubuntu@34.220.252.148 kubectl apply -f node-app-pod.yml"

                        } catch(error){
                            sh "ssh ubuntu@34.220.252.148 kubectl create -f . "
                        }
                    }
                }
            }
        }
    }
    
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
