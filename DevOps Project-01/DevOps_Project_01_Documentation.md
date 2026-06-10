**DevOps Project-01**

Deploy Java Login Application with Docker & MySQL

Local 3-Tier Architecture using Floci + Docker Compose
# **Project Overview**
This project demonstrates how to deploy a Java Spring Boot Login Application on a local 3-tier architecture using Docker containers. Instead of provisioning cloud resources on AWS, we use Floci — an experimental local cloud simulator — to replicate cloud infrastructure on your own machine with zero cost.

|**Goal:** Register and log in users via a browser, backed by a MySQL database — all running inside Docker containers orchestrated by Docker Compose.|
| :- |

## **Architecture**
**Browser  →  Java Spring Boot App (port 8080)  →  MySQL Database (port 3306)**

Each middle and data tier runs in its own Docker container. Docker Compose handles networking and startup order.

## **Tech Stack**

|**Component**|**Technology**|**Version**|
| :- | :- | :- |
|Application|Java Spring Boot + Tomcat|2\.2.4|
|Build Tool|Apache Maven|3\.x|
|Database|MySQL|8\.0|
|Containerization|Docker + Docker Compose|Latest|
|Base Image|eclipse-temurin (JRE)|11-jre-jammy|
|Cloud Simulator|Floci|Latest|
|OS / Shell|Ubuntu / WSL2|-|


# **Phase 1 — Local Setup & App Verification**
In this phase we start Floci, launch MySQL, clone the repo, fix the database schema, build the Java app with Maven, and verify the full login flow works before containerising anything.

|**1**|**Start Floci with correct network binding**|
| :-: | :- |
|docker run -d --name floci \||
|`  `-p 4566:4566 \||
|`  `--network bridge \||
|`  `floci/floci:latest||

|**2**|**Get Floci's container IP and save it**|
| :-: | :- |
|FLOCI\_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' floci)||
|echo $FLOCI\_IP||
|echo "export FLOCI\_IP=$FLOCI\_IP" >> ~/.bashrc && source ~/.bashrc||

|**3**|**Verify Floci is healthy**|
| :-: | :- |
|curl http://localhost:4566/\_localstack/health | python3 -m json.tool||

|**4**|**Start MySQL container**|
| :-: | :- |
|docker run -d \||
|`  `--name mysql-db \||
|`  `-e MYSQL\_ROOT\_PASSWORD=root123 \||
|`  `-e MYSQL\_DATABASE=logindb \||
|`  `-e MYSQL\_USER=admin \||
|`  `-e MYSQL\_PASSWORD=password123 \||
|`  `-p 3306:3306 \||
|`  `mysql:8.0||

|**5**|**Verify MySQL is running and test connection**|
| :-: | :- |
|docker ps||
|docker exec -it mysql-db mysql -u admin -ppassword123 logindb -e "SHOW DATABASES;"||
|**Note:** The warning 'Using a password on the command line interface can be insecure' is normal — ignore it.||

|**6**|**Clone the repository**|
| :-: | :- |
|git clone https://github.com/EmperorWhiteBeard/DevOps-Cloud-Projects-Portfolio.git||
|cd DevOps-Cloud-Projects-Portfolio/DevOps\ Project-01||

|**7**|**Create the Employee table in MySQL**|
| :-: | :- |

The app's source code expects a table named Employee with these exact columns:

|docker exec -it mysql-db mysql -u admin -ppassword123 logindb -e "|
| :- |
|CREATE TABLE Employee (|
|`  `id int unsigned auto\_increment not null,|
|`  `first\_name varchar(250),|
|`  `last\_name varchar(250),|
|`  `email varchar(250),|
|`  `username varchar(250),|
|`  `password varchar(250),|
|`  `regdate timestamp,|
|`  `primary key (id)|
|);"|

|**8**|**Verify the table was created**|
| :-: | :- |
|docker exec -it mysql-db mysql -u admin -ppassword123 logindb -e "DESCRIBE Employee;"||

|**9**|**Update application.properties to point to local MySQL**|
| :-: | :- |
|cat > Java-Login-App/src/main/resources/application.properties << 'EOF'||
|spring.mvc.view.prefix=/pages/||
|spring.mvc.view.suffix=.jsp||
|spring.datasource.url=jdbc:mysql://127.0.0.1:3306/logindb||
|spring.datasource.username=admin||
|spring.datasource.password=password123||
|EOF||

|**10**|**Build the Java app with Maven**|
| :-: | :- |
|cd Java-Login-App||
|mvn clean package -DskipTests||

This downloads dependencies and compiles the app. First run takes 2–3 minutes. Success looks like:

|[INFO] BUILD SUCCESS|
| :- |

|**11**|**Run the app and test in browser**|
| :-: | :- |
|java -jar target/dptweb-1.0.war||

Wait for the startup message, then open your browser:

|Register:  http://localhost:8080/register|
| :- |
|Login:     http://localhost:8080|

Fill in all fields on the register page (First Name, Last Name, Email, Username, Password), then log in with those credentials.

|**12**|**Verify user is saved in the database**|
| :-: | :- |
|docker exec -it mysql-db mysql -u admin -ppassword123 logindb -e "SELECT \* FROM Employee;"||

|**✅ Phase 1 Complete — Login works, Register works, Database stores users, App runs on localhost:8080**|
| :- |


# **Phase 2 — Dockerize the Java App**
In this phase we package the Java app into a Docker image and use Docker Compose to orchestrate both the app and the database as containers.

|**1**|**Navigate to the Java app folder**|
| :-: | :- |
|cd ~/DevOps-Cloud-Projects-Portfolio/DevOps\ Project-01/Java-Login-App||

|**2**|**Create the Dockerfile**|
| :-: | :- |
|cat > Dockerfile << 'EOF'||
|FROM eclipse-temurin:11-jre-jammy||
|WORKDIR /app||
|COPY target/dptweb-1.0.war app.war||
|EXPOSE 8080||
|ENTRYPOINT ["java", "-jar", "app.war"]||
|EOF||
|**Why eclipse-temurin?** The original openjdk:11-jre-slim image was retired from Docker Hub. eclipse-temurin:11-jre-jammy is the recommended replacement.||

|**3**|**Build the Docker image**|
| :-: | :- |
|docker build -t java-login-app:1.0 .||

Success output:

|Successfully built b2654b9d5846|
| :- |
|Successfully tagged java-login-app:1.0|

|**4**|**Create the DB init script**|
| :-: | :- |
|mkdir -p ../init-db||
|cat > ../init-db/init.sql << 'EOF'||
|CREATE TABLE IF NOT EXISTS Employee (||
|`  `id int unsigned auto\_increment not null,||
|`  `first\_name varchar(250),||
|`  `last\_name varchar(250),||
|`  `email varchar(250),||
|`  `username varchar(250),||
|`  `password varchar(250),||
|`  `regdate timestamp,||
|`  `primary key (id)||
|);||
|EOF||

This script runs automatically when the MySQL container starts for the first time, creating the Employee table without any manual steps.

|**5**|**Create docker-compose.yml**|
| :-: | :- |
|cd ..||
|cat > docker-compose.yml << 'EOF'||
|version: '3.8'||
| ||
|services:||
|`  `mysql-db:||
|`    `image: mysql:8.0||
|`    `container\_name: mysql-db||
|`    `environment:||
|`      `MYSQL\_ROOT\_PASSWORD: root123||
|`      `MYSQL\_DATABASE: logindb||
|`      `MYSQL\_USER: admin||
|`      `MYSQL\_PASSWORD: password123||
|`    `ports:||
|`      `- "3306:3306"||
|`    `volumes:||
|`      `- mysql-data:/var/lib/mysql||
|`      `- ./init-db:/docker-entrypoint-initdb.d||
| ||
|`  `java-app:||
|`    `image: java-login-app:1.0||
|`    `container\_name: java-app||
|`    `ports:||
|`      `- "8080:8080"||
|`    `depends\_on:||
|`      `- mysql-db||
|`    `environment:||
|`      `SPRING\_DATASOURCE\_URL: jdbc:mysql://mysql-db:3306/logindb||
|`      `SPRING\_DATASOURCE\_USERNAME: admin||
|`      `SPRING\_DATASOURCE\_PASSWORD: password123||
| ||
|volumes:||
|`  `mysql-data:||
|EOF||

|**6**|**Stop existing containers and launch with Docker Compose**|
| :-: | :- |
|docker stop mysql-db java-app 2>/dev/null; docker rm mysql-db java-app 2>/dev/null||
|docker-compose up -d||

Expected output:

|Creating network "devopsproject-01\_default" with the default driver|
| :- |
|Creating mysql-db ... done|
|Creating java-app ... done|

|**7**|**Verify containers are running**|
| :-: | :- |
|docker ps||

You should see three containers: java-app (8080), mysql-db (3306), and floci (4566).

|**8**|**Check app logs**|
| :-: | :- |
|docker logs java-app||

Look for:

|Started MyWebAppApplication in X seconds (JVM running for X)|
| :- |

|**9**|**Test the full Docker stack in browser**|
| :-: | :- |
|Register:  http://localhost:8080/register||
|Login:     http://localhost:8080||

Register a new user, log in, then verify the record was saved:

|docker exec -it mysql-db mysql -u admin -ppassword123 logindb -e "SELECT \* FROM Employee;"|
| :- |

|**✅ Phase 2 Complete — Both containers running, app and database communicating via Docker Compose networking**|
| :- |


# **Phase 3 — Push to GitHub**

|**1**|**Add .gitignore to exclude build artifacts**|
| :-: | :- |
|cat > Java-Login-App/.gitignore << 'EOF'||
|target/||
|\*.class||
|\*.log||
|EOF||

|**2**|**Stage and commit all changes**|
| :-: | :- |
|cd ~/DevOps-Cloud-Projects-Portfolio||
|git add .||
|git commit -m "Project-01: Deploy Java Login App with Docker and MySQL||
| ||
|- Added Dockerfile for Java Spring Boot app||
|- Added docker-compose.yml to run app + MySQL together||
|- Added init-db/init.sql for automatic DB table creation||
|- Updated application.properties to use containerized MySQL||
|- App tested: register and login working end-to-end"||

|**3**|**Push to GitHub**|
| :-: | :- |
|git push origin main||
|**Authentication:** GitHub no longer accepts passwords for git push. Use a Personal Access Token (PAT) from github.com/settings/tokens as the password.||

|**✅ Phase 3 Complete — Full project pushed to GitHub portfolio**|
| :- |


# **Final Verification Checklist**

- Floci container running on port 4566
- MySQL container running on port 3306
- Java app container running on port 8080
- Register page works at http://localhost:8080/register
- Login page works at http://localhost:8080
- User records visible in MySQL via SELECT \* FROM Employee
- All three containers start cleanly with docker-compose up -d
- Code, Dockerfile, docker-compose.yml and init.sql pushed to GitHub


# **Key Learnings**

**Portability & Consistency**

Writing a clean Dockerfile eliminates the 'it works on my machine' problem. The app image behaves identically on any machine that has Docker installed.

**Industry Standards**

Docker is universally adopted across modern engineering teams. Understanding Dockerfiles, image tagging, and container networking directly maps to real-world microservice deployments.

**Resource Efficiency**

Containers are significantly lighter and faster to spin up and tear down compared to traditional Virtual Machines, making them ideal for CI/CD pipelines, local development, and rapid iteration.

**Local Cloud Simulation with Floci**

Floci provides a local AWS-compatible cloud environment, allowing you to practice cloud DevOps workflows without incurring any cloud costs or managing AWS credentials. It is an experimental tool and a practical choice for portfolio projects.

**Docker Compose Networking**

Services defined in docker-compose.yml can reach each other by their service name as a hostname (e.g. mysql-db). This is how the Java app connects to the database inside Docker without knowing any IP address.

**Database Initialisation Automation**

Mounting an SQL file into /docker-entrypoint-initdb.d/ inside the MySQL container means the schema is created automatically on first startup — no manual SQL steps required after docker-compose up.

#
# **Repository**
**GitHub: [github.com/EmperorWhiteBeard/DevOps-Cloud-Projects-Portfolio](https://github.com/EmperorWhiteBeard/DevOps-Cloud-Projects-Portfolio)**

**LinkedIn**: www.linkedin.com/in/mizhabnp

**Author:** Mizhab Mujeeb NP

