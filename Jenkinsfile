pipeline {
    agent { label 'kube-masternode' }
    stages {
        stage('Clone Source Code') {
            steps {
                sh '''
                if [ -d sosmed-bp ]
                then
                    cd sosmed-bp && git pull
                else 
                    git clone https://github.com/rafli024/sosmed-bp.git
                fi
                '''
            }
        }
        
        stage('Clone CICD Repo'){
            steps{
                sh '''
                    cd $WORKSPACE && ls -ltr
                '''
            }
        }
        
        stage('Docker Build') {
            steps {
                sh '''
                cd $WORKSPACE/sosmed-bp/bp-sosmed && 
                sudo docker build --no-cache --network=host -t muhammadrafli24/sosmed:${BUILD_NUMBER} -f dockerfile .
                '''
            }
        }
        
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'user-docker', variable: 'username'), string(credentialsId: 'docker-passwd', variable: 'password')]) {
                // some block
                sh "sudo docker login -u ${username} -p ${password}"
                    }
                sh '''
                sudo docker push muhammadrafli24/sosmed:${BUILD_NUMBER}
                '''
                }
            }
        
        stage('Deploy to K8S'){
            steps{
                sh'''        
                    kubectl apply -f sosmed-bp/bp-sosmed-yaml                
                    kubectl set image deployment/pesbuk sosmed=muhammadrafli24/sosmed:${BUILD_NUMBER}
                    kubectl describe deployments/pesbuk
                    kubectl get pods -A
                '''
            }
        }
        stage('CleanUp'){
            steps{
                sh '''
                    sudo docker rmi muhammadrafli24/sosmed:${BUILD_NUMBER}
                '''
            }
        }
    }
}