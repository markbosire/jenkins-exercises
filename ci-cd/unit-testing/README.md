# JaCoCo and JUnit Integration with Jenkins Coverage Plugin

This repository demonstrates how to integrate JaCoCo code coverage and JUnit testing with Jenkins using the Coverage plugin.

## Project Structure

```
sample-project/
├── pom.xml                      # Maven project configuration
├── src/
│   ├── main/java/com/example/   # Main source code
│   │   ├── Calculator.java      # Sample Java class
│   │   └── StringUtils.java     # Sample Java class
│   └── test/java/com/example/   # Test source code
│       ├── CalculatorTest.java  # Test for Calculator class
│       └── StringUtilsTest.java # Test for StringUtils class
└── Jenkinsfile                  # Jenkins pipeline configuration
```

## Setup Guide

### Prerequisites

1. Jenkins server (version 2.479.3 or later)
2. Required Jenkins plugins:
   - [Coverage Plugin](https://plugins.jenkins.io/coverage/)
   - [Git Forensics Plugin](https://plugins.jenkins.io/git-forensics/)
   - [Pipeline Plugin](https://plugins.jenkins.io/workflow-aggregator/)
   - [JUnit Plugin](https://plugins.jenkins.io/junit/)

### Jenkins Configuration

1. Create a new Pipeline job in Jenkins
2. Configure it to use this repository as the source
3. Make sure you have the Maven and JDK tools configured in Jenkins with the names:
   - `Maven 3.9.6`
   - `JDK 11`

If the tool names are different in your Jenkins instance, please adjust the Jenkinsfile accordingly.

## Understanding the Reports

The Coverage plugin provides:

1. **Overview Dashboard**: Shows overall coverage metrics
2. **Trend Charts**: Coverage trends over time
3. **Tree Maps**: Visual representation of coverage distribution
4. **Source Code View**: Annotated source with coverage information
5. **Modified Lines Coverage**: Shows coverage of changed lines

## Key Features

- Integration of JaCoCo code coverage and JUnit testing
- Quality gates that mark builds as unstable if coverage thresholds are not met
- Source code retention for historical analysis
- Modified lines coverage analysis for pull requests
- Coverage trend visualization

## Troubleshooting

If you encounter issues:

1. Check that Jenkins has the Coverage plugin installed
2. Verify that Maven and JDK tools are correctly configured
3. Ensure the Jenkins user has appropriate permissions to run the build
4. Examine build logs for specific error messages
5. Check the JaCoCo and JUnit XML reports are generated correctly

## License

MIT