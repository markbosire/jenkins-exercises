# Jenkins Docker Cloud Agent Setup

## Prerequisites
- Jenkins with Docker plugin installed
- Docker daemon accessible to Jenkins

## Enable Docker TCP API Access

### Method 1: Via systemd Override
```bash
sudo systemctl edit docker.service
```

Add configuration:
```ini
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2376
```

### Method 2: Via Environment File
Edit Docker environment:
```bash
sudo nano /etc/default/docker
```

Add line:
```bash
DOCKER_OPTS="-H tcp://0.0.0.0:2376 -H unix:///var/run/docker.sock"
```

### Apply Changes
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### Verify TCP Access
```bash
curl http://DOCKER_HOST_IP:2376/version
```

## Step 1: Install Docker Plugin
1. **Manage Jenkins** → **Manage Plugins**
2. **Available** → Search "Docker"
3. Install **Docker** plugin

## Step 2: Configure Docker Cloud
1. **Manage Jenkins** → **Manage Nodes and Clouds**
2. **Configure Clouds** → **Add a new cloud** → **Docker**

### Docker Cloud Settings
- **Name**: `docker-cloud`
- **Docker Host URI**: `tcp://localhost:2376` or `unix:///var/run/docker.sock`
- **Enabled**: ✓

### Test Connection
Click **Test Connection** - should show Docker version

## Step 3: Add Docker Agent Template
Click **Docker Agent templates** → **Add Docker Template**

### Basic Settings
- **Labels**: `docker-agent`
- **Name**: `jenkins-agent`
- **Docker Image**: `jenkins/agent:latest`
- **Instance Capacity**: `2`
- **Remote File System Root**: `/home/jenkins`

### Container Settings
- **Usage**: `Use this node as much as possible`
- **Connect method**: `Connect with SSH`
- **SSH Key**: Use Jenkins SSH credentials

## Step 4: Use Docker Agent

### Scripted Pipeline
```groovy
node('docker-agent') {
    stage('Build') {
        checkout scm
        sh 'echo "Running in Docker container"'
        sh 'docker --version'
    }
}
```

### Custom Docker Image
```dockerfile
# Dockerfile
FROM jenkins/agent:latest
USER root
RUN apt-get update && apt-get install -y maven nodejs
USER jenkins
```

Build and use:
```bash
docker build -t custom-jenkins-agent .
```

Update template **Docker Image**: `custom-jenkins-agent`