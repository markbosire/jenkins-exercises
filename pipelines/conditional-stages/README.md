# **Conditional Jenkins Pipeline Example**  
This project demonstrates a simple Jenkins pipeline with a **conditional stage** that runs based on a boolean parameter (`DEBUG`).  

## **Pipeline Overview**  
The pipeline:  
- Uses a `booleanParam` (`DEBUG`) to control execution.  
- Only runs the `"Test"` stage if `DEBUG` is `true`.  
- Prints `"Hello World!"` when the condition is met.  

## **Jenkinsfile**  
```groovy
pipeline {
    agent any
    parameters {
        booleanParam(name: "DEBUG", defaultValue: true)
    }
    stages {
        stage("Test") {
            when {
                expression {
                    return params.DEBUG
                }
            }
            steps {
                echo "Hello World!"
            }
        }
    }
}
```

## **How to Use**  
1. **Run the Pipeline:**  
   - Jenkins will prompt you to set `DEBUG` (default: `true`).  
   - If `DEBUG=true`, the `"Test"` stage executes.  
   - If `DEBUG=false`, the stage is skipped.  

2. **Customize:**  
   - Replace `echo` with actual test commands (e.g., `sh 'pytest'`).  
   - Add more stages with different conditions (e.g., branch-based logic).  

## **Key Concepts**  
✔ **Conditional Execution**: Uses `when { expression { ... } }` to check `params.DEBUG`.  
✔ **Parameterization**: Allows runtime control via Jenkins UI/API.  

---

### **Improvement Ideas**  
- Add a `post` section for failure/success notifications.  
- Extend with branch-based conditions (e.g., `branch 'main'`).  


