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
        stage('osv scanner') {
            steps {
                sh '''
                    osv-scanner scan --lockfile package-lock.json --format json --output results/sca-osv-scanner.json || true
                '''
            }
        }
    }
    post {
        always {
            echo "archiveArtifacts"
            archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
            echo "sending reports to DefectDojo"
            defectDojoPublisher(artifact: 'results/sca-osv-scanner.json', 
                    productName: 'Juice Shop', 
                    scanType: 'OSV Scan', 
                    engagementName: 'pawel.polakiewicz@fabrity.pl')
        }
    }
}