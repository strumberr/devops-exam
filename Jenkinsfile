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
                        ${USERNAME}@TARGET_IP:~/myapp

                    ssh -o StrictHostKeyChecking=no -i ${FILENAME} \
                        ${USERNAME}@TARGET_IP "cd ~/myapp && nohup node index.js &"
                    '''
                }
            }
        }
    }
}
