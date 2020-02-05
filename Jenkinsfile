currentBuild.displayName = "sample-k8s#"+currentBuild.number
pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t dhoni/nodeapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                 sh "docker login -u dhoni -p bharath*22"
                    sh "docker push dhoni/nodeapp:${DOCKER_TAG}"
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops-mechine']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml root@13.235.70.238:/home/ec2-user/"
                    script{
                        try{
                            sh "ssh root@13.235.70.238 kubectl apply -f ."
                        }catch(error){
                            sh "ssh root@13.235.70.238 kubectl create -f ."
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

