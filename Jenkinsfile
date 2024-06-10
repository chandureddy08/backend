pipeline {
    agent {
        label 'AGENT-1'
    }
    options{
        timeout(time: 10, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    stages{
        stage('Test') {
            steps{
            sh """
           sh "echo this is testing"
           ls -ltr
            """
            }
        }
        stage('Plan') {
            when{
                expression{
                    params.action == 'Apply'
                }
            }
            steps{
            sh """
            cd instance
            terraform plan
            """
            }
        }
         stage('Deploy') {
             when{
                expression{
                    params.action == 'Apply'
                }
            }
            input{
                message "Should we continue?"
                ok "Yes, we should."
            }
            steps{
            sh """
            cd instance
            terraform apply -auto-approve
            """
            }
        }
        stage('Destroy') {
             when{
                expression{
                    params.action == 'Destroy'
                }
            }
            steps{
            sh """
            cd instance
            terraform destroy -auto-approve
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
            echo 'I will run when pipeline is success'
        }
        failure { 
            echo 'I will run when pipeline is failure'
        }
    }
}