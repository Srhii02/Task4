🚀 CI/CD Pipeline for Kaiburr Task-1 (Spring Boot + Docker)

This document explains how the Continuous Integration and Continuous Deployment (CI/CD) pipeline was created for the Kaiburr Task-1 Spring Boot application using GitHub Actions.

The pipeline automatically builds the Java app, packages it into a Docker image, and pushes the image to the GitHub Container Registry (GHCR) whenever a new version tag is pushed.

🧰 Tools & Technologies Used
Tool	Purpose
GitHub Actions	Automates build and deployment process
Maven	Builds and packages the Spring Boot project
Docker	Containerizes the Java application
GitHub Container Registry (GHCR)	Stores and manages Docker images
Java 21 (Temurin)	Runtime environment for the application
⚙️ CI/CD Workflow Overview

Whenever a new version tag (like v1.0.0) is pushed to the GitHub repository:

🧩 GitHub Actions automatically triggers the workflow.

☕ Java setup is configured using Temurin JDK 21.

🏗️ Maven builds the project and generates the JAR file.

🐳 Docker builds a container image using the Dockerfile.

📦 Image is pushed to the GitHub Container Registry (GHCR).

🏁 The image can then be pulled and deployed on any server.

🧱 Workflow File Structure

The GitHub Actions workflow file is stored at:

.github/workflows/ci-cd.yml

📝 Complete Workflow File
name: Java CI/CD with Maven and GitHub Container Registry

on:
  push:
    tags:
      - 'v*.*.*'   # Runs only when version tags like v1.0.0 are pushed

permissions:
  contents: read
  packages: write

env:
  IMAGE_NAME: kaiburr-task1
  REGISTRY: ghcr.io
  OWNER: ${{ github.repository_owner }}

jobs:
  build-and-push-image:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Java (Temurin 21)
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '21'
          cache: maven

      - name: Build with Maven
        run: mvn -B package --file pom.xml

      - name: Log in to the GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ env.OWNER }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image to GHCR
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.OWNER }}/${{ env.IMAGE_NAME }}:${{ github.ref_name }}
            ${{ env.REGISTRY }}/${{ env.OWNER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
            ${{ env.REGISTRY }}/${{ env.OWNER }}/${{ env.IMAGE_NAME }}:latest

🔄 How the Pipeline Works
Step	Action	Description
1. Checkout	actions/checkout@v4	Clones the source code into the CI environment
2. Setup Java	actions/setup-java@v4	Installs and configures JDK 21
3. Build App	mvn package	Builds the Spring Boot JAR file
4. Login to GHCR	docker/login-action@v3	Authenticates to GitHub Container Registry
5. Docker Build & Push	docker/build-push-action@v4	Builds the Docker image and pushes it to GHCR
🧪 Triggering the Pipeline

To trigger the CI/CD pipeline, simply create and push a version tag:

git tag v1.0.0
git push origin v1.0.0


Once pushed, GitHub Actions will:

Build the project

Create a Docker image

Push it to the GHCR registry automatically 🎉

📦 Output (Docker Image)

The image is stored in GitHub Container Registry under:

ghcr.io/<your-username>/kaiburr-task1:<version-tag>


Example:

ghcr.io/srhii02/kaiburr-task1:v1.0.0


You can pull and run it manually:

docker pull ghcr.io/<username>/kaiburr-task1:v1.0.0
docker run -p 8080:8080 ghcr.io/<username>/kaiburr-task1:v1.0.0
