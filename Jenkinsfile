pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '5')) // keep last 5 builds
        timeout(time: 5, unit: 'MINUTES') // global timeout
    }

    environment {
        LOG_DIR = "logs"
        ARCHIVE_DIR = "archive_logs"
    }

    stages {

        stage('Setup') {
            steps {
                echo "Setting up directories..."
                sh '''
                    mkdir -p $LOG_DIR
                    mkdir -p $ARCHIVE_DIR
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Build started..."
                sh 'echo "Build successful" > logs/build.log'
            }
        }

        stage('Test (Simulate Failure)') {
            steps {
                script {
                    echo "Running tests..."

                    // Simulate failure
                    sh '''
                        echo "Test failed due to error XYZ" > logs/test.log
                        exit 1
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "This stage will be skipped if failure occurs"
            }
        }
    }

    post {

        success {
            echo "Pipeline executed successfully!"
        }

        failure {
            echo "Build FAILED!"

            // Mock email notification
            echo "ALERT: Sending failure notification to team..."

            // Archive failure logs
            sh '''
                cp -r $LOG_DIR/* $ARCHIVE_DIR/ || true
            '''

            archiveArtifacts artifacts: 'archive_logs/**', allowEmptyArchive: true
        }

        always {
            echo "Running cleanup..."

            // Cleanup old artifacts (example: delete logs older than 1 day)
            sh '''
                find $WORKSPACE -type f -name "*.log" -mtime +1 -exec rm -f {} \\; || true
            '''

            // Optional: clean workspace
            cleanWs()
        }
    }
}
