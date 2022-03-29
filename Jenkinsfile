pipeline
{
    agent any
    environment
    {
        dockerhub_repo = "shashwatknol/finalcapstone"
        dockerhub_creds = 'dockerid'
        dockerImage = ''
    }
    
    tools
    { 
        maven 'maven3'
    }
    
    stages
    {
        stage("clean")
        {
            steps
            {
                sh "mvn clean"
            }
        }
        stage("Build")
        {
            steps
            {
                sh "mvn compile"
            }
            
        }
        stage("package")
        {
            steps
            {
                sh "mvn package -DskipTests"
            }
        } 
        stage("building docker image")
        {
            steps
            {
                script
                {
                    dockerImage = docker.build dockerhub_repo + ":$GIT_COMMIT-build-$BUILD_NUMBER"
                }
            }    
        }
        stage("Pushing the docker image")
        {
            steps
            {
                script
                {
                    docker.withRegistry('', dockerhub_creds)
                    {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage("deploy")
        {
            steps
            {
                withKubeConfig([credentialsId: 'kube'])
                { 
                    sh 'pwd && ls'   
                    sh 'kubectl get po'
                    sh 'kubectl apply -f app-service.yaml'
                    sh 'kubectl apply -f mysql-pvc.yaml'
                    sh 'kubectl apply -f mysql-deployment.yaml'
                    sh 'sleep 600'
                    sh 'kubectl apply -f app-deployment.yaml'
                }

            }
        }    
                    
    }
}