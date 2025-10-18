# LMW Fitness Tekton Pipeline

This Tekton pipeline has been updated to match the functionality of the Harness pipeline with improved resource efficiency and better integration with the lmwfitness-helmcharts.

## Features

The pipeline now includes:

### Testing & Quality Assurance
- **Frontend**: ESLint linting, unit tests, npm audit for security vulnerabilities
- **Backend**: Go linting with `go vet`, comprehensive tests with PostgreSQL database
- **Conditional execution**: Tasks run only for the appropriate application type

### Security
- **Trivy scanning**: Vulnerability scanning for backend Docker images
- **Critical severity filtering**: Configurable severity levels

### Deployment
- **Helm integration**: Direct deployment using lmwfitness-helmcharts
- **Dynamic image tagging**: Uses pipeline sequence ID for versioning
- **Resource optimization**: Reduced memory and CPU requirements

## Pipeline Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `GITHUB_CLONE_URL` | Source repository URL | `https://github.com/lmwcode/lmwfitness-frontend.git` |
| `GITHUB_HELM_URL` | Helm charts repository URL | `https://github.com/lmwcode/lmwfitness-helmcharts.git` |
| `IMAGE_REPO` | Docker image repository | `index.docker.io/lmwcode/lmw-fitness-frontend` |
| `APPLICATION_TYPE` | Application type | `frontend` or `backend` |
| `DOCKERFILE` | Path to Dockerfile | `Dockerfile` |
| `CONTEXT` | Build context path | `.` |
| `BUILDER_IMAGE` | Kaniko builder image | `gcr.io/kaniko-project/executor:latest` |
| `TRIVY_SEVERITY` | Security scan severity | `CRITICAL,HIGH` |
| `HELM_CHART_PATH` | Helm chart path | `frontend` or `backend` |
| `PIPELINE_SEQUENCE_ID` | Pipeline sequence for tagging | `1` |

## Usage

### Frontend Deployment
```bash
kubectl apply -f examples/frontend-pipelinerun.yaml
```

### Backend Deployment
```bash
kubectl apply -f examples/backend-pipelinerun.yaml
```

## Pipeline Flow

1. **Git Clone**: Clone source and Helm chart repositories
2. **Debug**: Display resolved repository and tag information
3. **Conditional Testing**:
   - **Frontend**: Linting → Unit Tests → NPM Audit
   - **Backend**: Linting → Database Setup → Integration Tests
4. **Docker Build**: Build and push image with Kaniko
5. **Security Scan**: Trivy vulnerability scan (backend only)
6. **Helm Deploy**: Deploy using lmwfitness-helmcharts

## Resource Requirements

The pipeline has been optimized for resource efficiency:

- **Debug tasks**: 10Mi memory, 10m CPU
- **Frontend tasks**: 100-500Mi memory, 100-250m CPU
- **Backend tasks**: 100-1750Mi memory, 100-1250m CPU
- **Security scanning**: 250-500Mi memory, 100-250m CPU

## Helm Chart Integration

The pipeline integrates directly with your lmwfitness-helmcharts:
- **Frontend**: Uses `lmwfitness-helmcharts/frontend`
- **Backend**: Uses `lmwfitness-helmcharts/backend`
- **Dynamic values**: Image repository and tag are set automatically
- **Namespace**: Deploys to `lmw-fitness` namespace

## Differences from Harness Pipeline

### Improvements
- **Resource efficiency**: Significantly reduced memory and CPU usage
- **Conditional execution**: Tasks only run when needed
- **Better error handling**: Proper exit codes and error propagation
- **Simplified parameters**: Fewer, more intuitive parameters

### Maintained Features
- All testing and linting functionality
- Security scanning with Trivy
- Docker build and push
- Helm deployment
- Environment variable support

## Prerequisites

1. Tekton Pipelines installed in your cluster
2. Secrets configured:
   - `git-credentials`: GitHub access
   - `docker-config`: Docker registry credentials
3. lmwfitness-helmcharts repository accessible
4. Proper RBAC permissions for the pipeline service account