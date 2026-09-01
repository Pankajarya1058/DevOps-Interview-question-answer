## Q1. What is Jenkins?
Jenkins is a open source automation server, which is mainly used for CI/CD. with the help of jenkins we can build, test, and deploy code automatically. 

For example: When developer push the code in Git, Jenkins automatically build and run the tests and if everything looks good then it can deploy also.

## Q2. What is Jenkins Pipeline?
Jenkins Pipeline is a automated process, In which we define the complete CI/CD steps such as code checkout, build, run tests, create artifact and deploy. Generally we define the Pipeline in Jenkinsfile, which maintain in source code repository.

## Q3. What is the difference between Declarative Pipeline and Scripted Pipeline?
**Declarative:** It is simple, readable, structured and has predefined syntax. that's why it is easy for beginners. we use predefined blocks such as pipeline, stages, steps, post.

**Scripted:** It provide more flexibility because It use groovy-based scripting. It is useful if you want to implement complex logic, conditions, loops or dynamic behavior.

- **Declarative** = Easy, structured, less complex
- **Scripted** = Flexible, powerful, complex logic ke liye

## Q4. Can you show me a basic example of a Declarative Jenkins Pipeline?
```bash
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

## Q5. What is the purpose of the agent directive in a Jenkins Declarative Pipeline?
The purpose of the agent directive is to decide on which Jenkins node/agent a Jenkins pipeline or a particular stage should be executed.

## Q6. What is the difference between a Jenkins Controller and a Jenkins Agent?
