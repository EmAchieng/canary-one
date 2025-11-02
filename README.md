# Project Code Structure

## The UpCommerce Application

At the core of this project is a lightweight Flask application that represents a simplified version of UpCommerce's front end. The application serves an HTML page with a configurable background color:
- white for the stable version (v1)
- green for the canary version (v2)

This visual distinction makes it easy to verify a canary deployment in action. The application is instrumented with Prometheus metrics and exposes metrics at the /metrics endpoint. Metrics include request counts by version, which let you monitor traffic distribution between stable and canary deployments. While simplified, this app demonstrates the principles of canary deployments and monitoring.

## Docker Configuration

Pre-built Docker images are available on Docker Hub:
- uonyeka/canary-demo:v1
- uonyeka/canary-demo:v2

These images are usable on Windows and Linux (including GitHub Codespaces). If you prefer to build images locally or for other architectures, the repository includes a Dockerfile and requirements.txt. The Dockerfile is based on Python 3.9-slim and:
- copies the application code
- installs dependencies from requirements.txt
- sets environment variables for version identification

To build your own image, update the image repository and tags in values.yaml or build locally with docker build and push to your registry.

## Helm Chart Structure

The canary-demo Helm chart manages both stable and canary deployments. Key files in the chart templates directory:
- deployment.yaml: handles main and canary deployments
- service.yaml: creates service endpoints
- ingress.yaml: manages traffic routing and includes canary annotations
- configmap.yaml: holds environment-specific configuration
- servicemonitor.yaml: configures Prometheus monitoring

Use values.yaml to customize:
- image versions and repositories
- replica counts
- resource requests and limits
- canary rollout settings (percentage, annotations, etc.)

This structure follows Helm best practices while remaining simple for learning and experimentation.

## Unit Testing

Tests are located at ./tests/test_app.py and use Python's unittest and the requests library. The suite validates:
- correct responses from main and canary deployments (version-specific content and background colors)
- metrics exposition at /metrics
- expected traffic distribution (for example, ~20% of traffic to the canary when configured)

Tests make HTTP requests with appropriate Host headers to simulate real traffic and confirm the canary behavior.

## Quick local setup (Minikube)

Run the commands below in your Codespace or local terminal:

minikube start

minikube addons enable ingress

kubectl create namespace canary-demo

These commands create a single-node cluster, enable the ingress controller, and create the canary-demo namespace for deployments.

## Notes

- Update values.yaml if you change image names, tags, or resource requirements.
- Use kubectl and Helm to deploy into the canary-demo namespace.
- Inspect metrics via Prometheus and confirm canary traffic split using the included tests.
