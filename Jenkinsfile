@Library('jenkins-shared-library') _

pipeline {
    agent any

    environment {
        IMAGE = "docker.io/saiprasad361/service-a:${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                myBuild()
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | podman login docker.io -u $USER --password-stdin'
                    myPush()
                }
            }
        }

        stage('Deploy to RKE2') {
            steps {
                myDeploy("k8s/deployment.yaml", "rke2-kubeconfig")
            }
        }
    }

    post {
        always {
            myNotify("Service A Pipeline Completed")
        }
    }
}
