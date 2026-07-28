
## Jenkins

Jenkinsfile ==> file name

### Declarative Pipeline 

sample declarative file
```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                sh 'mvn deploy'
            }
        }
    }
    post {
        always {
            echo 'Pipeline completed'
        }
    }
}

```


---

### Scripted Pipeline
sample scripted pipeline

```
node {
    stage('Build') {
        sh 'mvn clean install'
    }
    stage('Test') {
        parallel(
            'Unit Tests': {
                sh 'mvn test'
            },
            'Integration Tests': {
                sh 'mvn integration-test'
            }
        )
    }
    stage('Deploy') {
          echo 'Deploying....'
    }
    post {
        always {
            echo 'Pipeline completed'
        }
    }
}
```



---

cleans()

```
pipeline {
    agent any
    options {
        skipDefaultCheckout(true) // Disables automatic checkout
    }
    stages {
        stage('Build') {
            steps {
                cleanWs() // Cleans the workspace before checkout
                checkout scm // You must now explicitly check out your code
                // ... rest of your build steps
            }
        }
    }
}


```

