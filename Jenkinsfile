
pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    stages {
        stage('Install Newman') {
            steps {
                sh 'npm install --no-save newman'
            }
        }

        stage('Run Original API Tests') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    sh '''
                        ./node_modules/.bin/newman run collections/Koel-API.postman_collection.json \
                        -e environments/QA.postman_environment.json \
                        --env-var "baseUrl=https://qa.koel.app" \
                        --env-var "baseURL=https://qa.koel.app" \
                        --ignore-redirects \
                        -r cli,junit \
                        --reporter-junit-export newman-original-results.xml
                    '''
                }
            }
        }

        stage('Run Sprint 12 API Tests') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    sh '''
                        ./node_modules/.bin/newman run collections/Sprint12-Like-Unlike-Songs.postman_collection.json \
                        -e environments/QA.postman_environment.json \
                        --env-var "baseUrl=https://qa.koel.app" \
                        --env-var "baseURL=https://qa.koel.app" \
                        --ignore-redirects \
                        -r cli,junit \
                        --reporter-junit-export newman-sprint12-results.xml
                    '''
                }
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'newman-*-results.xml'
        }
    }
}
