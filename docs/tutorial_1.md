[Home](index.md)
# Configuring Spring Boot Application for Deployment

Deploying a Spring Boot application can be an intricate task, but with the right setup and configuration, it can be streamlined for both development and production environments. This tutorial will guide you through the steps necessary to configure and deploy a Spring Boot application effectively.

## 1. Prerequisites

Before starting, ensure you have the following installed:

- **Java Development Kit (JDK)**: Version 11 or later.
- **Apache Maven or Gradle**: For building the application.
- **Spring Boot**: Version 2.5 or later.
- **A Git Repository**: For source control (e.g., GitHub, GitLab).
- **Docker**: (Optional) For containerization.
- **Cloud Provider Account**: e.g., AWS, Azure, or Heroku (optional).

## 2. Setting Up a Spring Boot Application

### 1. Create a Spring Boot Project:
- You can use [Spring Initializr](https://start.spring.io/) to generate a new Spring Boot project. Choose the dependencies you need, such as Spring Web, Spring Data JPA, etc.

### 2. Directory Structure:
Your project should have the following structure:
```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           └── DemoApplication.java
│   └── resources/
│       ├── application.properties (or application.yml)
│       └── static/
│       └── templates/
└── test/
    ├── java/
    └── resources/
```

### 3. Application Properties Configuration:
Configure your `application.properties` or `application.yml` to define key properties like:
```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```

## 3. Building the Application

### 1. Build with Maven:
Run the following command in the root directory:
```bash
mvn clean install
```
This will create an executable JAR file in the `target/` directory.

### 2. Build with Gradle:
Run the following command:
```bash
gradlew build
```
The executable JAR will be in the `build/libs/` directory.

## 4. Containerizing with Docker (Optional)

### 1. Create a Dockerfile:
In the root of your project, create a `Dockerfile`:
```dockerfile
FROM openjdk:11-jre-slim
VOLUME /tmp
ARG JAR_FILE=target/demo-0.0.1-SNAPSHOT.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```
This file specifies the base image (OpenJDK 11), copies the JAR file to the container, and defines the entry point to run the JAR.

### 2. Build Docker Image:
Run the following command to build the Docker image:
```bash
docker build -t myapp:latest .
```

### 3. Run the Docker Container:
Run the container using the command:
```bash
docker run -p 8080:8080 myapp:latest
```

## 5. Configuring for Deployment

### A. Using External Configurations

#### 1. Externalized Configuration:
You can externalize your configuration using environment variables, command-line arguments, or external property files:
```bash
java -jar app.jar --server.port=8081
```

#### 2. Profiles for Different Environments:
Define profiles in `application.properties`:
```properties
spring.profiles.active=prod
```
Create separate property files like `application-prod.properties` for production.

### B. Database Configuration

#### 1. Production Database Configuration:
For production, configure your database in `application-prod.properties`:
```properties
spring.datasource.url=jdbc:mysql://prod-db-host:3306/prod-db
spring.datasource.username=prod-user
spring.datasource.password=prod-pass
```

#### 2. Data Source Pooling:
Use a connection pooling library like HikariCP (default in Spring Boot 2.x):
```properties
spring.datasource.hikari.maximum-pool-size=20
```

## 6. Deploying to Production

### A. Deploying to a Cloud Provider

#### 1. Deploy to AWS Elastic Beanstalk:
- Package your application as a WAR or JAR file.
- Create an Elastic Beanstalk environment and deploy the application via the AWS Management Console or CLI.

#### 2. Deploy to Azure:
- Use the Azure Web App service.
- Deploy your JAR file directly through the Azure CLI or the Azure portal.

#### 3. Deploy to Heroku:
- Use Heroku's Git-based deployment system:
```bash
git push heroku master
```

### B. Deploying to a Server

#### 1. Copy the JAR File:
- Use SCP or any file transfer method to copy the JAR file to your server.

#### 2. Run the Application as a Service:
- Create a systemd service file (Linux):
```bash
sudo nano /etc/systemd/system/myapp.service
```
- Example service configuration:
```ini
[Unit]
Description=My Spring Boot Application
After=network.target

[Service]
User=ubuntu
ExecStart=/usr/bin/java -jar /home/ubuntu/myapp.jar
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```
- Enable and start the service:
```bash
sudo systemctl enable myapp
sudo systemctl start myapp
```

## 7. Monitoring and Logging

### 1. Set Up Logging:
Configure logging in `application.properties`:
```properties
logging.file.name=app.log
logging.level.root=INFO
```

### 2. Monitoring with Actuator:
Add Spring Boot Actuator to your project to expose health metrics and other monitoring endpoints:
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
Access metrics at `/actuator/metrics`.

## 8. Security Considerations

### 1. SSL/TLS Configuration:
Ensure your application is secured with HTTPS by configuring SSL:
```properties
server.ssl.key-store=classpath:keystore.jks
server.ssl.key-store-password=changeit
server.ssl.key-password=changeit
```

### 2. Environment Variables for Secrets:
Avoid hardcoding sensitive data in your application; use environment variables or services like AWS Secrets Manager or Azure Key Vault.

## Conclusion

This tutorial has covered the essential steps for configuring and deploying a Spring Boot application. Depending on your needs, you may adjust the configuration for specific environments, add more security layers, or integrate with CI/CD pipelines for automated deployment.
