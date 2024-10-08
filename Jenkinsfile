pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'fb60c992-d1bb-497b-9530-2967d737a3cb'
    }
    
    stages {
        
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                        npm install netlify-cli
                        node_modules/.bin/netlify --version
                        echo "eploying to production Site ID:fb60c992-d1bb-497b-9530-2967d737a3cb"
                '''
            }
        }
        
        stage('Run Tests') {
            parallel {
                stage('Test') {
                    agent {
                            docker {
                            image 'node:18-alpine'
                            reuseNode true
                            }
                        }
                    steps {
                        sh '''
                        echo "Test Stage"
                        test -f build/index.html
                        npm test
                        ls -la
                        '''
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                        }
                    }
                steps {
                    sh '''
                        npm install serve
                        node_modules/.bin/serve -s build &
                        sleep 10
                        npx playwright test --reporter=html
                    '''
                    }
                }
            }
        }

    }
    post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: 'Nice Report', useWrapperFileDirectly: true])
        }
    }
}
