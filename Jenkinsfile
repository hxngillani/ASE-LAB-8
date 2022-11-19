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
}
}