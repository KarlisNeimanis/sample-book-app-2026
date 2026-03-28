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
                    deploy("dev", 1010)
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
                    deploy("stg", 2020)
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
                    deploy("prd", 3030)
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
    bat "npm install"
    echo "Pushing image to docker registry.." 
}

def deploy(String environment, int port){
    echo "Deployment to ${environment} environment has started.."
    bat "npx node_modules//.bin//pm2 delete \"books-${environment}\" || exit 0"
    bat "node_modules//.bin//pm2 start -n \"books-${environment}\" index.js -- ${port}"
    //bat "node_modules//.bin//pm2 reload -n \"books-${environment}\" index.js -- ${port}"
    bat "dir"
    echo "Deployment to ${environment} environment finished.."
}

def test(String environment){
    echo "Testing Sample Book Application service has started on ${environment} environment.."
    git branch: 'main', poll: false, url: 'https://github.com/KarlisNeimanis/RTU-sample-API-automation-2026.git'
    echo "Testing Sample Book Application service finished.."
}
