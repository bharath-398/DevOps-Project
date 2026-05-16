# DevOps Project - File Documentation

This document provides a comprehensive overview of the 5 key files in the **DevOps-Project** repository, including their differences, purposes, and use cases.

---

## 📋 Repository Overview

A DevOps project built with **Node.js**, **Docker**, and **Jenkins**. The project includes 3 pipeline stages: Build → Test → Deploy. Applications are containerized with Docker and pushed to DockerHub.

---


### 1. **index.js** - Node.js Application Server

#### Content Summary:
- Creates an HTTP server using Node.js built-in `http` module
- Listens on port 3000
- Returns a success message when accessed
- Exports the server for testing purposes

#### Key Features:
```javascript
- Port: 3000
- Protocol: HTTP
- Response Type: HTML with embedded CSS
- Message: "Congratulations Node.js App executed successfully !! All the Best!"
```

#### Use Case:
- Serves as the web application that runs inside the Docker container
- Displays a success page when the application is deployed and running
- Can be imported by test files for unit testing

#### When to Use:
- Primary application file for the project
- Must be running for the deployment to be successful
- Referenced by the Dockerfile's CMD instruction

---

### 2. **Dockerfile** - Docker Container Configuration

**Type:** Container Definition File  
**Size:** 244 bytes  
**Purpose:** Defines how to build a Docker image for the Node.js application

#### Content Summary:
```dockerfile
- Base Image: node:18 (Official Node.js image)
- Working Directory: /app
- Copy: All source code into container
- Installation: npm install (dependencies)
- Expose: Port 3000
- Command: node index.js
```

#### Key Stages:
1. **FROM**: Uses official Node.js v18 image
2. **WORKDIR**: Sets /app as working directory
3. **COPY**: Copies all source files to container
4. **RUN**: Installs npm dependencies
5. **EXPOSE**: Declares port 3000 as accessible
6. **CMD**: Runs `node index.js` on container startup

#### Use Case:
- Provides reproducible container environment
- Ensures consistent application deployment across different systems
- Enables Docker image builds and pushes to DockerHub

#### When to Use:
- Build stage in all Jenkins pipelines
- Creates standardized images with version control
- Essential for containerization workflow

---

### 3. **jenkinsfile** - Basic CI/CD Pipeline (Build & Test)

**Type:** Jenkins Pipeline Configuration (Declarative)  
**Size:** 437 bytes  
**Purpose:** First pipeline version - Basic build process with 3 stages

#### Stages:
1. **Checkout Code**
   - Clones the GitHub repository
   - Branch: `main`
   - Repository: emc-nodejs-app

2. **Build Docker Image**
   - Executes: `docker build -t emc-nodejs-app:latest .`
   - Creates Docker image from Dockerfile

3. **Show Docker Images**
   - Displays: `docker images`
   - Lists all available Docker images
   - Verification step

#### Environment Variables:
```
IMAGE_NAME = 'emc-nodejs-app:latest'
```

#### Key Characteristics:
- Basic pipeline without Docker Hub integration
- Uses Windows batch commands (`bat`)
- No Push or Deployment stages
- Good for initial development/testing

#### Use Case:
- Development environment
- Testing Docker build process
- Verifying that images build correctly locally

#### When to Use:
- Learning Jenkins pipeline basics
- Testing locally without pushing to registry
- CI (Continuous Integration) only, no CD

---

### 4. **jenkinspipeline 2** - Extended Pipeline (Build, Test & Push)

**Type:** Jenkins Pipeline Configuration (Declarative)  
**Size:** 1040 bytes  
**Purpose:** Second pipeline version - Adds Docker Hub push functionality

#### Stages:
1. **Checkout Code**
   - Clones the GitHub repository
   - Branch: `main`

2. **Build Docker Image**
   - Executes: `docker build -t %IMAGE_NAME% .`
   - Creates Docker image

3. **Show Docker Images**
   - Lists Docker images
   - Verification step

4. **Docker Login and Push** ⭐ NEW
   - Logs into Docker Hub using credentials
   - Tags image: `venkiemc/emcnewrepo:latest1`
   - Pushes image to DockerHub repository

#### Environment Variables:
```
IMAGE_NAME = 'emc-nodejs-app:latest'
DOCKERHUB_CREDENTIALS = 'dockerhub-token'  // Jenkins credential ID
DOCKERHUB_REPO = 'venkiemc/emcnewrepo'
```

#### Key Differences from jenkinsfile:
- ✅ Adds Docker Hub push functionality
- ✅ Uses Jenkins credentials management
- ✅ Tags images with custom naming convention (`:latest1`)
- ✅ Full CI/CD pipeline (not just CI)

#### Use Case:
- Pushing built images to Docker Hub registry
- Making images available for other teams/services
- Building and publishing to public/private Docker registries

#### When to Use:
- Ready for shared deployment
- Team collaboration requiring centralized image repository
- Partial CD implementation (push only, no deploy)

---

### 5. **jenkinspipeline 3** - Complete CI/CD Pipeline (Build, Test, Push & Deploy)

**Type:** Jenkins Pipeline Configuration (Declarative)  
**Size:** 1136 bytes  
**Purpose:** Third pipeline version - Full CI/CD with deployment to production environment

#### Stages:
1. **Code Checkout**
   - Clones the GitHub repository
   - Branch: `main`

2. **Build Docker Image**
   - Executes: `docker build -t %IMAGE_NAME% .`
   - Creates Docker image

3. **Docker Login and Push** ⭐ OPTIMIZED
   - Logs into Docker Hub
   - Tags image: `venkiemc/emc-nodejs-app:latest` (cleaner naming)
   - Pushes to DockerHub

4. **Pull Docker Image** ⭐ NEW
   - Pulls the latest image: `docker pull %DOCKERHUB_REPO%:latest`
   - Ensures latest version is available

5. **Deploy Docker Container** ⭐ NEW
   - Runs container in detached mode
   - Maps port 80 (host) → 3000 (container)
   - Container name: `emc-node-container`
   - Fully deployed and accessible on port 80

#### Environment Variables:
```
IMAGE_NAME = 'emc-nodejs-app:latest'
DOCKERHUB_CREDENTIALS = 'dockerhub-token'
DOCKERHUB_REPO = 'venkiemc/emc-nodejs-app'
```

#### Key Differences from jenkinspipeline 2:
- ✅ Removes "Show Docker Images" verification step (streamlined)
- ✅ Adds Pull stage (ensures freshness)
- ✅ Adds Deploy stage (actual deployment)
- ✅ Simpler tagging convention (`:latest` instead of `:latest1`)
- ✅ Port mapping for external access (80:3000)
- ✅ Complete end-to-end CI/CD

#### Use Case:
- Production-ready deployment pipeline
- Automated end-to-end application deployment
- Continuous deployment from code commit to production

#### When to Use:
- Production environment
- Fully automated CI/CD workflow
- Auto-deployment on every successful build
- Best for mature projects with stable CI/CD processes

---

## 📊 Comparison Table

| Feature | jenkinsfile | jenkinspipeline 2 | jenkinspipeline 3 |
|---------|-------------|-------------------|-------------------|
| **Checkout Code** | ✅ | ✅ | ✅ |
| **Build Docker Image** | ✅ | ✅ | ✅ |
| **Show Images** | ✅ | ✅ | ❌ |
| **Docker Hub Login** | ❌ | ✅ | ✅ |
| **Push to Registry** | ❌ | ✅ | ✅ |
| **Pull Image** | ❌ | ❌ | ✅ |
| **Deploy Container** | ❌ | ❌ | ✅ |
| **Pipeline Type** | CI Only | CI/CD (Partial) | Full CI/CD |
| **Stages Count** | 3 | 4 | 5 |
| **Production Ready** | ❌ | ⚠️ (Partial) | ✅ |

---

## 🔄 Pipeline Evolution

```
jenkinsfile (Basic CI)
    ↓ (Enhancement)
jenkinspipeline 2 (CI/CD - Push)
    ↓ (Enhancement)
jenkinspipeline 3 (Full CI/CD - Push & Deploy)
```

### Progression:
1. **Stage 1**: Build and test Docker images locally
2. **Stage 2**: Push built images to DockerHub registry
3. **Stage 3**: Deploy containers to production automatically

---

## 🛠️ How Files Work Together

```
┌─────────────────────────────────────┐
│   jenkinsfile (or variants)         │
│   (Jenkins Pipeline Orchestration)  │
└────────────────┬────────────────────┘
                 │
         ┌───────▼──────────┐
         │ Builds Container │
         │   (Docker build) │
         └───────┬──────────┘
                 │
         ┌───────▼──────────┐
         │   Dockerfile     │
         │  + index.js      │
         │  (Creates Image) │
         └───────┬──────────┘
                 │
    ┌────────────┼────────────┐
    │            │            │
 ┌──▼──┐    ┌────▼────┐  ┌────▼─────┐
 │ Push│    │   Pull  │  │  Deploy  │
 │ Hub │ → │  Image  │ →│Container │
 └─────┘    └─────────┘  └──────────┘
            (Pipeline 2 & 3 only)
```

---

## 🚀 Usage Guide

### For Development (Use `jenkinsfile`):
1. Set up Jenkins server
2. Create new Pipeline job
3. Point to `jenkinsfile`
4. Build and test Docker images locally

### For Registry Sharing (Use `jenkinspipeline 2`):
1. Set up Docker Hub credentials in Jenkins
2. Use `jenkinspipeline 2`
3. Images pushed automatically on successful build
4. Useful for team collaboration

### For Production (Use `jenkinspipeline 3`):
1. Configure Docker Hub credentials
2. Ensure port 80 is available on deployment server
3. Use `jenkinspipeline 3`
4. Full automated deployment on each commit

---

## 📝 Summary

| File | Role | When to Use |
|------|------|------------|
| **index.js** | Application Logic | Always required - main application |
| **Dockerfile** | Container Blueprint | Required for all Docker operations |
| **jenkinsfile** | Basic CI Pipeline | Development & testing phase |
| **jenkinspipeline 2** | Extended CI/CD | Registry sharing & staging |
| **jenkinspipeline 3** | Full CI/CD | Production deployment |

---

## ✅ Quick Reference

- **Start here**: `index.js` + `Dockerfile` + `jenkinsfile`
- **Add sharing**: Add `jenkinspipeline 2`
- **Go live**: Switch to `jenkinspipeline 3`

---

*Last Updated: 2026-05-16*
