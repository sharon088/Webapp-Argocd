# Weather App - CI/CD Pipeline with GitHub Actions and ArgoCD

## Project Overview

This project is a **Weather App** that utilizes **GitHub Actions** for continuous integration and continuous deployment (CI/CD), and **ArgoCD** for automated deployment to Kubernetes. The repository is configured to build the Docker image, tag it, update the Kubernetes manifests, and push changes to the repository. Additionally, it includes configuration files for **Argocd** and **RBAC** to handle deployments and access control.

## Features

- **GitHub Actions CI/CD Pipeline**: Automates building, tagging, and pushing Docker images to Docker Hub.
- **Kubernetes Manifests**: Configurations for Kubernetes deployment, service, and ArgoCD.
- **ArgoCD Integration**: Manages deployments through ArgoCD.
- **RBAC Configuration**: Configures roles and permissions for accessing ArgoCD resources.

## Prerequisites

Before you begin, ensure you have the following set up:

- **GitHub**: To host the repository and set up GitHub Actions workflows.
- **Docker Hub**: For hosting the Docker images.
- **Kubernetes Cluster**: To deploy the app using ArgoCD.
- **ArgoCD**: For managing the application lifecycle in Kubernetes.
- **kubectl**: For interacting with the Kubernetes cluster.
- **Python**: Python 3.10 for versioning (used by GitHub Actions).

## Setup and Installation

1. **Clone the repository**:

    ```bash
    git clone https://github.com/your-username/weather-app.git
    cd weather-app
    ```

2. **Configure Secrets**: Ensure the following secrets are set in your GitHub repository's settings:
    - `DOCKER_USERNAME`: Your Docker Hub username.
    - `DOCKER_PASSWORD`: Your Docker Hub password.

3. **GitHub Actions Workflow**:

    The workflow is defined in `.github/workflows/argocd_build_pipeline.yml`. It automatically triggers on every push to the `main` branch. The workflow includes the following steps:
    - **Checkout**: The repository code is checked out.
    - **Set up Python**: Python 3.10 is set up to handle versioning.
    - **Tag Version**: A version tag is created based on the GitHub versioning pattern.
    - **Login to Docker Hub**: The Docker Hub credentials are used to log in.
    - **Build and Push Docker Image**: A Docker image is built and pushed to Docker Hub with the version tag.
    - **Update Manifest**: The Kubernetes deployment manifest is updated with the new image tag.
    - **Commit Changes**: The updated manifest is committed and pushed back to the `main` branch.

4. **Kubernetes Manifests**:

    The `manifests` folder contains Kubernetes configuration files for deploying the Weather App. These include:
    
    - **argocd-cm.yaml**: ConfigMap for ArgoCD.
    - **argocd-rbac-cm.yaml**: RBAC ConfigMap for ArgoCD access control.
    - **webapp_service.yaml**: Defines the Kubernetes service to expose the Weather App.
    - **webapp_weather.yaml**: Deployment file for the Weather App, including the Docker image used for deployment.

    Ensure that your Kubernetes cluster is configured to use ArgoCD for deployment management.

5. **ArgoCD Setup**:

    The `argocd-cm.yaml` and `argocd-rbac-cm.yaml` files are used to configure **ArgoCD** and **RBAC** for managing application access. The `webapp_weather.yaml` deployment manifest is used by ArgoCD to deploy the Weather App.

## GitHub Actions Workflow Explained

### Workflow Configuration

The workflow `argocd_build_pipeline.yml` is triggered on a push to the `main` branch. Here's a breakdown of the key steps:

1. **Checkout**: This step checks out the repository using the `actions/checkout@v4` action.
   
2. **Set up Python**: Sets up Python 3.10, which is used for automatic versioning in the workflow.
   
3. **Fetch Git Tag**: Fetches the latest tags from GitHub to ensure that the versioning is up to date.
   
4. **Tag Version**: This step uses `bitshifted/git-auto-semver` to automatically generate a semantic version tag based on the repository version.
   
5. **Login to Docker Hub**: Uses `docker/login-action@v3` to authenticate to Docker Hub with credentials stored in GitHub Secrets.
   
6. **Build and Push to Docker Hub**: Builds the Docker image and tags it with the generated version. The image is then pushed to Docker Hub using the `docker/build-push-action@v6`.
   
7. **Update Kubernetes Manifest**: The Kubernetes manifest for the Weather App is updated to reflect the new image version.
   
8. **Commit and Push Changes**: The updated manifest file is committed and pushed back to the repository.

### ArgoCD and RBAC

ArgoCD is configured with the following files:

- **argocd-cm.yaml**: ConfigMap for ArgoCD settings.
- **argocd-rbac-cm.yaml**: RBAC configuration that assigns roles to users.
    - The `sharon-role` user is granted `readonly` access to applications managed by ArgoCD.

The **webapp_service.yaml** and **webapp_weather.yaml** are used for defining the Kubernetes service and deployment.

## Docker Image Versioning

The Docker image for the Weather App is automatically tagged using the semantic versioning system based on the GitHub repository version. The `git-auto-semver` action generates a version string like `v1.0.6`, which is used in the Docker image tag.

## Example Kubernetes Manifests

Here is a brief description of some key Kubernetes manifests:

1. **argocd-cm.yaml**:
    ConfigMap for ArgoCD with basic settings, including user access information.

2. **argocd-rbac-cm.yaml**:
    RBAC configuration to define roles and permissions for ArgoCD users.

3. **webapp_service.yaml**:
    Exposes the Weather App as a Kubernetes service.

4. **webapp_weather.yaml**:
    A Kubernetes deployment manifest that specifies how to deploy the Weather App with the latest Docker image.

## Contributing

1. Fork the repository.
2. Create a new branch (`git checkout -b feature-branch`).
3. Commit your changes (`git commit -am 'Add new feature'`).
4. Push to the branch (`git push origin feature-branch`).
5. Open a Pull Request.
