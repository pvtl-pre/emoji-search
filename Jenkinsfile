pipeline {
    agent any
    
    environment {
        CF_CREDS = credentials('pre-bhakta')
        CF_PATH = tool name: 'CF', type: 'com.cloudbees.jenkins.plugins.customtools.CustomTool'
        CF_API_ENDPOINT = 'api.sys.clearlake.cf-app.com'
        CF_ORG = 'graybar'
        CF_SPACE_DEV = 'dev'
        CF_SPACE_TEST = 'test'
        CF_SPACE_PROD = 'prod'
        CF_HOSTNAME_DEV = 'emoji-search-dev'
        CF_HOSTNAME_TEST = 'emoji-search-test'
        CF_HOSTNAME_PROD = 'emoji-search'
    }

    tools {
        nodejs "node"
    }

    stages {
        stage('Install dependencies') { 
            steps {
                sh 'npm install' 
            }
        }
        stage('Build') { 
            steps {
                sh 'npm run build' 
            }
        }
        stage('Test') { 
            steps {
                sh 'CI=true npm test' 
            }
        }
        stage('Deploy to Dev') {
            steps {
                milestone 1
                
                timeout(time: 5, unit: 'MINUTES') {
                    input 'Deploy to Dev?' 
                }
                
                milestone 2

                sh '''
                    ${CF_PATH}/cf login -a ${CF_API_ENDPOINT} -u ${CF_CREDS_USR} -p ${CF_CREDS_PSW} -o ${CF_ORG} -s ${CF_SPACE_DEV} --skip-ssl-validation
                    ${CF_PATH}/cf push ${CF_HOSTNAME_DEV} --hostname ${CF_HOSTNAME_DEV}
                '''
            }
        }
        stage('Deploy to Test') {
            steps {
                milestone 3
                
                timeout(time: 5, unit: 'MINUTES') {
                    input 'Deploy to Test?'
                }
                
                milestone 4
                
                sh '''
                    ${CF_PATH}/cf login -a ${CF_API_ENDPOINT} -u ${CF_CREDS_USR} -p ${CF_CREDS_PSW} -o ${CF_ORG} -s ${CF_SPACE_TEST} --skip-ssl-validation
                    ${CF_PATH}/cf push ${CF_HOSTNAME_TEST} --hostname ${CF_HOSTNAME_TEST}
                '''
            }
        }
        stage('Deploy to Prod') {
            steps {
                milestone 5
                
                timeout(time: 5, unit: 'MINUTES') {
                    input 'Deploy to Prod?'
                }
                
                milestone 6
                
                sh '''
                    ${CF_PATH}/cf login -a ${CF_API_ENDPOINT} -u ${CF_CREDS_USR} -p ${CF_CREDS_PSW} -o ${CF_ORG} -s ${CF_SPACE_PROD} --skip-ssl-validation
                    ${CF_PATH}/cf push ${CF_HOSTNAME_PROD} --hostname ${CF_HOSTNAME_PROD}
                '''
            }
        }
    }
}