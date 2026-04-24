# Experiment 10: SonarQube — Static Code Analysis

## 1. Objective

To implement static code analysis using SonarQube and identify bugs, vulnerabilities, and code smells in a Java application.

---

## 2. Problem Statement

In software development, bugs and vulnerabilities are often detected late in the development cycle. Manual code reviews are slow and inconsistent. This creates a need for automated tools that can analyze code quality early.

SonarQube solves this problem by performing static code analysis and providing immediate feedback on code quality.

---

## 3. Theory

### 3.1 What is SonarQube

SonarQube is an open-source platform used to analyze source code for:

* Bugs
* Security vulnerabilities
* Code smells

It provides a dashboard to visualize issues and enforce quality gates.

---

### 3.2 Architecture

SonarQube consists of:

* **SonarQube Server**: Stores and displays results
* **Sonar Scanner**: Analyzes code and sends report

Flow:

```
Your Code → Sonar Scanner → SonarQube Server → Dashboard
```

---

### 3.3 Key Terms

* Quality Gate: Pass/fail condition for code
* Bug: Code that may fail
* Vulnerability: Security risk
* Code Smell: Poor coding practice
* Technical Debt: Time to fix issues

---

## 4. Prerequisites

* Docker installed
* Docker Compose installed
* Java (JDK 11+)
* Maven installed
![ss](./Screenshot%202026-04-24%20184529.png) 
---

## 5. Procedure

### Step 1: Start SonarQube Server

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:

  sonar-db:
    image: postgres:13
    container_name: sonar-db
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonarqube
      POSTGRES_HOST_AUTH_METHOD: trust
    volumes:
      - sonar-db-data:/var/lib/postgresql/data
    networks:
      - sonarqube-lab

  sonarqube:
    image: sonarqube:lts-community
    container_name: sonarqube
    ports:
      - "9000:9000"
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://sonar-db:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonar-data:/opt/sonarqube/data
      - sonar-extensions:/opt/sonarqube/extensions
    depends_on:
      - sonar-db
    networks:
      - sonarqube-lab

volumes:
  sonar-db-data:
  sonar-data:
  sonar-extensions:

networks:
  sonarqube-lab:
    driver: bridge
```
![ss](./Screenshot%202026-04-24%20185338.png)

Run commands:

```bash
docker-compose up -d
docker-compose logs -f sonarqube
```
![ss](./Screenshot%202026-04-24%20185443.png)
![ss](./Screenshot%202026-04-24%20185550.png)
![ss](./Screenshot%202026-04-24%20185601.png)
Open browser:

```
http://localhost:9000
```
![ss](./Screenshot%202026-04-24%20185634.png)
Login:

```
Username: admin
Password: admin
```

---
![ss](./Screenshot%202026-04-24%20185710.png)

### Step 2: Create Java Project

Commands used:

```bash
mkdir -p sample-java-app/src/main/java/com/example
cd sample-java-app
```
![ss](./Screenshot%202026-04-24%20190150.png)
Create `Calculator.java`:

```java
package com.example;

public class Calculator {

    public int divide(int a, int b) {
        return a / b;
    }

    public int add(int a, int b) {
        int result = a + b;
        int unused = 100;
        return result;
    }

    public String getUser(String userId) {
        String query = "SELECT * FROM users WHERE id = " + userId;
        return query;
    }

    public int multiply(int a, int b) {
        int result = 0;
        for (int i = 0; i < b; i++) {
            result = result + a;
        }
        return result;
    }

    public int multiplyAlt(int a, int b) {
        int result = 0;
        for (int i = 0; i < b; i++) {
            result = result + a;
        }
        return result;
    }

    public String getName(String name) {
        return name.toUpperCase();
    }

    public void riskyOperation() {
        try {
            int x = 10 / 0;
        } catch (Exception e) {
        }
    }
}
```

---
![ss](./Screenshot%202026-04-24%20190126.png)

### Step 3: Create pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://www.maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>sample-app</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <sonar.projectKey>sample-java-app</sonar.projectKey>
        <sonar.host.url>http://localhost:9000</sonar.host.url>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.sonarsource.scanner.maven</groupId>
                <artifactId>sonar-maven-plugin</artifactId>
                <version>3.9.1.2184</version>
            </plugin>
        </plugins>
    </build>

</project>
```
---

![ss](./Screenshot%202026-04-24%20190901.png)

### Step 4: Generate Token

Steps:

1. Open SonarQube dashboard
2. Go to My Account → Security
3. Generate token
4. Copy token

---
![ss](./Screenshot%202026-04-24%20190404.png)
### Step 5: Run Analysis

Commands used:

```bash
mvn clean install
mvn sonar:sonar -Dsonar.login=YOUR_TOKEN
```

---
![ss](./Screenshot%202026-04-24%20190842.png)
![ss](./Screenshot%202026-04-24%20190851.png)

### Step 6: View Results

Open:

```
http://localhost:9000/dashboard?id=sample-java-app
```

Dashboard shows:

* Bugs
* Vulnerabilities
* Code smells
* Quality gate status

---

## 6. Error Faced and Solution

### Error:

```
Malformed POM: Expected root element 'project' but found 'sonar.login'
modelVersion, groupId, artifactId, version missing
```

### Cause:

The `pom.xml` file was incorrectly written. It started with `<sonar.login>` instead of `<project>`, which broke Maven structure.

### Solution:

* Rewrote the `pom.xml` correctly
* Ensured it starts with `<project>`
* Removed token from `pom.xml`
* Passed token via command line

Correct command used:

```bash
mvn sonar:sonar -Dsonar.login=YOUR_TOKEN
```

---

## 7. Result

The SonarQube analysis was successfully performed. The dashboard displayed detected bugs, vulnerabilities, and code smells. The Quality Gate status was evaluated based on the analysis.

---

## 8. Conclusion

SonarQube helps in improving code quality by detecting issues early in development. It automates code review and ensures better maintainability, security, and reliability of software systems.

---
