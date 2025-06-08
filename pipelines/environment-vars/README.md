# Jenkins Pipeline Environmental variable Exercise

This is a basic Jenkins Pipeline script demonstrating the use of:

* **Pipeline syntax**
* **Stage definition**
* **Environment variable declaration**
* **Shell command execution inside a step**

## 📄 Pipeline Overview

```groovy
pipeline {
    agent any
    stages {
        stage('confirmation') {
            environment {
                user = 'vagrant'
            }
            steps {
                sh "echo `whoami`s real name is ${env.user}"
            }
        }
    }
}
```

## 🔍 Explanation

* **`agent any`**: This tells Jenkins to run the pipeline on any available agent.
* **`environment` block**: Sets the environment variable `user` to `"vagrant"` for the stage.
* **`sh` step**: Executes a shell command that combines the output of `whoami` with the value of the `user` environment variable.

For example, if the current OS user is `jenkins`, the output will be:

```bash
jenkinss real name is vagrant
```

> Note: The use of backticks ( \` \` ) around `whoami` executes the command in a subshell.

## 🧪 Running This

1. Create a Jenkins pipeline job.
2. Paste the pipeline code into the pipeline definition.
3. Run the job and view the console output.

