# Jenkins Private Git Clone with SSH


## 1. Generate SSH Keys

```bash
# Switch to jenkins user
su - jenkins

# Generate SSH key
ssh-keygen -t rsa -b 4096 -C "jenkins@yourserver"
# Press Enter for default location (~/.ssh/id_rsa)
# Press Enter twice for no passphrase

# Copy public key
cat ~/.ssh/id_rsa.pub
```

## 2. Add SSH Key to Git Repository

- Copy the public key content
- Go to your Git provider (GitHub/GitLab/Bitbucket)
- Add the key to Deploy Keys or SSH Keys section

## 3. Configure Jenkins SSH

```bash
# Test SSH connection
ssh -T git@github.com  # or your git provider
# Type 'yes' to accept fingerprint

# Add to known_hosts
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

## 4. Add SSH Credentials to Jenkins

1. Go to **Manage Jenkins** → **Manage Credentials**
2. Click **Global** → **Add Credentials**
3. Select **SSH Username with private key**
4. Fill details:
   - ID: `git-ssh-key`
   - Username: `git`
   - Private Key: **Enter directly** → paste content of `~/.ssh/id_rsa`

## 5. Create Jenkins Job

1. **New Item** → **Pipeline** → Enter name
2. In **Pipeline** section, select **Pipeline script from SCM**
3. Configure:
   - SCM: **Git**
   - Repository URL: `git@github.com:username/repo.git`
   - Credentials: Select `git-ssh-key`
   - Branch: `*/main` or `*/master`

## 6. Create Jenkinsfile

In your repository root, create `Jenkinsfile`:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Repository cloned successfully!'
                sh 'ls -la'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building application...'
                // Add your build commands
            }
        }
    }
}
```

## 9. Run the Job

1. Click **Build Now**
2. Check **Console Output** for results

## Troubleshooting

**Permission denied (publickey)**
```bash
# Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

**Host key verification failed**
```bash
# Add host to known_hosts
ssh-keyscan your-git-host.com >> ~/.ssh/known_hosts
```

**Jenkins can't find SSH key**
- Ensure credentials ID matches in job configuration
- Verify private key format (starts with `-----BEGIN RSA PRIVATE KEY-----`)
