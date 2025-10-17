pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {
        stage('Print Git Commit') {
            steps {
                sh 'git rev-parse HEAD'
            }
        }

        stage('Check Node, NPM & Maven') {
            steps {
                sh '''
                which node || echo "node not found"
                which npm || echo "npm not found"
                node -v || echo "node version unknown"
                npm -v || echo "npm version unknown"

                which mvn || echo "mvn not found"
                mvn -v || echo "mvn version unknown"
                '''
            }
        }

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

        stage('Build Backend') {
            steps {
                dir('springbootbackend') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                WAR_PATH="/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/webapps/task.war"
                WAR_DIR="/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/webapps/task"

                rm -f "$WAR_PATH"
                rm -rf "$WAR_DIR"

                cp springbootbackend/target/*.war "$WAR_PATH"
                '''
            }
        }

        stage('Restart Tomcat') {
            steps {
                sh '''
                TOMCAT_BIN="/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43/bin"

                # Stop Tomcat (ignore error if already stopped)
                "$TOMCAT_BIN/shutdown.sh" || true

                # Wait a bit for shutdown to complete
                sleep 5

                # Start Tomcat
                "$TOMCAT_BIN/startup.sh"
                '''
            }
        }
    }

    post {
        success {
            echo ' Deployment Successful!'
        }
        failure {
            echo ' Pipeline Failed.'
        }
    }
}
