pipeline {
    agent any

    environment {
        IMAGE = "jaggu25/project9"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/iam-mangalajagadeesh/project9-multistage.git'
            }
        }

        stage('Build Multi-Stage Image') {
            steps {
                sh 'docker build -t $IMAGE:latest .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['prod-server']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@18.61.231.104 "
                        sudo docker rm -f project9 || true;
                        sudo docker pull jaggu25/project9:latest;
                        sudo docker run -d -p 80:3000 --name project9 jaggu25/project9:latest;
                    "
                    '''
                }
            }
        }
    }
}
