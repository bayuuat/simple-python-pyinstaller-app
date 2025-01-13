node {
    stage('Build') {
        docker.image('python:3-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }
    
    stage('Approval') {
        input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
    }
    
    stage('Deploy') {
        docker.image('python:3-slim').inside('-u root') {
            try {
                sh 'apt-get update && apt-get install -y gcc binutils'

                sh 'pip install --upgrade pip setuptools wheel'

                sh 'pip install pyinstaller'

                sh 'pyinstaller --onefile sources/add2vals.py'

                archiveArtifacts 'dist/add2vals'

                // echo 'Application will run for 1 minute...'
                // sleep(time: 60, unit: 'SECONDS')

                echo 'Application execution completed'
            } catch (Exception e) {
                error "Deployment failed: ${e.getMessage()}"
            }
        }
    }

    stage('Approval EC2') {
        input message: 'Lanjutkan Deploy ke EC2?', ok: 'Proceed'
    }

    stage('Deploy to EC2') {
        try {
            sshPublisher(
                publishers: [
                    sshPublisherDesc(
                        configName: 'EC2-Server',
                        transfers: [
                            sshTransfer(
                                sourceFiles: 'dist/add2vals',
                                remoteDirectory: '/home/ubuntu/app',
                                execCommand: '''
                                    cd /home/ubuntu/app
                                    chmod +x add2vals
                                    ./add2vals
                                '''
                            )
                        ]
                    )
                ]
            )
        } catch (Exception e) {
            error "EC2 deployment failed: ${e.getMessage()}"
        }
    }
}