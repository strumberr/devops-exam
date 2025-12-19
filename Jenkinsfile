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
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'USERNAME'
                    )
                ]) {
                    sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan -H target >> ~/.ssh/known_hosts

                    scp -i $SSH_KEY -r \
                        index.js \
                        package.json \
                        node_modules \
                        $USERNAME@target:~/myapp

                    ssh -i $SSH_KEY $USERNAME@target "pkill -f index.js || true"

                    ssh -i $SSH_KEY $USERNAME@target "cd ~/myapp && nohup node index.js > app.log 2>&1 &"
                    '''
                }
            }
        }
    }
}
