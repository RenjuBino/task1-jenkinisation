pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t renjubino/task1jenk .
                docker build -t renjubino/task1-nginx nginx
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push renjubino/task1jenk
                docker push renjubino/task1-nginx
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@renju-deploy <<EOF
                docker network rm task1-net && echo "Removed network" || echo "task1-net network not available"
                docker network create task1-net
                docker stop nginx && echo "Stopped nginx" || echo "nginx not running"
                docker rm nginx && echo "Removed nginx" || echo "nginx not available"
                docker stop flask-app && echo "Stopped flask-app" || echo "flask-app not running"
                docker rm flask-app && echo "Removed flask-app" || echo "flask-app not available"
                docker run -d --name flask-app --network task1-net renjubino/task1jenk
                docker run -d --name nginx --network task1-net -p 80:80 renjubino/nginx
                '''
            }

        }

    }

}
