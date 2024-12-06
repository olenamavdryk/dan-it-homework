pipeline {
    agent { label 'worker-label' }  

    environment {
        DOCKER_IMAGE = 'cutiepieom/step-project-2'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Pull Code') {
            steps {
                // Pull the latest code from GitHub
                git branch: 'main', url: 'https://github.com/olenamavdryk/dan-it-homework.git' 
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests inside the Docker container
                    def testResult = sh(script: 'docker run --rm $DOCKER_IMAGE:$DOCKER_TAG test', returnStatus: true)
                    if (testResult != 0) {
                        currentBuild.result = 'FAILURE'
                        error("Tests failed")
                    }
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                script {
                    // Login to Docker Hub and push the image
                    withCredentials([usernamePassword(credentialsId: 'b61c3e40-cc5d-4cba-a70d-f254830bcae9', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                    }
                    sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
                }
            }
        }

        stage('Test Failure') {
            when {
                expression { currentBuild.result == 'FAILURE' }
            }
            steps {
                echo 'Tests failed'
            }
        }
    }
}
