# Jenkins Pipeline Exercise

This repository contains a simple Jenkins pipeline that demonstrates how to use scripted loops in a declarative pipeline.

## Pipeline Description

The pipeline consists of three stages that perform the following operations:

1. **Generate Stage**:
   - Creates 5 empty text files named `1.txt` through `5.txt`

2. **Populate Stage**:
   - Writes content to each file ("this is file X" where X is the file number)

3. **Validate Stage**:
   - Outputs the contents of each file to the console log

## Pipeline Code

```groovy
pipeline {
    agent any
    stages {
        stage("Generate") {
            steps {
                script {
                    def numbers = [1, 2, 3, 4, 5]
                    for (int i in numbers) {
                        sh "touch ${i}.txt"
                    }
                }
            }
        }
        stage("Populate") {
            steps {
                script {
                    def numbers = [1, 2, 3, 4, 5]
                    for (int i in numbers) {
                        sh "echo 'this is file ${i}' > ${i}.txt"
                    }
                }
            }
        }
        stage("Validate") {
            steps {
                script {
                    def numbers = [1, 2, 3, 4, 5]
                    for (int i in numbers) {
                        sh "cat ${i}.txt"
                    }
                }
            }
        }
    }
}
```

## Purpose

This pipeline serves as an exercise to demonstrate:
- Using scripted steps within a declarative pipeline
- Iterating through lists in Groovy
- File operations in Jenkins pipelines
- Basic shell command execution (`touch`, `echo`, `cat`)

## Requirements

- Jenkins server with pipeline plugin installed
- Agent with shell access (Unix-like system recommended for these commands)

## How to Use

1. Create a new pipeline job in Jenkins
2. Paste the pipeline code into the pipeline definition
3. Run the pipeline
4. Observe the console output to see the file operations and contents
