# Jenkins Multi-stage pipeline

This repository contains a simple Node.js application with a multi-stage Jenkins declarative pipeline and Docker support.

## Application Code

### index.js
```javascript
const express = require('express')
const app = express()
const port = 3000
const d = new Date();
let year = d.getFullYear();
const { add, subtract } = require('./math');

app.get('/', (req, res) => {
  res.send(`Hello World! jenkins was inveted ${subtract(year,2004)} years ago`)
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```

### math.js
```javascript
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

module.exports = { add, subtract };
```

### math.test.js
```javascript
const { add, subtract } = require('./math');

test('adds 1 + 2 to equal 3', () => {
    expect(add(1, 2)).toBe(3);
});

test('subtracts 5 - 2 to equal 3', () => {
    expect(subtract(5, 2)).toBe(3);
});
```

## Dockerfile

```dockerfile
FROM node:24-alpine3.20
WORKDIR /app/src
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node","index.js"]

```

## Jenkinsfile

```groovy
pipeline{
	agent any
	stages{
		stage('test'){
			steps{
				sh'''
					npm install jest
					npm test
				'''
				}
			}
		stage('build'){
                        steps{
                                sh'docker build -t exampleapp .'
                                }
                        }
		stage('deploy'){
                        steps{
                                sh'docker run -d --name exampleapp -p 3000:3000 -t exampleapp'
                                }
                        }		
		}
}
```

## Explanation

### Multi-Stage Declarative Pipeline

This Jenkins pipeline is a declarative pipeline with multiple stages:

1. **Test**: Installs dependencies and runs tests
3. **Build Docker Image**: Creates a Docker image from the application
4. **Deploy**: Runs the container from the built image


### Prerequisites

1. Jenkins server with Docker and Node.js plugins installed
2. Node.js application with proper package.json and tests

### Setup Instructions

1. Create a new Jenkins pipeline job
2. Point it to this repository
3. Run the pipeline

This setup ensures that your application goes through testing, gets containerized, and is deployed in a consistent manner through the CI/CD pipeline.