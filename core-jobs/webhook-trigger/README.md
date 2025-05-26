# **Jenkins GitHub Webhook Integration Guide**

## **Overview**
This guide explains how to configure a Jenkins job to automatically trigger builds when code is pushed to a GitHub repository. For local Jenkins servers, you'll need to expose them to the internet first (see [ngrok guide](ngrok.md)).

## **Prerequisites**
- Jenkins server with internet access
- GitHub repository admin access
- Jenkins plugins installed:
  - GitHub Plugin

## **Configuration Steps**

### **1. Configure Jenkins Job**
1. Create a new pipeline/item in Jenkins
2. Under "Build Triggers" section:
   - Check **"GitHub hook trigger for GITScm polling"**


### **2. Set Up GitHub Webhook**
1. Go to your GitHub repo → Settings → Webhooks
2. Click "Add webhook"
3. Configure:
   - **Payload URL**: `https://your-jenkins-server/github-webhook/`
   - **Content type**: `application/json`
   - **Secret**: (Optional) Add a secret matching your Jenkins configuration
   - **Events**: Select "Just the push event"

### **3. Configure Jenkins System Settings**
1. Go to **Manage Jenkins → Configure System**
2. Under **GitHub** section:
   - Add GitHub server
   - Configure credentials if using private repos
3. Ensure "Manage hooks" is checked

## **Testing the Setup**
1. Make a commit and push to your repo
2. Check Jenkins for automatic build triggering

## **Troubleshooting**
- Check Jenkins logs at `/var/log/jenkins/jenkins.log`
- Verify GitHub webhook deliveries in repo settings
- Ensure correct permissions for Jenkins GitHub credentials

## **Exposing Local Jenkins**
For Jenkins servers running locally (localhost/Vagrant), you'll need to expose them to the internet for GitHub webhooks to work. See our [ngrok guide](ngrok.md) for detailed instructions.

## **Security Considerations**
- Use HTTPS for webhook URLs
- Implement secret validation
- Restrict webhook to push events only
- Consider IP whitelisting for production environments

**Note**: Webhooks may take 1-2 minutes to propagate after initial setup.
