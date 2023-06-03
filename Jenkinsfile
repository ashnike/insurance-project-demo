pipeline {
    agent any

    stages {
        stage('Clean workspace') {
            steps {
                deleteDir()
            }
        }
        stage('Clean Docker images') {
            steps {
                sh 'docker rmi ashnike/insurance:1.0 || true' // Remove the specific image tag
                sh 'docker rmi ashnike/insurance:latest || true' // Remove the specific image tag
                sh 'docker image prune -f' // Remove dangling images
            }
        }

        stage('git checkout') {
            steps {
                echo 'Checking out the code from GitHub'
                git 'https://github.com/ashnike/insurance-project-demo.git'
            }
        }

        stage('maven build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('build docker image') {
            steps {
                sh 'docker build -t ashnike/insurance:1.0 .'
            }
        }

        stage('push docker image to docker hub registry') {
            steps {
                echo 'Pushing image to the registry'
                withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerHubPassword')]) {
                    sh "docker login -u ashnike -p ${dockerHubPassword}"
                    sh 'docker push ashnike/insurance:1.0'
                }
            }
        }

        stage('configure test-server and deploy insure-me') {
            steps {
                echo 'Configuring test-server'
                ansiblePlaybook become: true, credentialsId: 'sshnew', disableHostKeyChecking: true, installation: 'Ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-test-server.yml'
            }
        }

        stage('Deploy to Production server') {
            steps {
                echo 'Configuring production server'
                ansiblePlaybook become: true, credentialsId: 'sshnew', disableHostKeyChecking: true, installation: 'Ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-prod-server.yml'
            }
        }
    }
}
