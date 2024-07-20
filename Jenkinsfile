pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 10, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment {
        nexusUrl = 'nexus.chandureddy.online:8081'
        region = 'us-east-1'
        account_id = '339713021737'
    }
    stages {
        stage('Read the Version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    env.appVersion = packageJson.version
                    echo "Application version: ${env.appVersion}"
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh """
                    npm install
                    ls -ltr
                    echo "Application version: ${env.appVersion}"
                """
            }
        }
        stage('Build') {
            steps {
                sh """
                    zip -q -r backend-${env.appVersion}.zip * -x Jenkinsfile -x backend-${env.appVersion}.zip
                    ls -ltr
                """
            }
        }
        stage('Docker Build') {
            steps {
                sh """
                    aws ecr get-login-password --region ${env.region} | docker login --username AWS --password-stdin ${env.account_id}.dkr.ecr.${env.region}.amazonaws.com

                    docker build -t ${env.account_id}.dkr.ecr.${env.region}.amazonaws.com/expense-backend:${env.appVersion} .

                    docker push ${env.account_id}.dkr.ecr.${env.region}.amazonaws.com/expense-backend:${env.appVersion}
                """
            }
        }
        stage('Deploy') {
            steps {
                sh """
                    aws eks update-kubeconfig --region ${env.region} --name expense-dev
                    cd helm
                    sed -i 's/IMAGE_VERSION/${env.appVersion}/' values.yaml
                    helm upgrade backend .
                """
            }
        }
    }
    post {
        always {
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success {
            echo 'I will run when pipeline is successful'
        }
        failure {
            echo 'I will run when pipeline fails'
        }
    }
}
