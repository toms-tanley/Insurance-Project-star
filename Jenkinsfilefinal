pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/toms-tanley/Insurance-Project-star.git'
            }
        }

        stage('Code Build') {
            steps {
                sh 'mvn clean package' // Build the application
            }
        }

        stage('Code Test') {
            steps {
                script {
                   sh 'mvn test' 
                }
                
            }
        }

        stage('Docker Image Build') {
            steps {
                sh 'docker build -t insureme_project123:latest .' // Build Docker image
            }
        }

        stage('Deploy to the Test Server') {
            steps {
                sh 'docker run -d -p 8082:8081 insureme_project123:latest' // Run Docker container
                echo "Application is successfully running"
                sh 'docker rm -f $(docker ps -a -q)' // Stopping the Running Docker container
            }
        }

        stage('Docker Login') {
            steps {
                echo 'Docker Login'
                withCredentials([usernamePassword(credentialsId: 'Dockerlogin', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                }
            }
        }

        stage('Push Docker Image to Hub') {
            steps {
                echo 'Push a Docker Image'
                sh 'docker tag insureme_project123 thomasdevops003/insureme_project123'
                sh 'docker push thomasdevops003/insureme_project123'
                sh 'docker rmi -f $(docker images -aq)' // Cleaning up the Image
            }
        }

        stage('Deployment using Ansible') {
            steps {
                ansiblePlaybook become: true, credentialsId: 'ansible1-ssh', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
            }
        }
    }
}
