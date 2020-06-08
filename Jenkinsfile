pipeline {
    environment {
        registry = "docker.io/tathagatk22/node-fibo"
        registryCredential = 'docker-tathagatk22'
        githubRepo = "https://github.com/tathagatk22/node-fibonacci.git"
        dockerImage = ''
    }
    agent any
    parameters {
        choice(
            choices:
            [
                'True',
                'False'
            ],
            description: 'Please select True to deploy this build on Kubernetes Cluster.',
            name: 'deployment_value'
        )
		choice(
            choices:
            [
                'branch',
                'commit'
            ],
            description: 'Please select branch or commit to checkout from.',
            name: 'selection'
        )
		string(
            description: 'Please insert valid input',
            name: 'selection_value'
        )
	}
    stages {
        stage('Git checkout from Branch') {
            when {
                expression { params.selection == 'branch' }
            }
            steps {
                git url: "$githubRepo", branch: "${selection_value}", credentialsId: 'git-tathagatk22'
            }
        }
        stage('Git checkout from Commit') {
            when {
                expression { params.selection == 'commit' }
            }
            steps {
                git url: "$githubRepo", credentialsId: 'git-tathagatk22'
                sh 'git checkout ' + "${selection_value}"
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Building image') {
            environment {
               COMMIT = sh(script: 'git rev-parse --short HEAD', , returnStdout: true).trim()
               BRANCH = sh(script: 'git rev-parse --abbrev-ref HEAD', , returnStdout: true).trim()
           }
            steps{
                script {
                    dockerImage = docker.build("$registry:$BUILD_NUMBER", "--build-arg COMMIT=${COMMIT}  --build-arg BRANCH=${BRANCH} -f Dockerfile ." )
                }
            }
        }
        stage('Publish Image') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Delete Image') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
        stage('Deploying on Kubernetes'){
            when {
                    expression { params.deployment_value == 'True' }
                }
            steps{
                script {
                    git url: "$githubDeployRepo"
                    withCredentials([usernamePassword(credentialsId: "${registryCredential}", usernameVariable: 'username', passwordVariable: 'password')])
                    {
                        sh (script: "kubectl delete secret --ignore-not-found regcred")
                        sh (script: "kubectl create secret docker-registry regcred --docker-server=docker.io --docker-username=$username --docker-password=\"$password\" ")
                        sh (script: "kubectl apply -f .")
                        sh (script: "kubectl set image deployment/sample-django-deployment sample-django=$registry:$BUILD_NUMBER --record")
                    }
                }
            }
        }
    }
}
