# Reusable Docker Build, Push & Security Scan Workflow

[![Docker Build & Scan](https://img.shields.io/badge/Docker-Build%20%26%20Scan-blue?logo=docker)](https://github.com/Harshraj9812/Reusable-Docker-Build-Push-Scan)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Reusable%20Workflow-green?logo=github-actions)](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
[![Security Scan](https://img.shields.io/badge/Security-Docker%20Scout-orange?logo=docker)](https://docs.docker.com/scout/)

A comprehensive and reusable GitHub Actions workflow for building, pushing, and security scanning Docker images with automated tagging and comprehensive reporting.

## ✨ Features

- 🐳 **Docker Build & Push**: Automated Docker image building and pushing to registries
- 🔒 **Security Scanning**: Integrated Docker Scout vulnerability scanning
- 🏷️ **Auto Tagging**: Intelligent versioning for both default and feature branches
- 🚀 **GitHub Releases**: Automated release creation with auto-generated changelogs
- 📊 **Detailed Reporting**: Rich workflow summaries and integrated build information
- ⚡ **Build Caching**: GitHub Actions cache integration for faster builds
- 🎯 **Flexible Configuration**: Extensive customization options via inputs and secrets
- 🔄 **Git Integration**: Optional automatic Git tag and release management

## 🚀 Quick Start

### Basic Usage

Create a workflow file in your repository (`.github/workflows/docker.yml`):

```yaml
name: Build and Deploy Docker Image

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  docker-build-push-scan:
    uses: Harshraj9812/Reusable-Docker-Build-Push-Scan/.github/workflows/docker-build-push-scan.yaml@master
    with:
      image_name: "your-dockerhub-username/your-app-name"
      dockerhub_username: "your-dockerhub-username"
    secrets:
      dockerhub_token: ${{ secrets.DOCKERHUB_TOKEN }}
```

### Advanced Usage

```yaml
name: Advanced Docker Build

on:
  push:
    branches: [main]
  release:
    types: [published]

jobs:
  docker-build-push-scan:
    uses: Harshraj9812/Reusable-Docker-Build-Push-Scan/.github/workflows/docker-build-push-scan.yaml@master
    with:
      image_name: "mycompany/myapp"
      dockerhub_username: "mycompany"
      dockerfile_path: "./docker/Dockerfile"
      docker_context: "./src"
      registry: "docker.io"
      build_tag_prefix: "release"
      push_latest: true
      create_git_tag: true
      scout_severities: "critical,high"
      enable_cache: true
    secrets:
      dockerhub_token: ${{ secrets.DOCKERHUB_TOKEN }}
```

## 📝 Configuration

### Required Inputs

| Parameter | Type | Description | Example |
|-----------|------|-------------|--------|
| `image_name` | `string` | Full Docker image name | `dockerusername/imagename` |
| `dockerhub_username` | `string` | DockerHub username (public) | `johndoe` |

### Required Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `dockerhub_token` | DockerHub access token | [Create token](https://hub.docker.com/settings/security) |

### Optional Inputs

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `dockerfile_path` | `string` | `"./Dockerfile"` | Path to the Dockerfile |
| `docker_context` | `string` | `"."` | Docker build context path |
| `registry` | `string` | `"docker.io"` | Container registry URL |
| `build_tag_prefix` | `string` | `"v1"` | Prefix for build tag version |
| `push_latest` | `boolean` | `true` | Whether to push latest tag |
| `create_git_tag` | `boolean` | `true` | Whether to create a git tag |
| `scout_severities` | `string` | `"critical,high,medium"` | Docker Scout severity levels |
| `enable_cache` | `boolean` | `true` | Enable GitHub Actions cache |

### Outputs

| Output | Description | Example |
|--------|-------------|--------|
| `build_tag` | The generated build tag | `v1.42` |
| `full_image_name` | Full image name with registry | `docker.io/username/myapp` |

## 🔧 Setup Guide

### 1. Prerequisites

- GitHub repository with a Dockerfile
- DockerHub account (or other container registry)
- Basic knowledge of GitHub Actions

### 2. Create DockerHub Access Token

1. Go to [DockerHub Security Settings](https://hub.docker.com/settings/security)
2. Click "New Access Token"
3. Give it a name (e.g., "GitHub Actions")
4. Copy the generated token

### 3. Add Repository Secrets

1. Go to your GitHub repository
2. Navigate to `Settings` → `Secrets and variables` → `Actions`
3. Click `New repository secret`
4. Add `DOCKERHUB_TOKEN` with your DockerHub access token

### 4. Create Workflow File

Create `.github/workflows/docker.yml` in your repository with the configuration above.

## 🛡️ Security Features

### Docker Scout Integration

This workflow includes comprehensive security scanning using Docker Scout:

- **Vulnerability Detection**: Scans for known CVEs in your images
- **Severity Filtering**: Configurable severity levels (critical, high, medium, low)
- **Integrated Summaries**: View scan results directly in the GitHub Actions run summary
- **Quick Overview**: Real-time security posture assessment during the build process

### Security Best Practices

- Uses secure authentication with DockerHub tokens
- Minimal required permissions (`contents: write`, `packages: write`)
- No secrets exposure in logs
- Automated vulnerability scanning for every build

## 📊 Workflow Outputs

The workflow provides comprehensive reporting:

### Build Information
- **Build Tag**: Unique identifier for the build
- **Branch/Commit**: Contextual information about the source code
- **Run Metadata**: Link to the specific GitHub Actions run

### Security Scan Results
- **Summary**: High-level overview of found vulnerabilities
- **Links**: Direct access to full Docker Scout reports in the Actions summary
- **Continuous Monitoring**: Automatic scanning on every push

### Push Details
- **Tags**: All successfully pushed image tags (including `:latest` if applicable)
- **Pull Command**: Ready-to-use command to pull the newly built image
- **Registry Info**: Detailed information about the target container registry

## 🎯 Use Cases

### Web Applications
```yaml
with:
  image_name: "mycompany/webapp"
  dockerhub_username: "mycompany"
  dockerfile_path: "./Dockerfile"
  build_tag_prefix: "webapp"
```

### Microservices
```yaml
with:
  image_name: "mycompany/auth-service"
  dockerhub_username: "mycompany"
  dockerfile_path: "./services/auth/Dockerfile"
  docker_context: "./services/auth"
  build_tag_prefix: "auth"
```

### Multi-Stage Builds
```yaml
with:
  image_name: "mycompany/api"
  dockerhub_username: "mycompany"
  dockerfile_path: "./build/Dockerfile.prod"
  enable_cache: true
  scout_severities: "critical,high"
```

## 🔄 Versioning Strategy

The workflow employs an intelligent versioning strategy designed for both production and development environments.

### Tag Formats
- **Default Branch**: Uses the pattern `{build_tag_prefix}.{github.run_number}` (e.g., `v1.42`)
- **Feature Branches**: Uses the pattern `feature-{build_tag_prefix}.{github.run_number}` (e.g., `feature-v1.42`)

### GitHub Releases
- **Automated Creation**: When `create_git_tag` is enabled, a GitHub Release is automatically created for every build.
- **Auto-Changelog**: Release notes are automatically generated based on pull request descriptions and commit messages.
- **Pre-releases**: Build from non-default branches (like feature branches) are automatically marked as **Pre-release** in GitHub.
- **Traceability**: Every Docker image tag corresponds to a matching Git tag and GitHub Release.

### Docker Tagging
- **Specific Version**: Every build is tagged with its unique version string.
- **Latest Tag**: The `:latest` tag is only updated and pushed for builds on the **default branch**.

## 🐛 Troubleshooting

### Common Issues

**Authentication Failed**
```
Error: denied: requested access to the resource is denied
```
*Solution*: Check DockerHub token permissions and username

**Dockerfile Not Found**
```
Error: Cannot locate specified Dockerfile
```
*Solution*: Verify `dockerfile_path` parameter

**Build Context Issues**
```
Error: failed to solve: failed to read dockerfile
```
*Solution*: Check `docker_context` path relative to repository root

**Security Scan Failures**
```
Error: Docker Scout scan failed
```
*Solution*: Ensure image was built successfully before scanning

### Debug Mode

Enable debug logging by adding to your workflow:
```yaml
env:
  ACTIONS_STEP_DEBUG: true
```

## 🤝 Contributing

Contributions are welcome! Please feel free to:

1. 🐛 Report bugs
2. 💡 Suggest new features
3. 🔧 Submit pull requests
4. 📖 Improve documentation

### Development

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with a sample project
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Links

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Scout Documentation](https://docs.docker.com/scout/)
- [DockerHub](https://hub.docker.com/)
- [Reusable Workflows Guide](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

## 📈 Statistics

![GitHub stars](https://img.shields.io/github/stars/Harshraj9812/Reusable-Docker-Build-Push-Scan?style=social)
![GitHub forks](https://img.shields.io/github/forks/Harshraj9812/Reusable-Docker-Build-Push-Scan?style=social)
![GitHub issues](https://img.shields.io/github/issues/Harshraj9812/Reusable-Docker-Build-Push-Scan)
![GitHub license](https://img.shields.io/github/license/Harshraj9812/Reusable-Docker-Build-Push-Scan)

---

**Made with ❤️ for the DevOps community**