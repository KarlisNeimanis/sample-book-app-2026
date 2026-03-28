pipeline {
    agent any
    triggers { 
        pollSCM('*/1 * * * *') 
    }
    stages {
        stage('build-install-deps') {
            when {
                not {
                    changeset "README.md"
                }
            }
            steps {
                script{
                    build()
                }
            }
        }
        stage('deploy-dev') {
            steps {
                script{
                    deploy("dev")
                }
            }
        }
        stage('test-dev') {
            steps {
                script{
                    test("DEV")
                }
            }
        }
        stage('deploy-stg') {
            steps {
                script{
                    deploy("stg")
                }
            }
        }
        stage('test-stg') {
            steps {
                script{
                    test("STG")
                }
            }
        }
        stage('deploy-prd') {
            steps {
                script{
                    deploy("prd")
                }
            }
        }
        stage('test-prd') {
            steps {
                script{
                    test("PRD")
                }
            }
        }
    }
}

def build(){
    echo "Building sample-book-app.." 
    sh "docker build --no-cache -t mtararujs/sample-book-app ."
   
    echo "Pushing image to docker registry.." 
    sh "docker push mtararujs/sample-book-app"
}

def deploy(String environment){
    echo "Deployment to ${environment} environment has started.."
    sh "docker pull mtararujs/sample-book-app"
    sh "docker compose stop sample-book-app-${environment}"
    sh "docker compose rm sample-book-app-${environment}"
    sh "docker compose up -d sample-book-app-${environment}"
    echo "Deployment to ${environment} environment finished.."
}

def test(String environment){
    echo "Testing Sample Book Application service has started on ${environment} environment.."
    sh "docker pull mtararujs/api-tests:latest"
    def directory = pwd()
    sh "echo '${directory}'"
    sh "docker run --rm --network sample-book-app-compose-network -v '${directory}':/api-tests/mochawesome-report mtararujs/api-tests books BOOKS_${environment}"
    sh "ls"
    archiveArtifacts allowEmptyArchive: true, artifacts: 'mochawesome.json', followSymlinks: false
    echo "Testing Sample Book Application service finished.."
}
