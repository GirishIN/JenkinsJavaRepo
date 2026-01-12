pipeline {
    agent any

    environment {
        APP_PORT = "8081"
        APP_DIR  = "/opt/springboot"
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
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
                set -e

                JAR_FILE=$(ls target/*.jar | grep -v original | head -n 1)
                [ -z "$JAR_FILE" ] && exit 1

                export PATH=$JAVA_HOME/bin:$PATH

                pkill -f ${APP_DIR}/app.jar || true

                cp "$JAR_FILE" ${APP_DIR}/app.jar

                nohup ${JAVA_HOME}/bin/java -jar ${APP_DIR}/app.jar \
                  --server.port=${APP_PORT} \
                  --server.address=0.0.0.0 \
                  > ${APP_DIR}/app.log 2>&1 &
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
