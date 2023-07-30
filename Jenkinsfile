pipeline {
    agent none
    environment {
        EC2_USER = 'ubuntu'
        EC2_HOST = '52.77.209.117'
        EC2_KEY_FILE = credentials('wushsecret')
        DOCKER_IMAGE = 'wushie/react-app'
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:16-buster-slim'
                    args '-p 3000:3000'
                }
            }
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:16-buster-slim'
                    args '-p 3000:3000'
                }
            }
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deploy to EC2') {
            agent any
            steps {
                script {
                    withCredentials([file(credentialsId: 'wushsecret', variable: 'SSH_KEY')]) {
                        sh "ssh -i $SSH_KEY ${EC2_USER}@${EC2_HOST} 'docker pull ${DOCKER_IMAGE}'"
                        sh "ssh -i $SSH_KEY ${EC2_USER}@${EC2_HOST} 'docker run -d -p 3000:3000 ${DOCKER_IMAGE}'"
                    }
                }
            }
        }
        stage('Stop Container on EC2') {
            steps {
                input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)'
                script {
                    withCredentials([file(credentialsId: 'wushsecret', variable: 'SSH_KEY')]) {
                        sh "ssh -i $SSH_KEY ${EC2_USER}@${EC2_HOST} 'docker stop \$(docker ps -q --filter ancestor=${DOCKER_IMAGE})'"
                    }
                }
            }
        }
    }
}
