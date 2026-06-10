# Deploy Java Login Application using Docker and MySQL

## Architecture
Browser -> Java App Container (8080) -> MySQL Container (3306)

## Tech Stack
- Application: Java Spring Boot 2.2.4 + JSP + Tomcat
- Database: MySQL 8.0
- Containerization: Docker + Docker Compose
- Build Tool: Maven

## How to Run
1. Clone the repo
2. cd into DevOps Project-01
3. Run: docker-compose up -d
4. Open: http://localhost:8080

## Verification
- Register at http://localhost:8080/register
- Login at http://localhost:8080
- User saved in MySQL confirmed

## Key Learnings
- Containerizing Java Spring Boot with Docker
- Docker Compose networking between containers
- DB initialization with SQL init scripts
