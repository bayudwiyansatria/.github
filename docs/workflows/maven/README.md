# Maven Action Workflow

This directory contains documentation for Maven-related GitHub Actions workflows used in this repository.

## Available Workflows

- [Build Maven Workflow](build.md)
- [Deploy Maven Workflow](deploy.md)
- [Release Maven Workflow](release.md)
- [Test Maven Workflow](test.md)

Each workflow is documented with its configuration, inputs, secrets, and example usage.

### Build Maven Workflow

The Build Maven Workflow automates the process of building Java projects using Maven. It supports customizable configurations for Java version, Maven registry, and more.

### Deploy Maven Workflow

The Deploy Maven Workflow automates the process of deploying Maven packages. It includes steps for logging into a Maven registry, building Maven packages, and pushing them to the registry.

### Release Maven Workflow

The Release Maven Workflow automates the process of releasing Maven packages. It includes steps for preparing the repository, tagging the release, deploying the package, and updating the version.

### Test Maven Workflow

The Test Maven Workflow automates the process of testing Java projects using Maven. It includes steps for setting up Java, running tests, and uploading test results.
