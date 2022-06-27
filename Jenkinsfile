#!groovy

@Library('github.com/ayudadigital/jenkins-pipeline-library@v6.3.0') _


///Initialize global config
cfg = jplConfig('reto', 'backend' ,'', [email: 'amelgar@mapfre.com'])
cfg.commitValidation.enabled = false


pipeline {
    
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    } 
    
    stages {
        //stage ('Initialize') {
        //    steps  {
        //        jplStart(cfg)
        //        sh "ln -sf $PEM_FILE kevops-academy.pem"
                //sh "terraform init -backend-config=./web/"
        //        sh "terraform init -backend-config=./"
        //    }
        //}
        stage ("Terraform init") {
            steps {
                sh "terraform init -reconfigure -force-copy"
            }
        }
        stage ("Terraform plan") {
            when { branch 'pre' } 
            steps {
                sh "terraform plan"
            }
        }
        stage ("Terraform apply") {
            when { branch 'main' }
            steps {
                sh """
                    terraform plan 
                    terraform apply --auto-approve
                """
            }
        }
        stage ("Terraform Destroy") {
            when { branch 'destro' }
            steps {
                sh "terraform destroy"
            }
        }
        stage ("Terraform show") {
            steps {
                sh "terraform show"
            }
        }
        stage ("Ansible No rama Main") {
            when { not { branch 'main' } }
            steps {
                sh "ansible-playbook reto.yml --check"
            }
        }
        stage ("Ansible rama Main") {
            when { branch 'main' }
            steps {
                sh "ansible-playbook reto.yml"
            }
        }
    }

    post {
        always {
            jplPostBuild(cfg)
        }
    }

    options {
        timestamps()
        ansiColor('xterm')
        buildDiscarder(logRotator(artifactNumToKeepStr: '20',artifactDaysToKeepStr: '30'))
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }
}
