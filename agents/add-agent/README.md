# Jenkins Linux SSH Agent Setup

## Prerequisites
- Jenkins master server running
- Linux machine for agent
- SSH access to agent machine
- Java installed on agent machine

## Step 1: Prepare Agent Machine

### Install Java
```bash
sudo apt update
sudo apt install openjdk-11-jdk
```

### Create Jenkins User
```bash
sudo useradd -m -s /bin/bash jenkins
sudo mkdir -p /home/jenkins/.ssh
sudo chown jenkins:jenkins /home/jenkins/.ssh
sudo chmod 700 /home/jenkins/.ssh
```

### Create Working Directory
```bash
sudo mkdir -p /opt/jenkins
sudo chown jenkins:jenkins /opt/jenkins
```

## Step 2: Configure SSH Keys

### Generate SSH Key Pair (on Jenkins master)
```bash
ssh-keygen -t rsa -b 4096 -f jenkins-agent-key
```

### Copy Public Key to Agent
```bash
ssh-copy-id -i jenkins-agent-key.pub jenkins@AGENT_IP
```

Or manually:
```bash
# On agent machine
sudo -u jenkins nano /home/jenkins/.ssh/authorized_keys
# Paste public key content
sudo chmod 600 /home/jenkins/.ssh/authorized_keys
```

## Step 3: Add Agent in Jenkins

### Navigate to Jenkins Dashboard
1. Go to **Manage Jenkins** → **Manage Nodes and Clouds**
2. Click **New Node**
3. Enter node name and select **Permanent Agent**

### Configure Agent Settings
- **Name**: `linux-agent-01`
- **Description**: `Linux SSH Agent`
- **# of executors**: `2`
- **Remote root directory**: `/opt/jenkins`
- **Labels**: `linux ssh`
- **Usage**: `Use this node as much as possible`
- **Launch method**: `Launch agents via SSH`

### SSH Configuration
- **Host**: `AGENT_IP_ADDRESS`
- **Credentials**: Click **Add** → **Jenkins**

#### Add SSH Credentials
- **Kind**: `SSH Username with private key`
- **ID**: `jenkins-ssh-key`
- **Username**: `jenkins`
- **Private Key**: Select **Enter directly** and paste private key content
- **Passphrase**: Leave empty if no passphrase

### Advanced SSH Settings
- **Host Key Verification Strategy**: `Non verifying Verification Strategy`
- **Port**: `22`
- **JavaPath**: `/usr/bin/java`

## Step 4: Launch Agent

1. Click **Save**
2. Go to **Manage Nodes and Clouds**
3. Click on your agent name
4. Click **Launch agent**

## Step 5: Verify Connection

### Check Agent Status
- Agent should show as **Online** with green indicator
- Check agent logs for any errors

### Test Build
Create simple freestyle job:
```bash
echo "Hello from $(hostname)"
whoami
pwd
java -version
```

## Troubleshooting

### Common Issues
- **SSH connection refused**: Check SSH service running on agent
- **Permission denied**: Verify SSH key permissions and ownership
- **Java not found**: Install Java or specify correct path
- **Working directory access**: Ensure jenkins user can write to directory

### Useful Commands
```bash
# Check SSH connection
ssh -i jenkins-agent-key jenkins@AGENT_IP

# Verify Java installation
java -version

# Check Jenkins user permissions
sudo -u jenkins ls -la /opt/jenkins
```

## Security Notes
- Use dedicated SSH keys for Jenkins
- Restrict jenkins user permissions
- Consider using SSH key passphrases
- Regularly rotate SSH keys
- Monitor agent access logs