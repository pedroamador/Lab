#!groovy

@Library('github.com/ayudadigital/jenkins-pipeline-library@v6.3.0') _

//// Initialize global config
cfg = jplConfig('terraform', 'backend' ,'', [email: 'micorre@direc.com'])
cfg.commitValidation.enabled = false

pipeline {
    agent { label 'docker' }

    environment {
        PEM_FILE=credentials('jpl-ssh-credentials')
        aws_access_key_id = (${env.aws_access_key_id})
        aws_secret_access_key = (${env.aws_secret_access_key})
    }

    stages {
        //stage ('Initialize') {
        //    steps  {
        //        jplStart(cfg)
        //        sh "ln -sf $PEM_FILE kevops-academy.pem"
        //        sh "terraform init -backend-config=./web/"
        //    }
        //}
        stage ("Terraform init") {
            steps {
                sh "terraform init"
            }
        }
        stage ("Terraform plan") {
            when { not { branch 'main' } }
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
        stage ("Terraform show") {
            steps {
                sh "terraform show"
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
