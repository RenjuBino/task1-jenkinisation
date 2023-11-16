pipeline {
    agent any
    environment{
        YOUR_NAME = credentials("YOUR_NAME")
    }
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
                export YOUR_NAME = ${YOUR_NAME}
                docker network rm task1-net && echo "Removed network" || echo "task1-net network not available"
                docker network create task1-net
                docker stop nginx && echo "Stopped nginx" || echo "nginx not running"
                docker rm nginx && echo "Removed nginx" || echo "nginx not available"
                docker stop flask-app3 && echo "Stopped flask-app3" || echo "flask-app3 not running"
                docker rm flask-app3 && echo "Removed flask-app3" || echo "flask-app3 not available"
                docker stop flask-app1 && echo "Stopped flask-app1" || echo "flask-app1 not running"
                docker rm flask-app1 && echo "Removed flask-app1" || echo "flask-app1 not available"
                docker stop flask-app2 && echo "Stopped flask-app2" || echo "flask-app2 not running"
                docker rm flask-app2 && echo "Removed flask-app2" || echo "flask-app2 not available"
                docker run -d --name flask-app3 --network task1-net -e YOUR_NAME=${YOUR_NAME} renjubino/task1jenk
                docker run -d --name flask-app1 --network task1-net -e YOUR_NAME=flask2 renjubino/task1jenk
                docker run -d --name flask-app2 --network task1-net -e YOUR_NAME=flask3 renjubino/task1jenk
                docker run -d --name nginx --network task1-net -p 80:80 renjubino/task1-nginx
                '''
            }

        }

    }

}
