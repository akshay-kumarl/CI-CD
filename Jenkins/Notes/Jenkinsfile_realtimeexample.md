# General Structure of a Jenkins Declarative Pipeline


```
pipeline {
    agent any //agent {label 'sample'}
    
    environment {
        // Define environment variables here
        NODE_VERSION = '14.17.0'  // Example: Node.js version
        DOCKER_IMAGE = 'my-app:latest' // Example: Docker image tag
        ANSIBLE_HOME = '/usr/local/bin/ansible' // Example: Ansible path
        SCANNER_HOME= tool 'sonarqube'
    }

    tools {
        // Define the tools needed, such as Node.js, Maven, JDK, etc.
        // tool tool-name-given-in-the-jenkins-while-installing
        nodejs 'Node'
        dockerTool 'Docker'
        ansible 'Ansible'  // Assuming Ansible is installed and configured as a Jenkins tool
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull the code from Git repository
                git branch: 'main', url: 'https://github.com/your-repo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install project dependencies (Example: Node.js)
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                // Build the project (can vary based on the language and tool)
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                // Run tests
                //sh 'npm test'
                echo 'test'
            }
        }

        stage('Docker Build') {
            steps {
                // Build Docker image
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                // Invoke an Ansible playbook to deploy
                ansiblePlaybook playbook: 'deploy.yml', inventory: 'inventory.ini'
            }
        }

        stage('Trivy scan') {
            steps {
                sh 'trivy image -f table -o fs.html sample:v1'
                // sh "trivy image sample:v1 fs --format table -o fs.html ."
               //sh 'trivy image -f json -o /home/ubuntu/trivyresults/results.json sample:v1'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            // Actions that should always run, like sending notifications or cleanup
            cleanWs() // Clean up workspace
        }
    }
}
```
