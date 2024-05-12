pipeline {
    agent any

    environment {
        NODEJS_HOME = tool 'NodeJS'
        DOCKER_MAIN_IMAGE = 'nodemain:v1.0'
        DOCKER_DEV_IMAGE = 'nodedev:v1.0'
    }

    stages {
        stage('Build') {
            steps {
                sh "${NODEJS_HOME}/bin/npm install"
            }
        }

        stage('Test') {
            steps {
                sh "${NODEJS_HOME}/bin/npm test"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh "docker stop nodemain || true && docker rm nodemain || true"
                        sh "docker build -t ${DOCKER_MAIN_IMAGE} ."
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh "docker stop nodedev || true && docker rm nodedev || true"
                        sh "docker build -t ${DOCKER_DEV_IMAGE} ."
                    } else {
                        error "Branch not supported"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh "docker run -d --expose 3000 -p 3000:3000 ${DOCKER_MAIN_IMAGE}"
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh "docker run -d --expose 3001 -p 3001:3000 ${DOCKER_DEV_IMAGE}"
                    } else {
                        error "Branch not supported"
                    }
                }
            }
        }
    }

    post {
        always {
        }
    }
}
