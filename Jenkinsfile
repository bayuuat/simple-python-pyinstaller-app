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
                sh 'pip install cx-Freeze==5.1.1' 
                
                writeFile file: 'setup.py', text: """ 
                from cx_Freeze import setup, Executable 
                 
                setup( 
                    name='add2vals', 
                    version='0.1', 
                    description='Sample application', 
                    executables=[Executable('sources/add2vals.py')]
                ) 
                """ 
                 
                sh 'python setup.py build' 

                archiveArtifacts 'build/exe.linux-x86_64-2.7/add2vals'

                echo 'Application will run for 1 minute...'
                sleep(time: 60, unit: 'SECONDS')

                echo 'Application execution completed'
            } catch (Exception e) {
                error "Deployment failed: ${e.getMessage()}"
            }
        }
    }
}