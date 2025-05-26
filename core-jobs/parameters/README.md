# **Jenkins Pipeline: Configurable Variables with parameters**
This guide explains how to add parameters to this project [Convert Freestyle to Pipeline Job](/core-jobs/convert-to-pipeline/). The new Pipeline is defined in a **`Jenkinsfile`** .  

## **Pipeline Definition**
```groovy
pipeline {
    agent any
    environment {
        TEMP = sh(returnStdout: true, script: 'curl -s "https://api.open-meteo.com/v1/forecast?latitude=-1.4761&longitude=36.9614&daily=temperature_2m_max&timezone=Africa%2FCairo&start_date=2025-05-26&end_date=2025-05-26" | jq -r .daily.temperature_2m_max[0]').trim()
        RATE = sh(returnStdout: true, script: 'curl -s "https://open.er-api.com/v6/latest/USD" | jq -r .rates.KES').trim()
    }
    parameters {
        string defaultValue: '30', description: 'Maximum temperature', name: 'MAX_TEMP'
        string defaultValue: '150', description: 'Maximum exchange rate', name: 'MAX_RATE'
    }
    stages {
        stage('Temperature') {
            steps {
                script {
                    def temp = env.TEMP.toFloat()
                    def maxTemp = params.MAX_TEMP.toFloat()
                    if (temp > maxTemp) {
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
                    def maxRate = params.MAX_RATE.toFloat()
                    if (rate > maxRate) {
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

## **Prerequisites**
- Jenkins with Pipeline plugin
- Tools on agent: `curl`, `jq`

## **Parameters**
| Name | Default | Description |
|------|---------|-------------|
| MAX_TEMP | 30 | Maximum allowed temperature (°C) |
| MAX_RATE | 150 | Maximum allowed USD-KES exchange rate |

## **Usage**
1. Navigate to pipeline job
2. Click "Build" this will build with defaults. Click "Build with parameters" this shows up in the second build
3. Adjust thresholds as needed
4. Start build

## **Behavior**
- Fails if temperature exceeds MAX_TEMP
- Fails if exchange rate exceeds MAX_RATE
- Prints current values in console output

## **Extending**
- Add location parameter
- Include notification steps
- Store historical data


