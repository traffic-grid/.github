# TrafficGrid

> Unified Traffic & Vehicle Management Platform for the City of Harare.

TrafficGrid consolidates vehicle records, traffic offences, parking fines, and payments across ZRP, ZINARA, VTS, City Parking, and City Council into a single digital platform. Officers log fines in the field via mobile. Citizens view their vehicle dashboard and pay fines online. Every action is fully audited.

---

## Tech Stack

- **Java 21**
- **Spring Boot** — REST API, Security, JPA, Validation
- **PostgreSQL** — primary database
- **Redis** — caching, session storage, distributed locks
- **Flyway** — database migrations
- **Docker & Docker Compose** — local infrastructure (PostgreSQL + Redis)
- **Swagger / OpenAPI** — auto-generated API documentation

---

## Prerequisites

Make sure you have the following installed before setting up the project:

| Tool | Version | Notes |
|---|---|---|
| Java JDK | 21 | [Download](https://adoptium.net/) |
| Maven | 3.9+ | Or use the included `./mvnw` wrapper |
| Docker | Latest | [Download](https://www.docker.com/products/docker-desktop) |
| Docker Compose | Latest | Included with Docker Desktop |
| Git | Latest | |

> PostgreSQL and Redis do **not** need to be installed locally — Docker Compose handles both.

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/aaron-dudue99/traffic-grid.git
cd traffic-grid
```

### 2. Start infrastructure (PostgreSQL + Redis)

```bash
docker compose up -d
```

This starts:
- **PostgreSQL** on port `5434`
- **Redis** on port `6379`

Verify containers are running:

```bash
docker compose ps
```

### 3. Configure the application

Copy the example environment config:

```bash
cp src/main/resources/application.example.yml src/main/resources/application-local.yml
```

Open `application-local.yml` and remove this line

```yaml
  profiles:
    active: local
```

> **Note:** Never commit `application-local.yml` to the repository. It is already listed in `.gitignore`.

### 4. Run database migrations

Flyway runs automatically on application startup — no manual step needed. It will create all tables and seed data in the correct order.

### 5. Build and run the application

Using the Maven wrapper (no local Maven install needed):

```bash
./mvnw spring-boot:run -Dspring-boot.run.profiles=local
```

Or with Maven installed locally:

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=local
```

Or using IntelliJ or any other IDE

```bash
 Use the  run configuration provided by the IDE
```

The application starts on **`http://localhost:8080`**.

---

## API Documentation

Once the application is running, Swagger UI is available at:

```
http://localhost:8080/swagger-ui/index.html
```

The raw OpenAPI spec (JSON) is at:

```
http://localhost:8080/v3/api-docs
```

---

## Project Structure

```
trafficgrid/
└── src/main/java/zw/co/trafficgrid/
    ├── TrafficGridApplication.java   ← entry point
    ├── auth/                         ← authentication, JWT, RBAC
    ├── vehicle/                      ← vehicle records and registration
    ├── fines/                        ← traffic offences and fine management
    ├── payments/                     ← EcoCash, bank transfer, cash recording
    ├── parking/                      ← parking tickets
    ├── notifications/                ← SMS, email, push notifications
    ├── audit/                        ← immutable action logging
    ├── admin/                        ← admin dashboard and reporting
    └── common/                       ← shared utilities, exceptions, DTOs
```

Each domain package follows the same internal structure:

```
{domain}/
├── controller/    ← REST endpoints
├── service/       ← business logic
├── repository/    ← database access (Spring Data JPA)
├── domain/        ← JPA entity classes
├── dto/           ← request and response objects
└── exception/     ← domain-specific exceptions
```

---

## Stopping the Application

Stop the Spring Boot app with `Ctrl + C`, then bring down the Docker containers:

```bash
docker compose down
```

To also remove the database volume (wipes all local data):

```bash
docker compose down -v
```

---

## Running Tests

```bash
./mvnw test
```

---

## Branch Strategy

| Branch | Purpose |
|---|---|
| `main` | Protected. Stable, production-ready codebase. No direct commits allowed. |
| `develop` | Primary development branch. All feature and fix branches start here and merge back here. |
| `deployments/staging` | Code deployed to the staging environment for testing before production. |
| `deployments/prod` | Code deployed to production containers. Only promoted from staging after verification. |

The general flow is:

```
feature/* or fix/*  →  develop  →  deployments/staging  →  deployments/prod  →  main
```

---

## Contributing

1. Branch off `develop` using the format `feature/your-feature-name` or `fix/your-fix-name`
2. Make your changes with clear, descriptive commits
3. Open a pull request against `develop` with a summary of what was changed and why
4. Do not commit secrets, credentials, or `application-local.yml`

---

## Roadmap

See the full system design document for planned features and future development backlog including ZIMRA integration, USSD support, partial payments, and national expansion.

---

*TrafficGrid · City of Harare · v0.1*
