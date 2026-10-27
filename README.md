# Containerized Flask App: GitHub Actions CI/CD to AWS EC2

**DevOps portfolio project** | Python Flask · Docker · GitHub Actions · Docker Hub · AWS EC2 · SSH deployment

This repository contains a small Flask application and an automated build/publish/deploy workflow. It demonstrates the delivery path from a source-code push to a container image and an EC2-hosted application.

> **Project scope:** This repository defines a working delivery pattern for a demo application. It is not presented as a production-grade, highly available service or as evidence of client deployments. See the limitations below.

## Delivery architecture

```text
Push to main
    |
GitHub Actions
    |-- Checkout source
    |-- Docker Buildx build
    |-- Authenticate to Docker Hub
    |-- Publish flask-app:latest
    |
    +--> SSH into EC2 host
         |-- docker pull
         |-- docker stop/rm previous flask-app (if present)
         +-- docker run -d -p 80:5000
                         |
                  Flask / on port 5000
```

**What the repository actually contains**

| File | Purpose |
| --- | --- |
| `app.py` | Minimal Flask endpoint at `/` returning `Hello from Docker Lab!` |
| `Dockerfile` | Python 3.12-slim image, installs Flask dependency, exposes port 5000 |
| `requirements.txt` | Python dependencies |
| `.github/workflows/docker.yml` | Push-triggered image build, Docker Hub push, and EC2 deployment jobs |
| `Docker_Flask_Lab_Notes.txt` | Local containerization steps and lab notes |
| `*.png` | Screenshots of local build/run and app verification steps |

## Run it locally

```bash
git clone https://github.com/demetriusvharvey/flask-docker-ci-cd.git
cd flask-docker-ci-cd
docker build -t flask-docker-lab .
docker run --rm -d --name flask-app -p 5000:5000 flask-docker-lab
curl http://localhost:5000/
docker logs flask-app
docker stop flask-app
```

Expected response from the source application: `Hello from Docker Lab!`.

## How the GitHub Actions pipeline works

The workflow in `.github/workflows/docker.yml` runs on pushes to `main`:

1. **Build:** Check out source, prepare Docker Buildx, and build the app image.
2. **Publish:** Authenticate to Docker Hub and push `DOCKERHUB_USERNAME/flask-app:latest`.
3. **Deploy:** A dependent job connects to an EC2 host over SSH, pulls the image, stops/removes the previous demo container if present, and starts the new container mapping host port 80 to container port 5000.

### Required GitHub Actions secrets

| Secret | Used for |
| --- | --- |
| `DOCKERHUB_USERNAME` | Docker Hub account/namespace |
| `DOCKERHUB_TOKEN` | Docker Hub access token with the minimum required permissions |
| `EC2_HOST` | EC2 IP or DNS name |
| `EC2_USER` | EC2 SSH account |
| `EC2_SSH_KEY` | Private key matching the server's authorized public key |

On EC2, install and start Docker; confirm the SSH user is permitted to run Docker commands; configure inbound SSH **only from authorized sources**, and expose HTTP only when intentionally testing the application. Never commit passwords, access tokens, or private keys.

## Verification and troubleshooting

| Symptom | Investigation |
| --- | --- |
| Build/push job fails | Review the workflow logs, Dockerfile, dependency installation, and Docker Hub credentials |
| SSH deployment fails | Check `EC2_HOST`, username, EC2 key permissions, SSH access controls, host connectivity, and Docker permissions |
| App cannot be reached | Check `docker ps`, `docker logs flask-app`, EC2 security groups, instance firewall, port 80, and port mapping `80:5000` |
| Container stops | Check process logs, image command, Flask dependency and startup |
| Old version appears | Inspect the image that was pulled and container lifecycle; using a mutable `latest` tag makes traceability harder |

Useful EC2 diagnostics:

```bash
docker ps -a
docker logs --tail=100 flask-app
docker images
sudo ss -lntp | grep ':80'
curl -I http://localhost/
```

## Engineering decisions and production-readiness gaps

- **Automation:** The repository includes a build/publish/deploy workflow, but the workflow currently does **not** run unit tests, linting, vulnerability scans, or staged approvals. The pipeline is better described as *CI/CD automation* than a complete quality-gated production pipeline.
- **Deployment behavior:** Stopping and replacing the container creates potential downtime. A production system would typically use immutable image tags, health checks, rollback, and a service/orchestrator rather than a single SSH-managed VM.
- **Security:** The workflow uses SSH deployment and GitHub repository secrets. Harden server access, minimize Docker Hub token privileges, and consider GitHub environments and federated cloud credentials as the project evolves.
- **Availability:** One EC2 instance is a single point of failure. There is no load balancer, auto-scaling, Kubernetes, or service-level monitoring in this repository.

## Suggested next iterations

Add automated Flask tests, PR checks, fixed image tags, image vulnerability scanning, deployment verification, rollback handling, and CloudWatch or Prometheus/Grafana observability. Those are **future enhancements**, not features already implemented here.

**Interview walkthrough:** Explain the push-to-deploy sequence, secret management, port translation, how you would respond to a failing deployment, and what you would change to make the design safer and more reliable.
