node {
    def buildImage = 'python:2-alpine'
    def buildDir = 'workspaces/sources'

    try {
        stage('Build') {
            docker.image(buildImage).inside {
                sh 'ls'
                sh 'ls -la /workspace/sources/'
                sh 'python -m py_compile /workspace/sources/add2vals.py /workspace/sources/calc.py'
            }
        }
    } catch (e) {
        echo "Build stage failed: ${e.message}"
        currentBuild.result = 'FAILURE'
        throw e
    }
}
