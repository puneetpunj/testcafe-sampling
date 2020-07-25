pipeline {
    agent any 
    stages {
        stage('cleanup') {
            steps{
                echo "Workspace location: ${env.WORKSPACE}"    
                sh 'ls -l'
                sh 'rm -rf node_modules/'
            }
        }
         stage('build') {
             steps{
                sh label: 'Build Docker Image', script: 'npm run docker:build'
                echo "After package installation"    
                sh 'ls -l'
             }
        }
        stage('Execute Tests') {
             steps{
                sh label: 'Execute Tests', script: 'docker run -i testcafeimage'
             }
        }
    }
}
