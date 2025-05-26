# **Converting Freestyle Project to Jenkins Pipeline**  

This guide explains how to migrate the **`core-jobs/freestyle-job`** (a temperature and currency rate checker) from a **Freestyle Project** to a **Declarative Pipeline**. The new Pipeline is defined in a **`Jenkinsfile`** and provides better structure, error handling, and scalability.  

---

## **Prerequisites**  
Before converting, ensure:  

✅ **Jenkins Plugins Installed:**  
- **Pipeline** (`workflow-aggregator`)  
- **Durable Task** (for long-running steps)  

✅ **Tools Available on Jenkins Agents:**  
- `curl`  
- `jq`  
- (Optional) `bc` or `awk` (if doing advanced math in scripts)  

✅ **Access to APIs:**  
- Open-Meteo (for temperature)  
- ER-API (for USD-KES rates)  

---

## **Key Differences: Freestyle vs. Pipeline**  

| Feature | Freestyle Project | Pipeline (`Jenkinsfile`) |  
|---------|------------------|------------------------|  
| **Structure** | Manual steps in UI | Code-defined (`stages`, `steps`) |  
| **Error Handling** | Basic shell exits | Granular stage failures |  
| **Reusability** | Hard to version | Stored in SCM (Git) |  
| **Logging** | Single console log | Per-stage logs |  
| **Restartability** | Full rebuild | Resume from failed stage |  

---

## **Steps to Convert**  

### **1. Create Pipeline (same one as the Jenkinsfile)
```groovy
pipeline {
    agent any
    environment {
        TEMP = sh(returnStdout: true, script: 'curl -s "https://api.open-meteo.com/v1/forecast?latitude=-1.4761&longitude=36.9614&daily=temperature_2m_max&timezone=Africa%2FCairo&start_date=2025-05-26&end_date=2025-05-26" | jq -r .daily.temperature_2m_max[0]').trim()
        RATE = sh(returnStdout: true, script: 'curl -s "https://open.er-api.com/v6/latest/USD" | jq -r .rates.KES').trim()
    }
    stages {
        stage('Temperature') {
            steps {
                script {
                    def temp = env.TEMP.toFloat()
                    if (temp > 30) {
                        echo "Too hot! Temp: ${temp}°C"
                        error("Temperature too high")
                    } else {
                        echo "Good Temp. Temp: ${temp}°C"
                    }
                }
            }
        }
        stage('Rate') {
            steps {
                script {
                    def rate = env.RATE.toFloat()
                    if (rate > 130) {
                        echo "KES rate is bad! Rate: ${rate} KES/USD"
                        error("Exchange rate too high")
                    } else {
                        echo "KES rate is good. Rate: ${rate} KES/USD"
                    }
                }
            }
        }
    }
}
```

---

### **2. Set Up Pipeline Job in Jenkins**  

1. **Create a New Pipeline Job:**  
   - Go to **Jenkins Dashboard** → **New Item** → **Pipeline**.  
   - Name it.  

2. **Configure Pipeline Source:**  
   - Under **Pipeline**, select:
	Pipeline script
3. **Save and Run:**  
   - Click **Save**, then **Build Now**.  

---

### **3. Expected Output**  

#### **Success Case**  
```plaintext
[Temperature Check]  Temperature OK: 25.7°C  
[Exchange Rate Check]  KES rate OK: 129.2 KES/USD  
[Post-Build] Build completed. See reports above.  
```

#### **Failure Case**  
```plaintext
[Temperature Check] Too hot! Temp: 31.5°C  
[ERROR] Temperature exceeds 30°C  
[Post-Build]  Build failed! Check stage logs for details.  
```

---

## **Benefits of Pipeline**  

✔ **Version Control:** Track changes via Git.  
✔ **Modularity:** Split logic into stages (e.g., temp vs. rate).  
✔ **Failure Isolation:** One stage fails without stopping others (if parallel).  
✔ **Advanced Features:** Retries, timeouts, artifacts, and notifications.  

---

## **Troubleshooting**  

| Issue | Solution |  
|-------|----------|  
| `sh: curl: not found` | Install `curl` on Jenkins agents. |  
| `jq: command not found` | Install `jq` (`apt install jq`). |  
| `java.lang.NumberFormatException` | Ensure API responses are valid numbers. |  
| Pipeline not triggering | Check SCM polling/webhook settings. |  




