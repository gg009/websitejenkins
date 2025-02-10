pipeline {
    agent any

    environment {
        registryCredential = 'ecr:us-west-1:0a766873-8762-4970-a8ca-887e2a7dc84c'   // AWS ECR Credentials ID
        appRegistry = "samplerepototest"                              // ECR Repository Name
        capstoneRegistry = "739275458037.dkr.ecr.us-west-1.amazonaws.com/samplerepototest" // ECR Registry URL
        cluster = "sampleclustertotestjenkins"                        // ECS Cluster Name
        service = "sampletestsvc"                                     // ECS Service Name
    }

    stages {
        stage('Clone Website') {
            steps {
                git url: 'https://github.com/gg009/websitejenkins.git'  // GitHub Repository URL
            }
        }

        stage("Docker Build Image") {
            steps {
                script {
                    dockerImage = docker.build(appRegistry + ":$BUILD_NUMBER", ".")  // Build Docker image
                }
            }
        }

        stage('Test Website') {
            steps {
                // Add your test commands here (e.g., run automated tests)
                sh 'echo "Running tests on the website"'
            }
        }

        stage("Upload App Image") {
            steps {
                script {
                    docker.withRegistry(capstoneRegistry, registryCredential) {
                        dockerImage.push("$BUILD_NUMBER")  // Push the image with the build number tag
                        dockerImage.push('latest')         // Push the image with the 'latest' tag
                    }
                }
            }
        }

        stage('Deploy to ECS') {
            when {
                branch "master"  // Only deploy when on the master branch
            }
            steps {
                withAWS(credentials: '0a766873-8762-4970-a8ca-887e2a7dc84c', region: 'us-west-1') {  // AWS Credentials ID for ECS
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }
    }
}
