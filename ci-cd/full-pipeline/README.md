# Jenkins CI/CD Pipeline

This repository contains a Jenkins pipeline configuration for a Node.js application with automated testing, Docker image building, and deployment to Docker Hub.

## Pipeline Overview

The Jenkins pipeline consists of four main stages that execute sequentially:

### 1. Packages Stage
- Installs required dependencies for testing
- Specifically installs Mocha (testing framework) and Chai v4.3.10 (assertion library)
- Command: `npm install mocha chai@4.3.10`

### 2. Test Stage
- Executes the application's test suite
- Runs tests using the npm test script defined in package.json
- Command: `npm run test`

### 3. Build Stage
- Creates a Docker image of the application
- Tags the image with the Jenkins build number for versioning
- Image naming convention: `markbosire/simpleapp:${BUILD_NUMBER}`

### 4. Push Stage
- Pushes the built Docker image to Docker Hub registry
- Uses Docker Hub credentials stored in Jenkins
- Publishes to the public Docker Hub repository

## Prerequisites

### Jenkins Setup
- Jenkins server with Docker plugin installed
- Docker installed on the Jenkins agent
- Node.js and npm available on the Jenkins agent

### Credentials Configuration
- Docker Hub credentials must be configured in Jenkins with ID: `dockerhub-credentials`
- Credentials should include Docker Hub username and password/access token

### Repository Requirements
- `package.json` file with test script defined
- `Dockerfile` in the repository root
- Test files compatible with Mocha and Chai

## Usage

1. Create a new Jenkins pipeline job
2. Configure the job to use this Jenkinsfile from your repository
3. Ensure all prerequisites are met
4. Run the pipeline

## Pipeline Behavior

- **Success Path**: All stages execute sequentially if each stage passes
- **Failure Handling**: Pipeline stops at the first failed stage
- **Artifacts**: Docker image is tagged with build number and pushed to Docker Hub
- **Testing**: Application must pass all tests before building Docker image

## Configuration Notes

- The pipeline uses `agent any`, meaning it can run on any available Jenkins agent
- Docker registry is configured for Docker Hub (`https://index.docker.io/v1/`)
- Chai version is pinned to 4.3.10 for consistency

## Customization

To adapt this pipeline for your project:

1. Update the Docker image name in the Build stage
2. Modify the Docker Hub username in the image tag
3. Adjust test dependencies if using different testing frameworks
4. Update credential ID if using different Jenkins credential configuration

## Troubleshooting

**Common Issues:**
- Ensure Docker Hub credentials are properly configured in Jenkins
- Verify that the npm test script exists in package.json
- Check that Docker daemon is running on the Jenkins agent
- Confirm that the repository contains a valid Dockerfile