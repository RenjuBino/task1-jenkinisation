pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t renjubino/task1jenk .
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push renjubino/task1jenk
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                //docker stop task1
                //docker rm task1
                docker run -d -p 80:5500 --name task1 renjubino/task1jenk
                '''
            }

        }

    }

}
