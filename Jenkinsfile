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

                    ssh -o StrictHostKeyChecking=no -i ${FILENAME} ${USERNAME}@target "
                      command -v node >/dev/null 2>&1 || (
                        curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash - &&
                        sudo apt install -y nodejs
                      )
                    "

                    scp -o StrictHostKeyChecking=no -i ${FILENAME} -r \
                        index.js package.json node_modules \
                        ${USERNAME}@target:~/myapp


                    ssh -o StrictHostKeyChecking=no -i ${FILENAME} \
                        ${USERNAME}@target "pkill -f index.js || true"


                    ssh -o StrictHostKeyChecking=no -i ${FILENAME} \
                        ${USERNAME}@target "cd ~/myapp && nohup node index.js > app.log 2>&1 &"
                    '''
                }
            }
        }
    }
}
