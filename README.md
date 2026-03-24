# Sample App

A simple Go HTTP server that serves solid-color PNG images. It is containerised with Docker and deployed to Google Kubernetes Engine (GKE) via Google Cloud Build.

## Overview

The application exposes two endpoints on port **8080**:

| Endpoint | Description |
|----------|-------------|
| `/blue`  | Returns a 100×100 blue PNG image |
| `/red`   | Returns a 100×100 red PNG image  |

## Project Structure

```
sample-app/
├── main.go               # Go application source
├── Dockerfile            # Multi-stage Docker build
├── cloudbuild.yaml       # Cloud Build pipeline (production)
├── cloudbuild-dev.yaml   # Cloud Build pipeline (development)
├── prod/
│   └── deployment.yaml   # Kubernetes deployment – production namespace
└── dev/
    └── deployment.yaml   # Kubernetes deployment – dev namespace
```

## Prerequisites

- [Go 1.19+](https://go.dev/dl/)
- [Docker](https://docs.docker.com/get-docker/)
- A Google Cloud project with the following APIs enabled:
  - Cloud Build
  - Artifact Registry
  - Google Kubernetes Engine

## Running Locally

```bash
go run main.go
```

The server will start on `http://localhost:8080`.

```bash
# Fetch a blue image
curl http://localhost:8080/blue --output blue.png

# Fetch a red image
curl http://localhost:8080/red --output red.png
```

## Building the Docker Image

```bash
docker build -t hello-app .
docker run -p 8080:8080 hello-app
```

## CI/CD with Google Cloud Build

### Production pipeline (`cloudbuild.yaml`)

Triggered on pushes to the main branch. Steps:

1. **Compile** the Go application.
2. **Build** the Docker image and tag it as `v2.0`.
3. **Push** the image to Artifact Registry (`us-east1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:v2.0`).
4. **Deploy** to the `prod` namespace of the `hello-cluster` GKE cluster in `us-east1-c`.

### Development pipeline (`cloudbuild-dev.yaml`)

Used for development builds. Steps mirror the production pipeline but target the `dev` namespace and use a configurable image version tag.

## Kubernetes Deployments

Both environments run **3 replicas** and expose port **8080**.

| Environment | Namespace | Deployment name          |
|-------------|-----------|--------------------------|
| Production  | `prod`    | `production-deployment`  |
| Development | `dev`     | `development-deployment` |

## License

Licensed under the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0).
