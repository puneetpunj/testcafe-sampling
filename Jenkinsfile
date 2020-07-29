pipeline {
    
    agent any 

    parameters {
        choice(
            name: 'BUILD_IMAGE',
            choices: ['true' , 'false'],
            description: 'An option to skip building image')
        // choice(
        //     name: 'EXECUTE_TESTS',
        //     choices: ['true' , 'false'],
        //     description: 'To skip docker run command')
        choice(
            name: 'PROD_EXECUTION',
            choices: ['true' , 'false'],
            description: 'To run tests in UAT')
        choice(
            name: 'UAT_EXECUTION',
            choices: ['true' , 'false'],
            description: 'To run tests in UAT')
        choice(
            name: 'DEV_EXECUTION',
            choices: ['true' , 'false'],
            description: 'To run tests in UAT')
    }

    stages {

        stage('🚜 Image & Container Cleanup') {
            steps {
                removeTestcafeImageAndContainer()
            }
        }
        
         stage('👷 Build Image') {
              when { expression { params.BUILD_IMAGE.toBoolean() } }
             steps{
                sh 'npm run docker:build'
             }
        }
        
        stage('✅ Execute Tests Prod') {
            when { expression { params.PROD_EXECUTION.toBoolean() } }
             steps{
                sh 'docker run -i testcafeimage'
             }
        

            post{
                always {
                    copyReportFromDockerContainer()
                    publishAllureReport('prod')
                }
            }
        }
        stage('✅ Execute Tests UAT') {
            when { expression { params.UAT_EXECUTION.toBoolean() } }
             steps{
                sh 'docker run -i testcafeimage'
             }
        

            post{
                always {
                    copyReportFromDockerContainer()
                    publishAllureReport('uat')
                }
            }
        }
        stage('✅ Execute Tests Develop') {
            when { expression { params.DEV_EXECUTION.toBoolean() } }
             steps{
                sh 'docker run -i testcafeimage'
             }

            post{
                always {
                    copyReportFromDockerContainer()
                    publishAllureReport('develop')
                }
            }
        }
    }
}

def copyReportFromDockerContainer(){
  sh '''
        cont=$(docker ps -q -l)
        docker cp $cont:/app/allure allure
        ls -la
    '''
}

def publishAllureReport(env){
    script {
        allure([
            includeProperties: false,
            jdk: '',
            properties: [],
            reportBuildPolicy: 'ALWAYS',
            results: [[path: "allure/allure-results-$env"]]
        ])
    }
}

def removeTestcafeImageAndContainer(){
    sh '''
        set +e
        docker rm $(docker stop $(docker ps -a -q --filter ancestor=testcafeimage --format="{{.ID}}"))
        docker rmi testcafeimage
        set -e
    '''
}