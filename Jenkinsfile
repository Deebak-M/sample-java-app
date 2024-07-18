pipeline {
    agent {
        label 'Docker' // Replace with the label of your Jenkins slave node
    }

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials-id' // Replace with your Docker Hub credentials ID in Jenkins
        SONARQUBE_SERVER = 'http://http://54.227.16.211:9000/'  // Replace with your SonarQube server URL
        SONARQUBE_CREDENTIALS_ID = 'sonarqube-credentials-id' // Replace with your SonarQube credentials ID in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from your version control system
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                script {
                    // Use Maven to build the project
                    sh 'mvn clean install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube Scanner'
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=my-java-project \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=${SONARQUBE_SERVER} \
                            -Dsonar.login=${SONARQUBE_CREDENTIALS_ID}"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    docker.build('my-java-app', '-f Dockerfile .')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image('my-java-app').push('latest')
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean workspace after the build
            cleanWs()
        }
    }
}
