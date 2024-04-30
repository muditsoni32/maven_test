pipeline {
    agent any

    environment {
        DEPLOY_SERVER = '18.206.147.42'
        DEPLOY_USER = 'deployuser'
        REMOTE_DIR = '/path/to/deployment/directory'
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
                    // Assuming you're creating a jar or war file
                    sh './mvnw package'
                    // bat '.\\mvnw package' for Windows
                }
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'my-ssh-connection', // Pre-configured SSH server in Jenkins settings
                            transfers: [
                                sshTransfer(
                                    sourceFiles: '**/target/*.jar', // Adjust this based on actual output of your build
                                    removePrefix: 'target',
                                    remoteDirectory: "${REMOTE_DIR}",
                                    execCommand: """
                                        cd ${REMOTE_DIR}
                                        java -jar *.jar
                                    """
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Clean up steps if necessary
        }
    }
}
