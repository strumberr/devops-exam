pipeline {
    agent any

    tools {
        nodejs "NodeJS_24"
    }

    stages {

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build . --tag myapp:latest'
            }
        }

        stage('Push to ttl.sh') {
            steps {
                sh '''
                IMAGE="ttl.sh/myapp:2h"

                docker tag myapp:latest $IMAGE
                docker push $IMAGE

                echo $IMAGE > image.txt
                '''
            }
        }

        stage('Show Image') {
            steps {
                sh "echo 'Image pushed:' && cat image.txt"
            }
        }

        stage('Docker Run Image') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'dockerkey',
                        keyFileVariable: 'FILENAME',
                        usernameVariable: 'USERNAME'
                    )
                ]) {
                    sh "ssh -o StrictHostKeyChecking=no -i ${FILENAME} ${USERNAME}@docker 'docker stop myapp || true'"
                    sh "ssh -o StrictHostKeyChecking=no -i ${FILENAME} ${USERNAME}@docker 'docker rm myapp || true'"
                    sh "ssh -o StrictHostKeyChecking=no -i ${FILENAME} ${USERNAME}@docker 'docker run --name myapp --pull always --detach --publish 4444:4444 ttl.sh/myapp-node:1h'"
                }
            }
        }
    }
}
