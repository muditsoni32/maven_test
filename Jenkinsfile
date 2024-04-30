pipeline {
    agent any

    environment {
        DEPLOY_SERVER = '18.206.147.42'
        DEPLOY_USER = 'ec2-user'
        REMOTE_DIR = '/mnt/build/'
    }

    stages {
        stage('Build') {
            steps {
                git 'https://github.com/vdespa-collab/java-rest-api-calculator.git'
                sh './mvnw clean compile'
                // bat '.\\mvnw clean compile'
            }
        }
        stage('Test') {
            steps {
                sh './mvnw test'
                // bat '.\\mvnw test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh './mvnw package -DskipTests' // Skip tests because they were already run
                    // bat '.\\mvnw package -DskipTests' for Windows
                }
                sshagent(credentials: ['mudit_test']) {
                    sh "scp -o StrictHostKeyChecking=no target/*.jar ${DEPLOY_USER}@${DEPLOY_SERVER}:${REMOTE_DIR}"
                    sh "ssh ${DEPLOY_USER}@${DEPLOY_SERVER} 'cd ${REMOTE_DIR} && java -jar *.jar'"
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline execution complete.'
        }
    }
}
