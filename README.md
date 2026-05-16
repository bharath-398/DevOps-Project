# DevOps-Project

A DevOps project built with **Node.js**, **Docker**, and **Jenkins**.

## Project Files

| File | Description |
|------|-------------|
| `index.js` | Node.js web application (runs on port 3000) |
| `Dockerfile` | Docker configuration to containerize the app |
| `jenkinsfile` | Jenkins pipeline - Build Docker image |
| `jenkinspipeline 2` | Jenkins pipeline - Build & Push to DockerHub |
| `jenkinspipeline 3` | Jenkins pipeline - Full CI/CD (Build, Push & Deploy) |

## How to Run

```bash
node index.js
```
Then open http://localhost:3000 in your browser.

## Pipeline Stages

```
Build → Test → Deploy
```

Applications are containerized with Docker and pushed to DockerHub.
