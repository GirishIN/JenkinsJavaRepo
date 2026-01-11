pipeline {
    agent any

    environment {
        APP_PORT = "8081"
        BUILD_DIR = "target"
    }

    stages {

        stage('Fetch from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/GirishIN/JenkinsJavaRepo.git'
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
                echo "Finding generated JAR..."
                JAR_FILE=$(ls target/*.jar | grep -v original | head -n 1)

                if [ -z "$JAR_FILE" ]; then
                  echo "❌ JAR file not found!"
                  exit 1
                fi

                echo "JAR found: $JAR_FILE"

                echo "Stopping existing application (if any)..."
                pkill -f "$JAR_FILE" || true

                echo "Starting application on port ${APP_PORT}..."
                nohup java -jar "$JAR_FILE" \
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
            echo "❌ Pipeline failed"
        }
    }
}
