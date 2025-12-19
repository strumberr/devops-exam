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

        stage('Deploy to Kubernetes') {
        steps {
            withCredentials([string(credentialsId: 'myapikey', variable: 'KUBECONFIG_TEXT')]) {
            sh '''
                cat <<EOF > kubeconfig.yaml
        $KUBECONFIG_TEXT
        EOF

                export KUBECONFIG=$(pwd)/kubeconfig.yaml

                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
            '''
            }
        }
}



    }
}
