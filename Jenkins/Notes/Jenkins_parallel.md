```
pipeline {
    agent any
    
    stages {
        stage('Preparation') {
            steps {
                echo 'Preparing environment...'
            }
        }
        
        stage('Parallel Execution') {
            parallel {
                stage('Test A') {
                    steps {
                        echo 'Running Test A'
                        // Example of long-running task
                        sh 'sleep 5'
                    }
                }
                stage('Test B') {
                    steps {
                        echo 'Running Test B'
                        // Another long-running task
                        sh 'sleep 3'
                    }
                }
                stage('Test C') {
                    steps {
                        echo 'Running Test C'
                        // Simulate a different task
                        sh 'sleep 7'
                    }
                }
            }
        }
        
        stage('Final Stage') {
            steps {
                echo 'Final cleanup or steps...'
            }
        }
    }
}
```
