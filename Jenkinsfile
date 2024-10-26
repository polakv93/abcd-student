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
        stage('Semgrep') {
            steps {
                sh '''
                    semgrep scan --config auto . --json --output results/semgrep.json
                    cat results/semgrep.json
                '''
            }
        }
    }
    post {
        always {
            echo "archiveArtifacts"
            archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
            echo "sending reports to DefectDojo"
            defectDojoPublisher(artifact: 'results/semgrep.json', 
                    productName: 'Juice Shop', 
                    scanType: 'Semgrep JSON Report', 
                    engagementName: 'pawel.polakiewicz@fabrity.pl')
        }
    }
}