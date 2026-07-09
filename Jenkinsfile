def deployToEnv(Map config) {
    unstash "artifact-${config.OS}"
    unstash "secret-${config.OS}"
    echo "Deploying to ${config.OS} ${config.ENVIRONMENT} environment"
    sh "cat artifact-${config.OS}.txt"
    echo "Secret: ${SECRET}"
    echo "Deploying to ${config.OS} ${config.ENVIRONMENT} environment complete!"
}

pipeline {
    agent any

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'test', 'prod'], description: 'Target environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests before deploy')
    }

    stages {
        stage('Build') {
            matrix {
                axes {
                    axis {
                        name 'OS'
                        values 'Ubuntu', 'macOS', 'Windows'
                    }
                }
                stages {
                    stage('Build') {
                        steps {
                            sh "echo 'Artifact for ${OS}' > artifact-${OS}.txt"
                            withCredentials([string(credentialsId: 'secret', variable: 'SECRET')]) {
                                sh "echo ${SECRET} > secret.txt"
                            }
                            stash name: "artifact-${OS}", includes: "artifact-${OS}.txt"
                            stash name: "secret-${OS}", includes: "secret.txt"
                        }
                    }
                }
            }
        }

        stage('Deploy to DEV') {
            matrix {
                axes {
                    axis {
                        name 'OS'
                        values 'Ubuntu', 'macOS', 'Windows'
                    }
                }
                stages {
                    stage('Deploy') {
                        steps {
                            deployToEnv(OS: OS, ENVIRONMENT: 'DEV')
                        }
                    }
                }
            }
        }

        stage('Deploy to PROD') {
            matrix {
                axes {
                    axis {
                        name 'OS'
                        values 'Ubuntu', 'macOS', 'Windows'
                    }
                }
                stages {
                    stage('Approve') {
                        steps {
                            script {
                                input message: "Approve deployment to ${OS} PROD environment?", ok: "Yes, absolutely!"
                            }
                            echo "Approved for ${OS} PROD environment"
                        }
                    }
                    stage('Deploy') {
                        steps {
                            deployToEnv(OS: OS, ENVIRONMENT: 'PROD')
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
