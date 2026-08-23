

# parameter jenkins file

```

pipeline{
    agent any
    
    parameters {
        choice(name: 'action', choices: ['destroy', 'apply'], description: 'Select the Terraform action to perform')
    }

    tools {
        terraform 'tf'
        dockerTool 'docker'
    }
    
    
    environment {
        AWS_ACCESS_KEY_ID = credentials('0002')  // Replace with your credentials ID
        AWS_SECRET_ACCESS_KEY = credentials('0002')  // Same as above
        AWS_REGION = 'ap-south-1'  // Replace with your desired AWS region
    }

    stages{
        stage("git checkout"){
            steps{
                git credentialsId: '0001', url: 'https://github.com/akshay-kumarl/sampleproj.git', branch: 'mainbranch'
            }
        }
        
        stage('terraform initialise'){
          steps{
            sh 'terraform init'
          }
        }
        
        stage('terraform plan'){
          steps{
            sh 'terraform plan -out=tfplan'
          }
        }
        
        stage('terraform apply'){
            steps{
               echo "Terraform action is --> ${action}"
                sh ("terraform ${action} --auto-approve tfplan")   
            }
        }
    }
}
```







---

## Complicated sample Jenkinsfile


```
pipeline {
    agent any
   // agent { label 'slave-1'}

    tools {
        terraform 'terraform_1.9.6'  // Name from the Global Tool Configuration
    }

    environment {
        AWS_CREDENTIALS_ID = 'aws-credentials'   // ID of the AWS credentials stored in Jenkins
        TF_WORKSPACE = 'terraform-workspace'     // Workspace for Terraform files
    }

    stages {
        stage('Checkout') {
            steps {
                // Assuming the Terraform script is in the repository
                git branch: 'main', url: 'https://github.com/your-repo/terraform-aws-ec2.git'
            }
        }
        stage('Init Terraform') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID]]) {
                    sh 'terraform init'
                }
            }
        }
        stage('Plan Terraform') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID]]) {
                    sh 'terraform plan -out=tfplan'
                }
            }
        }
        stage('Apply Terraform') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID]]) {
                    // Apply the Terraform plan
                echo "Terraform action is --> ${action}"
                sh ('terraform ${action} --auto-approve tfplan') 
                }
            }
        }
    }

    post {
        always {
            echo "Terraform operation complete!"
        }
    }
}
```

---

### Sample Jenkinsfile for node application  

```
pipeline {
    agent any
    environment {
        NODE_VERSION = 'NodeJS 14' // Name given in Jenkins for NodeJS installation
    }
    tools {
        nodejs "${NODE_VERSION}"
    }
    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/your-repository.git'
            }
        }
        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true
            }
        }
    }
    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}
```
