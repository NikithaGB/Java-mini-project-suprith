pipeline {
    agent any

    tools {
        jdk 'Java-21'
        maven 'Maven-3.9.9'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Kushbalaji/suprith_Jenkins_Assignment.git'
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
    }
}
