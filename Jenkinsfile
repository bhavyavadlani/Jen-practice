jenkinsfile

pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin"
        TOMCAT_HOME = "/Users/vadlanibhavya/Downloads/apache-tomcat-10.1.43"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Frontend') {
            steps {
                dir('reactfrontend') {
                    sh '''
                    echo "PATH=$PATH"
                    which node || echo "node not found"
                    node -v || echo "node version unknown"
                    which npm || echo "npm not found"
                    npm -v || echo "npm version unknown"

                    npm install
                    npm run build
                    '''
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                sh '''
                FRONTEND_DIR="$TOMCAT_HOME/webapps/manager"
                rm -rf "$FRONTEND_DIR"
                mkdir -p "$FRONTEND_DIR"
                cp -R reactfrontend/dist/* "$FRONTEND_DIR"
                '''
            }
        }

        stage('Build Backend WAR') {
            steps {
                dir('springbootbackend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Deploy Backend WAR to Tomcat') {
            steps {
                sh '''
                # Automatically get the built WAR file
                WAR_FILE=$(ls springbootbackend/target/*.war | head -n 1)
                cp "$WAR_FILE" "$TOMCAT_HOME/webapps/springbootbackend.war"
                '''
            }
        }

        stage('Restart Tomcat') {
            steps {
                sh '''
                $TOMCAT_HOME/bin/shutdown.sh || true
                sleep 3
                $TOMCAT_HOME/bin/startup.sh
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