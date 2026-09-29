pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Kushbalaji/suprith_Jenkins_Assignment.git'
            }
        }

        stage('Check Java and Maven') {
            steps {
                sh '''
                    echo "JAVA_HOME=$JAVA_HOME"
                    echo "PATH=$PATH"
        
                    echo "Java:"
                    java -version
        
                    echo "Javac:"
                    javac -version
        
                    echo "Maven:"
                    mvn -version
                '''
            }
        }

        stage('Build') {
            steps {
                dir('sample-app') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Upload to JFrog') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'jfrog-creds',
                                                 usernameVariable: 'JFROG_USER',
                                                 passwordVariable: 'JFROG_PASS')]) {
                    sh '''
                        echo "Uploading WAR to JFrog..."
                        WAR_FILE=$(ls sample-app/target/*.war)
                        curl -u $JFROG_USER:$JFROG_PASS -T $WAR_FILE \
                        "https://triald13vww.jfrog.io/artifactory/javarepo/${JOB_NAME}-${BUILD_NUMBER}-sample.war"

                    '''
                }
            }
        }
        stage('Deploy to Tomcat') {
                    steps {
                        sshagent (credentials: ['tomcat-ssh-key']) {
                            sh '''
                                echo "Deploying WAR to Tomcat server..."
        
                                WAR_FILE=$(ls sample-app/target/*.war)
                                SERVER_IP=172.31.45.163
                                SERVER_USER=ec2-user
                                TOMCAT_DIR=/opt/tomcat/webapps
        
                                # Copy WAR file to /tmp first (where ubuntu has access)
                                scp -o StrictHostKeyChecking=no $WAR_FILE $SERVER_USER@$SERVER_IP:/tmp/
        
                                # Move WAR into Tomcat webapps with sudo
                                ssh -o StrictHostKeyChecking=no $SERVER_USER@$SERVER_IP "sudo mv /tmp/$(basename $WAR_FILE) $TOMCAT_DIR/"
        
                                # Restart Tomcat service
                                ssh -o StrictHostKeyChecking=no $SERVER_USER@$SERVER_IP "sudo systemctl restart tomcat"
        
                                echo "Deployment completed successfully!"
                            '''
                        }
                    }
                }
            }
        }
