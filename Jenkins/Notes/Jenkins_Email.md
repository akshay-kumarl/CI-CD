TO SEND EMAIL REGARDING JOB STATUS FROM JENKINS  ++++++++++++++++++++


-> install EMAIL EXTENSION PLUGIN 
-> go to manage jenkins 
-> configure system 
-> extended email notification

=> add email extension server 
=> we will add company provided SMTP server details

-> Once SMTP properties added then we can configure email notification as 'Post build action' in jenkins job


### for pipeline

```
pipeline {
 agent any 
 stages {
   stage('hello'){
   steps {
    echo "hello world"
   }
  }
 }
post{
 always{
   mail to: "abc@gmail.com",
   subject: "",
   body: ""
  }
 }
}
```


### to configure Jenkins with outlook follow bellow PDF 

Jenkins Outlook mail config.pdf > see this pdf in this folder
