# Creating a new microservice

Follow these steps exactly, so all services look the same.
Back to the [README](../README.md) · Workflow rules: [CONTRIBUTING.md](../CONTRIBUTING.md)

---

## 1. Create a branch

Create a task in Notion first, then:

```bash
git checkout main
git pull
git checkout -b chore/AH-<id>-<name>-service-scaffold
```

Example: `chore/AH-7-security-service-scaffold`

---

## 2. Generate the project in IntelliJ

**File → New → Project → Spring Boot**, then fill in:

| Field | Value |
|---|---|
| Name | `<name>-service` (e.g. `security-service`) |
| Location | `.../alert-hub/services` |
| Create Git repository | ❌ **unticked** (alert-hub is already a Git repo) |
| Language | Java |
| Type | **Maven** |
| Group | `com.mst.alerthub` |
| Artifact | `<name>-service` |
| Package name | `com.mst.alerthub.<name>`, with **no dashes** (e.g. `com.mst.alerthub.emailsender`). IntelliJ fills this in wrong by default, so change it by hand |
| JDK | **Eclipse Temurin 25** |
| Java | **25** (not "25 Preview") |
| Packaging | Jar |
| Configuration | Properties |

Click **Next**, choose **Spring Boot 4.1.2**, and pick the dependencies:

| Service | Dependencies |
|---|---|
| **Every service** | Spring Web, Lombok, Validation, Spring Boot DevTools, Spring Boot Actuator, Spring for Apache Kafka |
| + services with a database (loader, security, user, metric, action) | Spring Data JPA, PostgreSQL Driver |
| + logger | Spring Data MongoDB |
| + services that call others (processor, evaluation, user, action) | OpenFeign |

Click **Create**. If IntelliJ asks how to open it, choose **This Window**.

---

## 3. Add Swagger (not needed for logger)

Open `services/<name>-service/pom.xml` and add this inside `<dependencies>`:

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>3.2.0</version>
</dependency>
```

After the service runs, Swagger UI is at `http://localhost:<port>/swagger-ui.html`.

---

## 4. Register the service in the root `pom.xml`

In `alert-hub/pom.xml`, remove the comment around your service's line:

```xml
<!-- before -->
<!-- <module>services/security-service</module> -->

<!-- after -->
<module>services/security-service</module>
```

This lets IntelliJ load the service as a Maven module.
Only do this in the same PR that creates the folder: Maven fails if a listed folder doesn't exist.

---

## 5. Add the CI workflow

1. Copy `.github/workflows/loader-service.yml` to `.github/workflows/<name>-service.yml`
2. Replace **every** `loader` with your service name (name, `paths`, `working-directory`, `loader_db`)
3. If your service has **no database** (processor, evaluation, email-sender, sms-sender): delete the `services: postgres` block and the three `SPRING_DATASOURCE_…` lines

---

## 6. Set the basic configuration

In `services/<name>-service/src/main/resources/application.properties`:

```properties
spring.application.name=<name>-service
server.port=<port from the README ports table>
```

Services with a database also need the connection settings. Read the password from an environment variable, never write it in the file:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/<name>_db
spring.datasource.username=${DB_USER:postgres}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=update
```

---

## 7. Check, commit, open a pull request

- Delete the generated `HELP.md`
- In IntelliJ: **Maven** tool window → 🔄 **Reload All Maven Projects**; your service appears as a module
- Check that `git status` shows no `.idea/` or `target/` files

```bash
git add .
git commit -m "chore(<name>): scaffold <name>-service (AH-<id>)"
git push -u origin chore/AH-<id>-<name>-service-scaffold
```

On GitHub: **Compare & pull request** → title `chore(<name>): scaffold <name>-service (AH-<id>)` → a maintainer reviews and merges.
In Notion: set the task to *In Review* and paste the PR link.

> **Note:** for services with a database, `./mvnw verify` fails locally until PostgreSQL runs in Minikube, because the generated test connects to the database. The CI on GitHub provides its own temporary database, so the PR check still passes.

---

## Reviewer checklist

- [ ] Java 25 and Spring Boot 4.1.2 in `pom.xml`
- [ ] Package is `com.mst.alerthub.<name>`
- [ ] springdoc dependency present (except logger)
- [ ] Module line uncommented in the root `pom.xml`
- [ ] Workflow file added and all `loader` references replaced
- [ ] No `HELP.md`, `.idea/`, `target/` or passwords committed