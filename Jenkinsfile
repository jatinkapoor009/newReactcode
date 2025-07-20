pipeline{
    agent any
    
    stages{
        stage("Git Checkout"){
            steps{
                git url:"https://github.com/javahometech/reactjs-app",branch:"main"
            }
        }
        stage("Docker Build"){
            steps{
                sh "docker build -t kammana/react-app:${currentBuild.number} ."
            }
        }
        stage("Docker Push"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'docker_password', usernameVariable: 'docker_user')]) {
                    sh "docker login -u ${docker_user} -p ${docker_password}"
                }
                sh "docker push kammana/react-app:${currentBuild.number}"
            }
        }
        stage("Dev Deploy"){
            steps{
                sshagent(['docker-dev']) {
        
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.15.57 docker rm -f react 2>/dev/null "
                    sh "ssh ec2-user@172.31.15.57 docker run -d -p 80:80 --name=react kammana/react-app:${currentBuild.number}"
                }
            }
        }
    }
}
