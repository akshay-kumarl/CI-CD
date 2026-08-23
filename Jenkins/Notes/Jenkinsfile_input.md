```
pipeline {
 agent any
 stages {
 stage('Approval') {
 steps {
 input 'Do you want to deploy to production?'
 }
 }
 stage('deployed'){
     steps{
         echo "deployed"
     }
 }
 }
 }

```

---

### sample jenkins file with input

```
stages {
        stage('Input Stage') {
            steps {
                script {
                    // Ask for user input to select between "Blue" or "Green"
                    def userInput = input(
                        id: 'userInput', // Unique ID for the input step
                        message: 'Select a stage to run:',
                        parameters: [
                            choice(name: 'Color', choices: ['Blue', 'Green'], description: 'Choose a stage to execute')
                        ]
                    )

                    // Store the user's selection
                    env.SELECTED_COLOR = userInput
                }
            }
        }
```
