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
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@34.221.125.216:/home/ec2-user"
                    script{
                        try{
                            sh "ssh ec2-user@34.221.125.216 kubectl apply -f ."
                        } catch(error){
                            sh "ssh ec2-user@34.221.125.216 kubectl create -f ."
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
