pipeline {
agent none
stages {
stage('Build') {
agent {
docker {
image 'python:3.9.15-slim-bullseye'
}
}
steps {
sh 'python3 -m py_compile sources/main.py'
stash(name: 'compiled-results', includes: 'sources/*.py*')
}
}

stage('Test') {
agent {
docker {
image 'safesecurity/pytest'
}
}
steps {
sh 'py.test --junit-xml test-reports/results.xml sources/test_game.py'
}
post {
always {
junit 'test-reports/results.xml'
}
}
}
stage('Deliver') {
agent any
environment {
VOLUME = '$(pwd)/sources:/src'
IMAGE = 'cdrx/pyinstaller-linux:python3'
}
steps {
dir(path: env.BUILD_ID) {
unstash(name: 'compiled-results')
sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F main.py'"
}
}
post {
success {
archiveArtifacts "${env.BUILD_ID}/sources/dist/main"
sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
}
}
}

}
}