# Run Stages on Different Agents

## Declarative Pipeline

### Sequential Stages
```groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            agent { label 'linux' }
            steps {
                checkout scm
                sh 'mvn clean compile'
                stash includes: 'target/**', name: 'build-artifacts'
            }
        }
        
        stage('Test') {
            agent { label 'docker' }
            steps {
                unstash 'build-artifacts'
                sh 'docker run --rm -v $(pwd):/app test-image'
            }
        }
        
        stage('Deploy') {
            agent { label 'production' }
            steps {
                unstash 'build-artifacts'
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
```

### Parallel Stages
```groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            agent { label 'linux' }
            steps {
                checkout scm
                sh 'mvn package'
                stash includes: '**/*.jar', name: 'build-output'
            }
        }
        
        stage('Parallel Tests') {
            parallel {
                stage('Unit Tests') {
                    agent { label 'linux' }
                    steps {
                        unstash 'build-output'
                        sh 'mvn test'
                    }
                }
                stage('Integration Tests') {
                    agent { label 'docker' }
                    steps {
                        unstash 'build-output'
                        sh 'docker-compose up --abort-on-container-exit'
                    }
                }
            }
        }
    }
}
```

## Stash/Unstash for File Transfer
```groovy
// Build stage on first agent
stage('Build') {
    agent { label 'build-agent' }
    steps {
        checkout scm
        sh 'make build'
        stash includes: 'dist/**', name: 'build-output'
    }
}

// Deploy stage on different agent
stage('Deploy') {
    agent { label 'deploy-agent' }
    steps {
        unstash 'build-output'
        sh 'deploy.sh'
    }
}
```

## Common Use Cases

### Build → Test → Deploy Pipeline
```groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            agent { label 'maven' }
            steps {
                checkout scm
                sh 'mvn package'
                stash includes: '**/*.jar', name: 'jar-files'
            }
        }
        
        stage('Test') {
            agent { label 'docker' }
            steps {
                unstash 'jar-files'
                sh 'docker run --rm -v $(pwd):/app maven:test'
            }
        }
        
        stage('Deploy') {
            agent { label 'k8s' }
            steps {
                unstash 'jar-files'
                sh 'kubectl create deployment app --image=myapp'
            }
        }
    }
}
```