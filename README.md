Flask Docker CI/CD Deployment

Project Overview



This project demonstrates a CI/CD pipeline for a Flask application using Docker and GitHub Actions. The Docker image is built and pushed to Docker Hub, then automatically deployed to an AWS EC2 instance.



Skills Highlighted:



Dockerizing Python/Flask apps



CI/CD with GitHub Actions



Docker Hub integration



AWS EC2 deployment



Troubleshooting and pipeline automation



Features



Dockerized Flask app



Automated build and push to Docker Hub



Automatic deployment to EC2



Publicly accessible Flask app via EC2 instance



Error handling and troubleshooting documented



Architecture / Pipeline

Local Machine (app.py, Dockerfile)

&nbsp;          │

&nbsp;          ▼

&nbsp;      GitHub Repo

&nbsp;          │

&nbsp;          ▼

GitHub Actions Workflow (build \& push Docker image)

&nbsp;          │

&nbsp;          ▼

&nbsp;      Docker Hub

&nbsp;          │

&nbsp;          ▼

&nbsp;       EC2 Instance

&nbsp;          │

&nbsp;          ▼

&nbsp;  Docker Container Running Flask App

&nbsp;          │

&nbsp;          ▼

&nbsp;      Live Site in Browser



Getting Started

Requirements



Docker installed locally



GitHub account



Docker Hub account



AWS account (for EC2 instance)



Setup Steps



Clone repo:



git clone https://github.com/<username>/flask-docker-ci-cd.git

cd flask-docker-ci-cd





Build Docker image locally (optional test):



docker build -t flask-docker-lab .

docker run -p 5000:5000 flask-docker-lab





Push changes to GitHub to trigger CI/CD workflow



Workflow automatically builds Docker image, pushes to Docker Hub, and deploys to EC2 (if secrets are configured).



GitHub Actions Secrets



DOCKERHUB\_USERNAME → Docker Hub username



DOCKERHUB\_TOKEN → Docker Hub personal access token



EC2\_HOST → EC2 public IP



EC2\_USER → EC2 username



EC2\_SSH\_KEY → EC2 private key contents (.pem)



Known Issues / Troubleshooting



Container exits immediately → use detached mode:



docker run -d -p 80:5000 --name flask-app flask-docker-lab





Browser can’t access app → check EC2 Security Group allows HTTP (port 80)



Git push errors → git pull --rebase to sync remote



Docker Hub token insufficient → add R/W/D scopes

