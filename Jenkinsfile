pipeline {
    agent any

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'test', 'prod'], description: 'Target environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests before deploy')
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building Artifact'
                    sh 'sleep 3'
                    echo 'Build complete!'
                }
            }
        }

        stage('Deploy to DEV') {
            steps {
                echo 'Deploying to DEV environment'
                sh 'sleep 3'
                echo 'DEV deployment complete!'
            }
        }

        stage('Promote to PROD') {
            input {
                message "Promote to PROD?"
                ok "Yes, absolutely!"
            }
            steps {
                echo 'Right on, mate!'
            }
        }

        stage('Deploy to PROD') {
            steps {
                echo 'Deploying to PROD environment'
                sh 'sleep 3'
                echo 'PROD deployment complete!'
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
