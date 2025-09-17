Step 1: Create an EC2 instance (t3.medium) and connect using SSH.
Step 2: Update packages:
sudo apt update && sudo apt upgrade -y
Step 3: Install JRE and JDK (21 + mini JDK 17 for this project):
sudo apt install openjdk-21-jdk -y
sudo apt install openjdk-17-jdk-headless -y
Step 4: Install Jenkins dependencies:
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
Step 5: Allow port 8080 in EC2 Security Group and install Jenkins.
Step 6: Update Jenkins default plugins.
Step 7: Create a pipeline and install Node.js plugin.
Step 8: Configure Node.js in Global Tools → NodeJS 22.4.1.
Step 9: Use Declarative Pipeline script:
pipeline {
  agent any
  tools { nodejs 'NodeJS 22.4.1' }
  stages {
    stage('Git Checkout') { steps { git url: 'https://github.com/jatinkapoor009/newReactcode.git', branch: 'main' } }
    stage('NPM Install') { steps { sh 'npm install' } }
    stage('Node Build') { steps { sh 'npm run build' } }
    stage('S3 Deploy') { steps { withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) { sh 'aws s3 sync dist/ s3://nodejs-project1 --delete' } } }
  }
}
Step 10: Create an IAM Role with S3 full access and attach to EC2.
Step 11: Create an S3 Bucket for deployment.
Step 12: Setup AWS CLI and provide credentials (Access Key + Secret Key) in Jenkins.
Note: If AWS CLI is not configured properly, re-run
aws configure
and check permissions.
