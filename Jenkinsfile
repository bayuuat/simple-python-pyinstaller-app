node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
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
        docker.image('python:2-alpine').inside {
            try {
                sh '''
                    apt-get update
                    apt-get install -y python-dev
                    pip install pyinstaller==3.6
                '''
                
                sh 'pyinstaller --onefile sources/add2vals.py'
                
                archiveArtifacts 'dist/add2vals'
                
                echo 'Application will run for 1 minute...'
                sleep(time: 60, unit: 'SECONDS')
                
                echo 'Application execution completed'
            } catch (Exception e) {
                error "Deployment failed: ${e.getMessage()}"
            }
        }
    }
}