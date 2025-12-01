# Flask Docker CI/CD Deployment

A minimal Flask app packaged with Docker and shipped through GitHub Actions to Docker Hub and an EC2 host. Use this repo as a template for testing container-based delivery pipelines.

## Repository contents
- `app.py` – simple Flask endpoint returning "Hello from Docker Lab!".
- `Dockerfile` – builds a Python 3.12 Slim image and runs the app on port 5000.
- `requirements.txt` – Python dependencies for the container build.
- `Docker_Flask_Lab_Notes.txt` and screenshots – workflow notes and reference images.

## Prerequisites
- Docker installed locally.
- Accounts: GitHub, Docker Hub, and AWS (for EC2 deployment).
- An EC2 instance with SSH access and security group open for HTTP (port 80 or your chosen port).

## Local development
1. Clone and enter the repo:
   ```bash
   git clone https://github.com/<username>/flask-docker-ci-cd.git
   cd flask-docker-ci-cd
   ```
2. Build and run locally:
   ```bash
   docker build -t flask-docker-lab .
   docker run -p 5000:5000 --name flask-app flask-docker-lab
   ```
3. Visit http://localhost:5000 to verify the greeting message.

## GitHub Actions pipeline
- Trigger: push to `main` (adjust in workflow if needed).
- Jobs: build the Docker image, push to Docker Hub, then deploy to EC2.
- Secrets required in the GitHub repo settings:
  - `DOCKERHUB_USERNAME` – Docker Hub username.
  - `DOCKERHUB_TOKEN` – Docker Hub access token with read/write/delete scopes.
  - `EC2_HOST` – public IP or DNS of the instance.
  - `EC2_USER` – SSH username (e.g., `ubuntu`).
  - `EC2_SSH_KEY` – private key contents for SSHing into the instance.

## Deploying to EC2 (summary)
1. Ensure Docker is installed on the EC2 host.
2. Confirm the GitHub Actions secrets above are configured.
3. Push to `main`; the workflow will SSH into EC2, pull the latest image from Docker Hub, and run the container.
4. Visit `http://<EC2_HOST>` in a browser to see the Flask response.

## Common issues & fixes
- **Container exits immediately locally** → run in detached mode and map the port: `docker run -d -p 80:5000 --name flask-app flask-docker-lab`.
- **Cannot reach app on EC2** → verify security group rules allow inbound HTTP (port 80/5000) and that the container is running.
- **Git push is rejected** → `git pull --rebase` to sync before pushing.
- **Docker Hub authentication fails** → regenerate the token with the required scopes and update the GitHub secret.

## Useful commands
- Stop and remove local container:
  ```bash
  docker stop flask-app && docker rm flask-app
  ```
- View running containers:
  ```bash
  docker ps
  ```
- Tail logs on EC2 (after SSHing in):
  ```bash
  docker logs -f flask-app
  ```
