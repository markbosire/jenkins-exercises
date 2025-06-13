# Using Jenkins Agent Labels

## Set Agent Label
1. **Manage Jenkins** → **Manage Nodes**
2. Select agent → **Configure**
3. **Labels**: `linux`

## Use Label in Job

### Freestyle Job
1. Job → **Configure**
2. Check **Restrict where this project can be run**
3. **Label Expression**: `linux`

### Scripted Pipeline
```groovy
node('linux') {
    stage('Build') {
        checkout scm
        sh 'make build'
    }
    stage('Test') {
        sh 'make test'
    }
}
```

## Common Labels
- `linux`, `windows`, `macos`
- `docker`, `maven`, `nodejs`
- `build`, `test`, `deploy`