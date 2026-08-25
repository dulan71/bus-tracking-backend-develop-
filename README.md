# BusTrackPro

BusTrackPro is the backend service for the BusTrackPro system — a Spring Boot application that provides REST APIs and background services for bus, route, schedule and notification management used by the BusTrackPro frontend and device/device-telemetry integrations.

Main purpose
- Serve as the central backend for managing buses, routes, timetables, trips and notifications.
- Persist domain data to a PostgreSQL database.
- Integrate with device telemetry / Azure IoT for vehicle/device data ingestion.
- Provide authentication and authorization for users and an admin seed account (JWT-based configuration is present).

Technologies used
- Java (version: not detectable from repository; see note below)
- Spring Boot (version: not detectable from repository; see note below)
- Spring Data JPA / Hibernate
- PostgreSQL (jdbc:postgresql)
- Maven (build tool)
- Azure IoT Hub / Event Hub (configuration keys referenced in application.yml)

Identified from code
- Main class: com.bustrackpro.BusTrackProApplication (sets servlet context path to /api)
- Persistence layer: several Spring Data JPA repositories (BusRepository, TimetableRepository, NotificationRepository, etc.)
- Configuration: application.yml at project root (contains datasource, JWT and Azure settings)

Java / Spring Boot version
- I could not find a pom.xml in the repository root from the files I inspected, so the exact Java and Spring Boot versions are not visible. Check pom.xml or mvnw/maven-wrapper.properties in the repo; common choices are Java 11 or 17 and Spring Boot 2.7.x / 3.x depending on your pom.

Main backend modules / features (based only on existing code)
- Bus management: buses, drivers, device references (BusRepository, BusDriver)
- Route & Halts: BusRoute, BusHalt, BusRouteHalt
- Scheduling: Timetable, ScheduleAssignment, Trip, Schedule repositories and related code
- Notifications: Notification and NotificationRecipient repositories
- Authentication/Authorization: JWT settings referenced (app.jwtSecret, jwtExpirationInMs)
- Device integration: Azure IoT / Event Hub configuration referenced in application.yml

Project structure (expected Maven layout)

If this is a standard Maven project the project tree should look like:

BusTrackPro/
  ├─ src/
  │  ├─ main/
  │  │  ├─ java/        (application code, packages under com.bustrackpro)
  │  │  └─ resources/   (application.yml, other resources)
  │  └─ test/
  ├─ .gitignore
  ├─ README.md
  ├─ pom.xml
  └─ ...

Note: I observed many Java files at the repository root in the current copy — verify that your source files are under src/main/java. Do not change or move code as part of this preparation step; this README assumes a standard Maven layout.

How to clone the repository

git clone https://github.com/dulan71/bus-tracking-backend-develop-.git
cd bus-tracking-backend-develop-

How to configure the database
- The project uses PostgreSQL (application.yml references a postgres JDBC URL). Do not commit real credentials.
- Create a local database and user or point the datasource at your test DB.

Recommended options
1) Use a local application.yml (NOT committed) with your secrets. Example:

  spring:
    datasource:
      url: jdbc:postgresql://localhost:5432/bustrackpro
      username: your_db_user
      password: your_db_password

2) Or export environment variables in CI or your shell (SPRING_DATASOURCE_URL, SPRING_DATASOURCE_USERNAME, SPRING_DATASOURCE_PASSWORD).

I have added application-example.yml (see below) you can copy to application.yml and fill in values.

How to run the Spring Boot backend

If this repo has the Maven wrapper (mvnw) use it; otherwise use your system mvn.

# With Maven wrapper
./mvnw spring-boot:run

# With installed Maven
mvn spring-boot:run

Or build an executable jar:

mvn -DskipTests package
java -jar target/*.jar

How to run tests

mvn test

API information
- I did not find obvious controller classes annotated with @RestController in the top-level files I inspected; however the application main class is present and repositories are present, so REST controllers are likely in the codebase under com.bustrackpro.controller or similar.
- Once the app is running the servlet context path is set to /api (BusTrackProApplication sets server.servlet.context-path to /api). Typical endpoints will be reachable at http://localhost:8080/api/...

Important configuration / environment information
- application.yml in the repository currently contains credentials and secrets (database username/password, jwtSecret, admin password, Azure connection string). DO NOT commit secrets to source control. Treat the checked-in application.yml as sensitive.
- I added application-example.yml as a template with placeholders. Developers should copy it to application.yml locally and fill values.
- For CI / production, use environment variables or secret management (GitHub Actions secrets, HashiCorp Vault, Azure Key Vault, etc.)

Local setup (example)
1. Copy the example config and update values:

cp application-example.yml application.yml
# edit application.yml and set datasource, jwtSecret, admin credentials, and azure keys

2. Start Postgres locally or point to a dev DB.
3. Run the app: ./mvnw spring-boot:run

Secrets & files to NOT commit
- application.yml (contains secrets)
- any local keystore or .env files

If you want, I can:
- create application-example.yml with placeholders (I already added it to the repo)
- create or refine .gitignore (already added)

If you'd like me to also scan for controllers to document endpoints, say so and I will search for @RestController or @RequestMapping annotations and list detected endpoints.
