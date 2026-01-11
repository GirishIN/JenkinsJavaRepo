pipeline {
    agent any

    environment {
        APP_NAME = "springboot-app"
        JAR_PATH = "target/*.jar"
        APP_PORT = "8081"
    }

    stages {

        stage('Fetch from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/<your-username>/<your-repo>.git'
            }
        }

        stage('Build using Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy on Ubuntu Instance') {
            steps {
                sh '''
                echo "Stopping existing application if running..."
                pkill -f ${APP_NAME} || true

                echo "Starting Spring Boot application..."
                nohup java -jar ${JAR_PATH} \
                    --server.port=${APP_PORT} \
                    > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Application deployed successfully on port ${APP_PORT}"
        }
        failure {
            echo "❌ Deployment failed"
        }
    }
}