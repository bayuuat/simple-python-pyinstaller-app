node {
    def buildImage = 'python:2-alpine'
    def buildDir = 'sources'

    try {
        stage('Build') {
            docker.image(buildImage).inside {
                sh "python -m py_compile ${buildDir}/add2vals.py ${buildDir}/calc.py"
            }
        }
    } catch (e) {
        echo "Build stage failed: ${e.message}"
        currentBuild.result = 'FAILURE'
        throw e
    }
}
