pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git branch to build')
    }

    environment {
        REPO_URL = 'https://github.com/example/repo.git' // replace with your repo
        LOG_DIR = 'logs'
        ARCHIVE_DIR = 'archive_logs'
    }

    options {
        timeout(time: 5, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning branch: ${params.BRANCH_NAME}"
                git branch: "${params.BRANCH_NAME}", url: "${REPO_URL}"
            }
        }

        stage('Setup') {
            steps {
                echo "Preparing environment..."
                sh '''
                    mkdir -p $LOG_DIR
                    mkdir -p $ARCHIVE_DIR
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Starting build..."
                retry(2) {
                    sh '''
                        echo "Build completed successfully" > $LOG_DIR/build.log
                    '''
                }
            }
        }

        stage('Test (Simulated Failure)') {
            steps {
                script {
                    echo "Running tests..."

                    // Simulate failure
                    sh '''
                        echo "Test failed due to error XYZ" > $LOG_DIR/test.log
                        exit 1
                    '''
                }
            }
        }

        stage('Deploy') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                echo "Deploying application..."
                sh '''
                    echo "Deployment successful" > $LOG_DIR/deploy.log
                '''
            }
        }
    }

    post {

        success {
            echo "Build SUCCESS ✅"

            // Archive logs
            archiveArtifacts artifacts: 'logs/**', allowEmptyArchive: true

            // Mock notification
            echo "Notification: SUCCESS email sent to team"
        }

        failure {
            echo "Build FAILED ❌"

            // Mock notification
            echo "ALERT: Sending FAILURE notification to team..."

            // Copy logs for failure analysis
            sh '''
                cp -r $LOG_DIR/* $ARCHSPACE 2>/dev/null || true
            '''

            archiveArtifacts artifacts: 'logs/**', allowEmptyArchive: true
        }

        unstable {
            echo "Build UNSTABLE ⚠️"
        }

        always {
            echo "Running cleanup..."

            // Delete logs older than 1 day
            sh '''
                find $WORKSPACE -type f -name "*.log" -mtime +1 -exec rm -f {} \\; || true
            '''

            // Clean workspace
            cleanWs()
        }
    }
}
