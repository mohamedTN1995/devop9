
def commit_id
pipeline {
    agent any

    stages {
        stage('preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage('build') {
            steps {
                echo 'Building maven workload'
                sh "mvn clean install"
                echo 'build complete'
            }
        }
        stage('image build') {
            steps {
                echo 'building docker image'
                sh "docker build -t 2alinfo7/position-simulator:${commit_id} ."
                echo 'docker image build'
            }
        }
        stage('image push') {
            steps {
                echo 'pushing docker image'
                sh "docker push 2alinfo7/position-simulator:${commit_id} "
                echo 'docker image pushed'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                sh "sed -i ir 's|richardchesterwood/k8s-fleetman-position-simulator:release2|2alinfo7/position-simulator:${commit_id}|' workloads.yaml"
                sh 'kubectl apply -f workloads.yaml --namespace dev9'
            }
        }
    }
}

