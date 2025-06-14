pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID = 'cc85a9f7-2471-475f-948e-da952a3e8d4e'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }
        stage('Run Tests'){
            parallel{
                stage('Test1'){
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps{
                        echo 'Test Stage'
                        sh 'test -f build/index.html'
                        sh 'npm test'
                    }
                }
                stage('Test2'){
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps{
                        echo 'Test Stage'
                        sh 'test -f build/index.html'
                        sh 'npm test'
                    }
                }
            }
        }

        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                echo 'Test Stage'
                sh 'test -f build/index.html'
                sh 'npm test'
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "Deplloying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }



        /*
        stage('E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.52.0-noble'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    npm install -g serv
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test

                '''
            }
        }
*/
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
