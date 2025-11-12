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
                slackSend(
                    channel: '#manju-jenkins-demo',
                    color: color,
                    message: """📢 *Build Notification:*
*Project:* ${env.JOB_NAME}
*Build:* #${env.BUILD_NUMBER}
*Status:* ${status}
<${env.BUILD_URL}|View Build Logs>"""
                )
            }
        }
    }
}
