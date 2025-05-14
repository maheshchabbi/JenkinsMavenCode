pipeline {
    agent {
        label 'build'
    }
    tools {
        maven 'maven3'
    }

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
                sh 'maven clean test'
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
        
        always {
            cleanWs()
        }
    }
}
