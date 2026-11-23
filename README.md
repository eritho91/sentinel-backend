TESTÄNDRING

# Sentinel Backend

Backend for Sentinel, an incident management system built with Java and Spring Boot.

## Requirements

- Java 21
- Git

Maven is included through Maven Wrapper, so a separate Maven installation is not required.

## Clone the repository

```bash
git clone https://github.com/eritho91/sentinel-backend.git
cd sentinel-backend
```

## Build the project

Run the following command to compile the project and run its tests:

```bash
./mvnw clean package
```

On Windows, use:

```bash
mvnw.cmd clean package
```

## Run the application

Start the Spring Boot application:

```bash
./mvnw spring-boot:run
```

The application starts at:

http://localhost:8080

## Database

H2 is currently used as the development database.

The database runs in memory and is recreated when the application restarts.

The project also includes the PostgreSQL driver for future database configuration.

## API documentation

Swagger UI is available at:

http://localhost:8080/swagger-ui.html

## Technology stack

- Java 21
- Spring Boot
- Spring WebMVC
- Spring Data JPA
- Hibernate
- H2 Database
- PostgreSQL
- Maven
- SpringDoc OpenAPI

## Project status

The initial backend structure is in place. Development of the incident management functionality is ongoing.
