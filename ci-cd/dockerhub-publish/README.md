# Docker Push to DockerHub

Simple setup to push Docker images to DockerHub using Jenkins.

## Setup

1. **DockerHub**: Create account and repository at hub.docker.com
2. **Jenkins Credentials**: Add DockerHub username/password with ID `dockerhub-creds`
3. **Update Jenkinsfile**: Change `markbosire/simpleapp` to your DockerHub repo

## Usage

Push code to trigger Jenkins build. Pipeline will:
- Build Docker image
- Push to DockerHub with `latest` tag

## Local Testing

```bash
docker build -t markbosire/simpleapp .
docker run -p 8080:8080 markbosire/simpleapp
```