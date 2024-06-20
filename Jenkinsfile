pipeline {
    agent any

    environment {
        DB_NAME = "my-named-db"
        DB_PWD = credentials("DB_PWD")
    }
    
    stages {
        stage('Create Secret & DB') {
            steps {
                sh "sed -e 's,{{DATABASE}},'${DB_NAME}',g;' -e 's,{{PASSWORD}},'${DB_PWD}',g;' secret.yaml | kubectl apply -f -"
            }
        }
    
        stage('Deploy App') {
            steps {
                sh "kubectl apply -f flask-app.yaml"
            }
        }
    
        stage('Deploy NGINX') {
            steps {
                sh "kubectl apply -f nginx.yaml"
                sh "sleep 50"
            }
        }
    
        stage('Test & Clean Up') {
            steps {
                sh "kubectl get svc flask-app"
                sh "kubectl get svc nginx-service"
                sh "sleep 100"

                sh "sed -e 's,{{DATABASE}},'${DB_NAME}',g;' -e 's,{{PASSWORD}},'${DB_PWD}',g;' secret.yaml | kubectl delete -f -"
                sh "kubectl delete -f nginx.yaml"
                sh "kubectl delete -f flask-app.yaml"

            }
        }
    
    }
}