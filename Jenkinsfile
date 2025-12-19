pipeline {
    agent any

    tools {
        nodejs "NodeJS_24"
    }

    stages {
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build') {
            steps {
                sh '''
                  echo "Build step (Node.js)"
                  node -v
                '''
            }
        }
    }
}
