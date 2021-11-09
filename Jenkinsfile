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
                    git clone ${repoURL}
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
                docker build --no-cache --network=host -t muhammadrafli24/sosmed:${BUILD_NUMBER} -f dockerfile .
                '''
            }
        }
        
        stage('Docker Push'){
            steps{
                sh '''
                   sudo docker login -u ${docker_username} -p ${docker_password} 
                   sudo docker push muhammadrafli24/sosmed:${BUILD_NUMBER}
                '''
            }
        }
        
        stage('Deploy to K8S'){
            steps{
                sh'''        
                    kubectl apply -f sosmed-bp/bp-sosmed-yaml                
                    kubectl set image deployment/sosmed-bp sosmed-bp=muhammadrafli24/sosmed:${BUILD_NUMBER}
                    kubectl describe deployments/sosmed-bp
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