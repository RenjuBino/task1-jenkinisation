pipeline {
    agent any
    environment{
        YOUR_NAME = credentials("YOUR_NAME")
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t renjubino/task1jenk:v${BUILD_NUMBER} .
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push renjubino/task1jenk:v${BUILD_NUMBER}
                '''
            }

        }
        stage('Staging Deploy') {
            steps {
                sh '''
                kubectl apply -f nginx-config.yaml --namespace staging
                sed -e 's,{{YOUR_NAME}},'${YOUR_NAME}',g;' -e 's,{{version}},'${BUILD_NUMBER}',g;' flask-pod-manifest.yaml | kubectl apply -f - --namespace staging
                kubectl apply -f nginx-pod.yaml --namespace staging
                '''
            }
        }
        stage('Quality Check') {
            steps {
                sh '''
                sleep 50
                export STAGING_IP=\$(kubectl get svc -o json --namespace staging | jq '.items[] | select(.metadata.name == "nginx") | .status.loadBalancer.ingress[0].ip' | tr -d '"')
                pip3 install requests
                python3 test-app.py
                '''
            }
        }
        stage('Prod Deploy') {
            steps {
                sh '''
                kubectl apply -f nginx-config.yaml --namespace prod
                sed -e 's,{{YOUR_NAME}},'${YOUR_NAME}',g;' -e 's,{{version}},'${BUILD_NUMBER}',g;' flask-pod-manifest.yaml | kubectl apply -f - --namespace prod
                kubectl apply -f nginx-pod.yaml --namespace prod
                sleep 60
                kubectl get services --namespace prod
                '''
            }

        }
        /*stage('Deploy') { Applying secret text from jenkins using Secret service
            steps {
                sh '''
                sed -e 's,{{yourname}},'${YOUR_NAME}',g;' your-name.yaml | kubectl apply -f -
                kubectl apply -f flask-pod-manifest.yaml
                sleep 60
                kubectl get services
                '''
            }

        }*/

    }

}
