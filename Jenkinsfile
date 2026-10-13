
pipeline {
    agent any

    stages {
        stage('Install Newman') {
            steps {
                sh 'npm install --no-save newman'
            }
        }

        stage('Run API Tests') {
            steps {
                sh '''
                    ./node_modules/.bin/newman run collections/Koel-API.postman_collection.json \
                    -e environments/QA.postman_environment.json \
                    --env-var "baseUrl=https://qa.koel.app" \
                    --env-var "baseURL=https://qa.koel.app" \
                    --ignore-redirects \
                    -r cli,junit \
                    --reporter-junit-export newman-results.xml
                '''
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'newman-results.xml'
        }
    }
}
