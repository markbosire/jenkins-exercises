# Jenkins Pipeline: Post-Build Actions Example

This Jenkins Pipeline demonstrates how to use **post-build actions** to define tasks that run **after the main pipeline stages**, depending on the build result (e.g., success or failure).

## 📄 Pipeline Script

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'exit 1'
            }
        }
    }
    post {
        success {
            echo 'I succeeded!'
        }        
        failure {
            echo 'I failed :('
        }
    }
}
```

## 🔍 What This Does

* **Stage: Build**

  * Runs a shell command: `exit 1` — this always causes the stage (and the pipeline) to **fail**.
* **Post Section**

  * **`success` block**: Executes only if the pipeline completes successfully.
  * **`failure` block**: Executes only if the pipeline fails.

In this case, since `exit 1` triggers a failure, you'll see this in the console output:

```
[Pipeline] echo
I failed :(
```

## 💡 Key Takeaways

* The `post` block is useful for:

  * Sending notifications
  * Cleaning up resources
  * Archiving artifacts
  * Logging success/failure events

## 🧪 How to Use

1. Create or open a pipeline job in Jenkins.
2. Paste this script into the Pipeline definition section.
3. Run the job and observe the **post-build behavior** based on pipeline success or failure.
