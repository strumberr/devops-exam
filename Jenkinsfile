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

        stage('Deploy') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'mykey',
                        keyFileVariable: 'FILENAME',
                        usernameVariable: 'USERNAME'
                    )
                ]) {
                    sh '''
                    scp -o StrictHostKeyChecking=no -i ${FILENAME} -r \
                        index.js package.json node_modules \
                        ${USERNAME}@target:~/myapp

                    ssh -o StrictHostKeyChecking=no -i ${FILENAME} \
                        ${USERNAME}@target "cd ~/myapp && nohup node index.js &"
                    '''
                }
            }
        }
    }
}