pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
    post {
    always {
        script {
            def status = currentBuild.currentResult
            def color = status == 'SUCCESS' ? 'good' : (status == 'FAILURE' ? 'danger' : 'warning')
            slackSend (
                channel: '#jenkins-builds',
                color: color,
                message: "📢 *Build Notification:*\n*Project:* ${env.JOB_NAME}\n*Build:* #${env.BUILD_NUMBER}\n*Status:* ${status}\n*Triggered by:* ${env.BUILD_USER}\n<${env.BUILD_URL}|View Build Logs>"
            )
        }
    }
}

