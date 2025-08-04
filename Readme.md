# 🚀 Jenkins CI/CD Pipeline with Docker, AWS ECR, and ECS

This project demonstrates a full CI/CD pipeline built using **Jenkins**, **Maven**, **Docker**, **SonarQube**, and **AWS (ECR + ECS)**. It automatically builds, tests, analyzes, packages, containers, and deploys a Java web application.

---

## 🛠️ Tools & Technologies

- **Jenkins** – Automation server to orchestrate CI/CD
- **Maven** – Java build & dependency management
- **Docker** – Containerization of the application
- **SonarQube** – Static code analysis and quality gate checks
- **Checkstyle** – Code style enforcement
- **AWS ECR** – Docker image registry
- **AWS ECS** – Application deployment on ECS cluster
- **GitHub** – Source code repository

---

## 📁 Project Structure

Jenkins_CICDproject/
├── Docker-files/
│ └── app/
│ └── multistage/
│ └── Dockerfile
├── Jenkinsfile
├── pom.xml
├── src/
└── README.md

