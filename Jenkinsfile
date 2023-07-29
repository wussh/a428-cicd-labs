pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                script {
                    sleep(time: 1, unit: 'MINUTES')
                }
                input(message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)')
            }
        }
    }
    post {
        always {
            sh './jenkins/scripts/kill.sh'
        }
    }
}
