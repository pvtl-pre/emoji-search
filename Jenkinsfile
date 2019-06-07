pipeline {
    agent any
    
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
        stage('Deploy') { 
            steps {
                pushToCloudFoundry(
                    target: 'api.sys.clearlake.cf-app.com',
                    organization: 'graybar',
                    cloudSpace: 'dev',
                    credentialsId: 'pre-bhakta',
                    selfSigned: true
                )
            }
        }
    }
}