pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'quake3144'
        IMAGE_NAME = 'my-html-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Replace with your GitHub repository URL
                git branch: 'main', url: 'https://github.com/khatrigarv24/dev-ops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKERHUB_USER/$IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
		export KUBECONFIG=/var/lib/jenkins/.kube/config
                kubectl set image deployment/my-html-deployment my-html-container=$DOCKERHUB_USER/$IMAGE_NAME:$BUILD_NUMBER
                kubectl rollout status deployment/my-html-deployment
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit your app at http://<EC2_PUBLIC_IP>:30008"
        }
        failure {
            echo "❌ Build or deployment failed!"
        }
    }
}

