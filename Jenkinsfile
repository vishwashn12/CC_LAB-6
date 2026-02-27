pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app

                sleep 3
                '''
            }
        }

        stage('Deploy NGINX') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d \
                  --name nginx-lb \
                  --link backend1 \
                  --link backend2 \
                  -p 80:80 \
                  nginx

                sleep 2

                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker ps'
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed. Check console logs."
        }
    }
}
