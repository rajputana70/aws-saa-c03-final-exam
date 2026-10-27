# AWS SAA-C03 Practice Assessment (Java + Spring Boot + MySQL)

A professional, AWS-inspired browser-based practice test with 50 questions, a 100-minute timer, single-answer and multiple-response questions, question navigation, Mark for Review, auto-saving answers, automatic time-expiry submission, results, domain analysis, and explanations.

> Independent study project. It is not affiliated with AWS and does not reproduce confidential exam questions. The included bank contains original practice scenarios.

## Requirements
- Java 21+
- Maven 3.9+
- MySQL 8+

## 1. Create a MySQL database and user

Log in to MySQL as an administrator and run:

```sql
CREATE DATABASE aws_saa_practice CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'saa_user'@'localhost' IDENTIFIED BY 'Choose-A-Strong-Password';
GRANT ALL PRIVILEGES ON aws_saa_practice.* TO 'saa_user'@'localhost';
FLUSH PRIVILEGES;
```

## 2. Configure the database connection

From the project folder, set environment variables before starting the app.

Linux/macOS:
```bash
export DB_URL='jdbc:mysql://localhost:3306/aws_saa_practice?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC'
export DB_USERNAME='saa_user'
export DB_PASSWORD='Choose-A-Strong-Password'
```

Windows PowerShell:
```powershell
$env:DB_URL="jdbc:mysql://localhost:3306/aws_saa_practice?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC"
$env:DB_USERNAME="saa_user"
$env:DB_PASSWORD="Choose-A-Strong-Password"
```

For a quick local-only trial, `application.properties` defaults to user `root` and password `change-me`; change those values or use environment variables. Do not commit real passwords to source control.

## 3. Run the application

In the project directory:
```bash
mvn spring-boot:run
```

Open `http://localhost:8080`.

## 4. Question bank

The application needs at least 50 rows in the `questions` table to start a 50-question exam. The project includes a data seeder that inserts 50 original practice questions on first startup when the table is empty. Hibernate creates the tables automatically in development mode.

## 5. Build a runnable JAR

```bash
mvn clean package
java -jar target/aws-saa-practice-1.0.0.jar
```

## Project structure

```text
aws-saa-practice/
├── pom.xml
├── README.md
└── src/main/
    ├── java/com/trainingbasket/awssaa/
    │   ├── AwsSaaPracticeApplication.java
    │   ├── model/
    │   │   ├── Question.java
    │   │   ├── ExamSession.java
    │   │   └── ExamQuestion.java
    │   ├── repo/
    │   │   ├── QuestionRepository.java
    │   │   ├── ExamSessionRepository.java
    │   │   └── ExamQuestionRepository.java
    │   └── web/
    │       ├── HomeController.java
    │       └── ExamController.java
    └── resources/
        ├── application.properties
        ├── templates/index.html
        └── static/
            ├── styles.css
            └── app.js
```

## API overview
- `POST /api/exam/start` — create an exam session
- `GET /api/exam/{id}` — retrieve exam questions and saved selections (never returns answer keys)
- `PUT /api/exam/{id}/answer/{examQuestionId}` — save answer and review flag
- `POST /api/exam/{id}/submit` — submit and calculate result
- `GET /api/exam/{id}/results` — retrieve result after submission

## Scoring
- Exact-match scoring is used for multiple-response questions: all and only correct options must be selected.
- This is a practice score and percentage, not an official AWS scaled score.
- A candidate should not use this application as an official pass/fail prediction.

## Production hardening before deployment
For public use, add authentication/authorization, CSRF protection appropriate to the chosen API design, rate limits, database migrations, HTTPS, backups, audit logging, and admin-only question management. The demo intentionally does not include login or an admin UI.
