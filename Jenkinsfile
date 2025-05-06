pipeline {
    agent any

    // Trigger build on Webhook (GitHub Push)
    triggers {
        githubPush()
    }




    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Unit Test') {
            steps {
                sh 'mvn clean test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
                failure {
                    script { currentBuild.result = 'FAILURE' }
                }
            }
        }

       

        stage('Deploy to Dev') {
            when {
                branch 'mahesh'
            }
            steps {
                echo "Deploying build #${env.BUILD_NUMBER} to MAH environment"
                // Add your dev deployment script/commands here
            }
        }

        stage('Deploy to Test') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploying build #${env.BUILD_NUMBER} to PROD environment"
                // Add your test deployment script/commands here
            }
        }
    }

    post {
        success {
            slackSend(
                channel: SLACK_CHANNEL,
                color: 'good',
                message: "✅ ${env.JOB_NAME} #${env.BUILD_NUMBER} (${env.BRANCH_NAME}) SUCCESS: <${env.BUILD_URL}|Open>"
            )
        }
        unstable {
            slackSend(
                channel: SLACK_CHANNEL,
                color: 'warning',
                message: "⚠️ ${env.JOB_NAME} #${env.BUILD_NUMBER} (${env.BRANCH_NAME}) UNSTABLE: <${env.BUILD_URL}|Open>"
            )
        }
        failure {
            slackSend(
                channel: SLACK_CHANNEL,
                color: 'danger',
                message: "❌ ${env.JOB_NAME} #${env.BUILD_NUMBER} (${env.BRANCH_NAME}) FAILED: <${env.BUILD_URL}|Open>"
            )
            emailext(
                subject: "[Jenkins] Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build <a href='${env.BUILD_URL}'>#${env.BUILD_NUMBER}</a> on branch ${env.BRANCH_NAME} failed.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']]
            )
        }
        always {
            cleanWs()
        }
    }
}
