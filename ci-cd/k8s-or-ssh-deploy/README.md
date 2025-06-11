# Jenkins SSH Deployment Pipeline (NGINX Example)

This guide walks through the process of setting up a Jenkins Pipeline that SSHs into a remote Docker host to deploy an NGINX container. The pipeline uses **Jenkins credentials** to securely manage SSH keys — no plugins like `Publish Over SSH` or `ssh-agent` are required.

---

## 🧰 Prerequisites

- Jenkins installed and accessible
- A separate remote VM (Docker host) with Docker installed
- SSH access to the Docker host
- Jenkins user able to run pipelines
- The Docker host's SSH port (default: 22) must be reachable from Jenkins

---

## 🔐 Step 1: Generate SSH Key for Jenkins

Login as the Jenkins user on the Jenkins host:

```bash
sudo su - jenkins
ssh-keygen -t rsa -b 4096 -C "jenkins@ci" -f ~/.ssh/id_rsa -N ""
````

This will generate:

* Private Key: `~/.ssh/id_rsa`
* Public Key: `~/.ssh/id_rsa.pub`

---

## 🔑 Step 2: Authorize the Public Key on Docker Host

On the Jenkins host (still as `jenkins`):

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the output.

Then on the **Docker host**, log in as the SSH user (e.g., `vagrant`), and paste it into:

```bash
nano ~/.ssh/authorized_keys
```

Ensure permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

Test the SSH connection from the Jenkins server:

```bash
ssh -i ~/.ssh/id_rsa vagrant@<DOCKER_HOST_IP>
```

---

## 🔐 Step 3: Add SSH Key to Jenkins Credentials

1. Go to **Jenkins → Manage Jenkins → Credentials → (Global) → Add Credentials**
2. Use the following:

| Field       | Value                                       |
| ----------- | ------------------------------------------- |
| Kind        | **SSH Username with private key**           |
| Username    | `vagrant` (or SSH user)                     |
| Private Key | Enter directly → paste from `~/.ssh/id_rsa` |
| ID          | `docker-ssh-creds`                          |
| Description | `SSH key for Docker host`                   |

---

## 🚀 Step 4: Jenkins Pipeline

This pipeline connects to the Docker host and runs NGINX via Docker:

```groovy
pipeline {
    agent any

    environment {
        DOCKER_HOST = "192.168.100.51" // your Docker host IP
    }

    stages {
        stage('Deploy NGINX via SSH') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'docker-ssh-creds',
                    keyFileVariable: 'KEY',
                    usernameVariable: 'USER'
                )]) {
                    sh '''
ssh -o StrictHostKeyChecking=no -i "$KEY" "$USER@$DOCKER_HOST" '
  mkdir -p ~/nginx-content &&
  echo "Hello from Jenkins" > ~/nginx-content/index.html &&
  docker rm -f jenkins-nginx || true &&
  docker run -d \\
    --name jenkins-nginx \\
    -p 8080:80 \\
    -v ~/nginx-content:/usr/share/nginx/html:ro \\
    nginx
'
'''
                }
            }
        }
    }
}
```

---

## 📦 What It Does

* Logs into the remote Docker host using SSH
* Creates a basic HTML file with a message
* Launches a fresh NGINX container bound to port 8080
* Serves the HTML file via volume mount

---

## 🧪 Test It

Once the pipeline runs successfully, open:

```
http://<DOCKER_HOST_IP>:8080
```

You should see:

```
Hello from Jenkins
```

---

## 🛠️ Troubleshooting

| Problem            | Solution                                                            |
| ------------------ | ------------------------------------------------------------------- |
| Permission denied  | Double-check `authorized_keys`, file permissions, and credential ID |
| SSH hangs          | Add `-v` to `ssh` command for debugging                             |
| Jenkins can't SSH  | Ensure correct user, port, and SSH IP are reachable                 |
| Docker not running | Verify Docker is installed and running on remote host               |

---

## ✅ Summary

* No plugins used for SSH
* Secure key handling via Jenkins credentials
* Clean one-stage deployment pipeline

---

## 📁 Repo Structure Suggestion

```
.
├── Jenkinsfile
└── README.md
```

---

