pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    }
    stages {
        stage('Git') {
            steps {
                git credentialsId: 'GitHub_DevOps', url: 'https://github.com/dubbaka/projCert.git'
            }
        }
        stage('Provision new Test Server using Ansible') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible Server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/edureka/ansible/provision.yaml --extra-vars "buildNum=$BUILD_NUMBER"', execTimeout: 170000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//edureka', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t dubbaka/nginx-php:latest .'
            }
        }
        stage('Docker Hub Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Publish Image to Docker Hub') {
            steps {
                sh 'docker push dubbaka/nginx-php:latest'
            }
        }
        stage('Install required software and Run Docker Container using Ansible') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible Server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/edureka/ansible/deployment.yaml --extra-vars "buildNum=$BUILD_NUMBER"', execTimeout: 990000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//edureka', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
        unstable {
            sh 'cd /home/nath/vagrantboxes/server$BUILD_NUMBER'
            sh 'vagrant destroy server$BUILD_NUMBER'
        }
    }
}
