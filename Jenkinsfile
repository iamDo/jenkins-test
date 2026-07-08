pipeline {
    agent any

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'test', 'prod'], description: 'Target environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests before deploy')
    }

    stages {
        stage('Validate') {
            steps {
                script {
                    echo "Validating deployment to ${params.ENVIRONMENT}"

                    if (params.ENVIRONMENT == 'prod') {
                        echo "Production deployment selected, extra care required"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building artifact...'
                sh 'mkdir -p build && echo "artifact-version-1" > build/app.txt'
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo 'Running tests...'
                sh 'test -f build/app.txt'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    retry(2) {
                        timeout(time: 1, unit: 'MINUTES') {
                            echo "Deploying to ${params.ENVIRONMENT}"
                            sh 'cat build/app.txt'
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed: preserve logs, inspect root cause, avoid blind retrying'
        }
        always {
            archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true
        }
    }
}
