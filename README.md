![DevOps-Projects](https://imgur.com/qimdPIU.png)

<!-- MARKDOWN LINKS & IMAGES -->
[forks-shield]: https://img.shields.io/github/forks/devcloudninjas/DevOps-Projects?style=for-the-badge&logo=github&logoColor=white&color=orange
[forks-url]: https://github.com/devcloudninjas/DevOps-Projects/network/members
[stars-shield]: https://img.shields.io/github/stars/devcloudninjas/DevOps-Projects.svg?style=for-the-badge&logo=github&logoColor=white&color=brightgreen
[stars-url]: https://github.com/devcloudninjas/DevOps-Projects/stargazers

[![Forks][forks-shield]][forks-url]
[![Stars][stars-shield]][stars-url]

---

## 🚀 Real World Projects for Aspiring DevOps Engineers [Beginner to Advanced]

_Welcome to the ultimate resource for **learning DevOps through hands-on projects!** This repository is designed to cater to aspiring **DevOps engineers** of all skill levels — from beginners taking their first steps, to advanced users looking to deepen their expertise._

> 💡 **New in this edition:** All cloud infrastructure examples use **[Floci](https://floci.io)** — a free, open-source local cloud emulator — instead of real AWS. No account needed. No credit card. No surprise bills.

---

## 📦 What is Floci?

[Floci](https://floci.io) is a lightweight, MIT-licensed local cloud emulator that runs AWS-compatible services right on your machine inside Docker. It supports 47 AWS services (S3, RDS, EC2, ECS, ECR, Lambda, SQS, and more), starts in ~24ms, and requires zero authentication tokens.

**Why Floci instead of real AWS?**

| Pain Point (Real AWS) | Floci Solution |
|---|---|
| 💸 Unexpected bills | 100% free, always |
| 🐢 Slow feedback loops | Starts in 24 milliseconds |
| 🌐 Internet required | Works fully offline |
| 🔐 IAM keys & account setup | No sign-up, no tokens ever |
| 😰 Fear of breaking prod | Fully isolated local environment |

**Quick Start:**
```bash
# Start Floci (AWS emulator on port 4566)
docker run -d --name floci -p 4566:4566 floci/floci:latest

# Point AWS CLI at Floci
aws configure
# Access Key: test | Secret Key: test | Region: us-east-1

# Test it — create an S3 bucket locally
aws --endpoint-url=http://localhost:4566 s3 mb s3://my-first-bucket
```

---

## 🗂️ Repository Contents

> [!IMPORTANT]
> This repository contains a comprehensive collection of DevOps projects, each crafted to provide hands-on learning experience. Projects are categorized by skill level so everyone can find a suitable starting point and progressively grow.
>
> - 🟢 **Beginner Projects:** Simple, foundational projects introducing basic DevOps concepts and tools.
> - 🟡 **Intermediate Projects:** More complex projects requiring a solid understanding of DevOps fundamentals.
> - 🔴 **Advanced Projects:** Challenging projects designed to push your limits and deepen your understanding of sophisticated DevOps practices.

---

## 🔗 Integration of DevOps with Other Technologies

> [!NOTE]
> DevOps doesn't exist in isolation. This repository includes projects that integrate DevOps with several key technologies, showing how they work together in real-world scenarios.
>
> - 🤖 **Machine Learning:** Implement DevOps practices to manage and deploy ML models efficiently.
> - 🌿 **Version Control with Git & GitHub:** Manage code versions and collaborate using Git and GitHub.
> - ⚙️ **CI/CD Pipelines:** Automate testing and deployment using Jenkins, GitHub Actions, and more.
> - ☁️ **Local Cloud with Floci:** Deploy and test applications using Floci's AWS-compatible emulator — no cloud account needed.
> - 🐳 **Containerization (Docker, Kubernetes):** Ensure your applications run consistently across all environments.

---

## 🛠️ Tech Stack Used Across Projects

| Category | Tools |
|---|---|
| **Local Cloud** | Floci (AWS emulator — S3, RDS, EC2, ECR, Lambda) |
| **Containers** | Docker, Docker Compose, Kubernetes |
| **CI/CD** | Jenkins, GitHub Actions |
| **Build Tools** | Maven, Gradle |
| **IaC** | Terraform (pointed at Floci endpoints) |
| **Monitoring** | Prometheus, Grafana |
| **Code Quality** | SonarQube |
| **Version Control** | Git, GitHub |
| **Languages** | Java (Spring Boot), Python, Bash |

---

## 📐 Project Scope

> [!IMPORTANT]
> Projects span a wide array of topics within the DevOps domain, each designed for practical, real-world experience:
>
> - 🚢 **Automated Deployment:** Automate app deployment using Jenkins pipelines and Docker containers, tested locally with Floci.
> - 🔄 **CI/CD:** Set up and manage full CI/CD pipelines — code commit → build → test → deploy, all automated.
> - 🏗️ **Infrastructure as Code (IaC):** Use Terraform against Floci endpoints to manage infrastructure through code without touching real cloud.
> - 📊 **Monitoring & Logging:** Implement Prometheus and Grafana to track application performance and catch issues early.
> - 🔒 **Security & Compliance:** Incorporate security scanning (SonarQube, Trivy) into DevOps workflows.
> - 📈 **Scalability & Performance:** Understand auto-scaling concepts and simulate them locally.

---

## 🏁 Getting Started

### Prerequisites

Install these tools before starting any project:

```bash
# 1. Docker (required for Floci and containers)
#    → https://docs.docker.com/get-docker/

# 2. Floci CLI (optional but handy)
curl -fsSL https://floci.io/install.sh | sh
floci start && eval $(floci env)

# 3. AWS CLI (works with Floci out of the box)
#    → https://aws.amazon.com/cli/

# 4. Git
#    → https://git-scm.com/

# 5. Java JDK 11+
#    → https://adoptium.net/
```

### Recommended Terminal

Use **Ubuntu WSL** (Windows) or your native terminal on macOS/Linux. All commands in this repo are Linux-style and work best in a bash environment.

---

## 📁 Project List

### 🟢 Beginner

| # | Project | Tools Used |
|---|---|---|
| 01 | Java Login App — 3-Tier Architecture | Docker, Maven, Floci RDS, Jenkins |
| 02 | Static Website Hosting | Floci S3, Docker |
| 03 | Automated CI Pipeline | Git, Jenkins, Maven |

### 🟡 Intermediate

| # | Project | Tools Used |
|---|---|---|
| 04 | Containerized Microservices | Docker Compose, Floci ECR |
| 05 | Infrastructure as Code | Terraform + Floci |
| 06 | Monitoring Stack | Prometheus, Grafana, Node Exporter |

### 🔴 Advanced

| # | Project | Tools Used |
|---|---|---|
| 07 | Kubernetes Deployment | k8s, Helm, Floci |
| 08 | Full DevSecOps Pipeline | Jenkins, SonarQube, Trivy, Docker |
| 09 | GitOps with ArgoCD | ArgoCD, GitHub, Kubernetes |

---

## 🌟 Why Explore This Repository?

> [!NOTE]
>
> - 🧪 **Hands-on Experience:** Every project is practical. You solve real-world challenges, not toy exercises.
> - 📈 **Skill Enhancement:** Structured learning path from zero to job-ready DevOps engineer.
> - 🏭 **Industry Relevance:** Reflects current industry practices — CI/CD, containers, IaC, monitoring, security.
> - 💰 **Zero Cost:** Thanks to Floci, every cloud-based project runs free on your laptop.
> - 🤝 **Community Engagement:** Share projects, seek feedback, and collaborate with fellow learners.

---

## 🤝 Contributing

Contributions are welcome! If you have a project idea or improvement:

1. Fork the repo
2. Create a branch: `git checkout -b feature/my-project`
3. Commit your changes: `git commit -m "Add: new project"`
4. Push and open a Pull Request

---

## 📜 License

This repository is open-source and available under the [MIT License](LICENSE).

---

<p align="center">
  Made with ❤️ for the DevOps community · <a href="https://floci.io">Powered locally by Floci</a>
</p>

