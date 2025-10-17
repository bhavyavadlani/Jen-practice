pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {
        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('reactfrontend') {
                    sh '''
                    npm install
                    npm run build
                    '''
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                sh '''
                REACT_DEPLOY_DIR="/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/webapps/task-manage"

                if [ -d "$REACT_DEPLOY_DIR" ]; then
                    rm -rf "$REACT_DEPLOY_DIR"
                fi

                mkdir -p "$REACT_DEPLOY_DIR"
                cp -R reactfrontend/dist/* "$REACT_DEPLOY_DIR"
                '''
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('springbootbackend') {
                    sh 'mvn clean package'
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                WAR_PATH="/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/webapps/task.war"
                WAR_DIR="/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/webapps/task"

                if [ -f "$WAR_PATH" ]; then
                    rm -f "$WAR_PATH"
                fi

                if [ -d "$WAR_DIR" ]; then
                    rm -rf "$WAR_DIR"
                fi

                cp springbootbackend/target/*.war "/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/webapps/"
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}