pipeline {
    agent { label 'first_agent' }  // Set the default agent for the entire pipeline
    environment {
    imagename = "rputhenp/hello_app"
    registryCredential = "riyas-dockerhub"
    dockerImage = ''
    }
    stages {
        stage('Clone repository') {
            steps {
                // Checkout the source code from the SCM (Source Control Management) repository.
                checkout scm
            }
        }
        
        stage('Build docker image') {
            steps {
                script {
                    dockerImage = docker.build imagename
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                    }
                }
            }
        }
         
        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $imagename:$BUILD_NUMBER"
                sh "docker rmi $imagename:latest"
            }
        }

        stage('Start Container') {
            steps {
                script {
                sh "docker stop $imagename || true"
                sh "docker rm $imagename || true"
                sh "docker run -i $imagename:$BUILD_NUMBER python hello.pydocker"
                //sh "docker run -d $imagename:$BUILD_NUMBER"
                }
            }
        }
   
        stage('Run test script on the app') {
            steps {
                script {
                    // Run tests inside the Docker container.
                    // The app.inside block runs commands inside a container based on the built image.
                    inside {
                        //sh 'python hello.py'  // Placeholder for actual tests; replace with real test commands
                    }
                }
            }
        }
        
    }
}