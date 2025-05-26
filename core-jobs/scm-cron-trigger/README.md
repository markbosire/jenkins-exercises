# **Jenkins SCM Polling Guide**

## **Overview**
This guide explains how to configure Jenkins jobs to trigger builds automatically using:
- **SCM Polling**: Checks your repository for changes at regular intervals

## **SCM Polling Configuration**

### **1. Basic SCM Polling**
1. In your Jenkins job configuration:
2. Under "Build Triggers" section:
   - Check **"Poll SCM"**
3. Set the polling schedule using cron syntax:
   ```
   H/5 * * * *  # Check every 5 minutes
   ```

### **2. Pipeline Syntax (Declarative)**
```groovy
pipeline {
    triggers {
        pollSCM('H/15 * * * *')  // Check every 15 minutes
    }
    // rest of your pipeline
}
```
   
## **Best Practices**
1. **Use the 'H' symbol** for hash randomization (e.g., `H * * * *` spreads load)
2. **Monitor performance** - frequent polling can impact system resources

## **Troubleshooting**
- Verify Jenkins system time matches your timezone
- Check `Polling Log` in job's last build details
- Review Jenkins system logs for scheduler errors

## **Notes**
- SCM polling requires repository credentials configured in Jenkins
- Cron triggers run regardless of repository changes
- For GitHub/Bitbucket, webhooks are more efficient than polling


