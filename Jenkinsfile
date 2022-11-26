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
        stage('Provision and Install software on Test Server using Ansible') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible Server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/edureka/ansible/provision.yaml --extra-vars "buildNum=$BUILD_NUMBER"', execTimeout: 150000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//edureka', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
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
        stage('Provision, Install required software and Deploy Docker Container using Ansible Playbooks') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible Server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/edureka/ansible/deployment.yaml --extra-vars "buildNum=$BUILD_NUMBER"', execTimeout: 620000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//edureka', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
