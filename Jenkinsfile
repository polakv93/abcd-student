pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-pat', url: 'https://github.com/polakv93/abcd-student', branch: 'main'
                }
            }
        }
        stage('Prepare') {
            steps {
                sh 'mkdir -p results'
            }
        }
        stage('trufflehog') {
            steps {
                sh '''
                    trufflehog git file://. --since-commit main --only-verified
                '''
            }
        }
    }
    post {
        always {
            echo "archiveArtifacts"
            archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
            // echo "sending reports to DefectDojo"
            // defectDojoPublisher(artifact: 'results/trufflehog.json', 
            //         productName: 'Juice Shop', 
            //         scanType: 'Trufflehog Scan', 
            //         engagementName: 'pawel.polakiewicz@fabrity.pl')
        }
    }
}