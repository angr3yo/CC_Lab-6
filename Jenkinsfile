pipeline {
    agent any

    stages {

        stage('Create Network') {
            steps {
                sh '''
                docker network create labnet || true
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 --network labnet backend-app
                docker run -d --name backend2 --network labnet backend-app

                sleep 5
                '''
            }
        }

        stage('Run NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d \
                --name nginx-lb \
                --network labnet \
                -p 80:80 \
                nginx

                sleep 3

                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}