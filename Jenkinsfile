pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git url: 'https://github.com/Sneha25081/Expenses-Tracker-WebApp.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Docker build'){
            steps{
                sh 'docker build -t expenses-tracker:latest .'
            }
        }
        stage('Docker Push'){
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId : 'dockerhubcred',
                        usernameVariable:'DOCKER_USER',
                        passwordVariable:'DOCKER_PASS'
                        )
                    ]){
                        sh '''
                        docker tag expenses-tracker:latest $DOCKER_USER/expenses-tracker:latest
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_USER/expenses-tracker:latest
                        '''
                    }
            }
        }
        stage('Remote Docker compose deploy') {
            steps{
                 sshagent(credentials: ['remote-server-ssh']) {
        sh '''
            ssh -o StrictHostKeyChecking=no ubuntu@${SERVER_IP} "
                cd ~/expenses-tracker &&
                 docker compose pull &&
                 docker compose up -d
            "
        '''
    }
            }
}
    }
}
