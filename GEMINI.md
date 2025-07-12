# Gemini Code Assistant Report

## Project Overview

This project is a RESTful API for the LuckPerms permissions plugin. It is implemented as a LuckPerms extension and built using Java and the Javalin web framework. The primary purpose of this API is to provide a programmatic HTTP interface for interacting with the LuckPerms core, allowing external systems to manage users, groups, tracks, and permissions.

The API is designed to be run as a standalone application within a Docker container, connecting to the same database as the main LuckPerms instance(s).

## Key Technologies

- **Language:** Java (Version 21)
- **Web Framework:** Javalin
- **Build Tool:** Gradle
- **API Specification:** OpenAPI 3.0 (Swagger)
- **Containerization:** Docker & Docker Compose
- **CI/CD:** GitHub Actions

## Project Structure

The project's source code is located in the `src/main/java/me/lucko/luckperms/extension/rest/` directory and is organized as follows:

-   **`RestExtension.java`**: The main class and entry point for the LuckPerms extension. It initializes the `RestServer`.
-   **`RestServer.java`**: The core of the application. It sets up the Javalin web server, configures routes, authentication, error handling, and the OpenAPI (Swagger) documentation.
-   **`controller/`**: Contains the controller classes that handle the logic for each API endpoint. There are dedicated controllers for Users, Groups, Tracks, Actions, etc. These controllers use the LuckPerms API to perform their operations.
-   **`bind/`**: Contains custom Jackson JSON serializers and deserializers. These are essential for converting complex LuckPerms objects (like `Node`, `ContextSet`, `User`) into the JSON format defined by the API specification, and vice-versa.
-   **`model/`**: Defines the Data Transfer Objects (DTOs) used for request and response bodies in the API, such as `PermissionCheckRequest` or `UserSearchResult`.
-   **`util/`**: Utility classes, including the Swagger UI setup and parameter parsing helpers.
-   **`resources/`**:
    -   **`luckperms-openapi.yml`**: The single source of truth for the API's design. It defines all available endpoints, their parameters, request/response schemas, and status codes. This is the most critical file for understanding the API's capabilities.
    -   **`extension.json`**: The descriptor file that LuckPerms uses to identify and load the extension.

## API Endpoints

The API provides comprehensive CRUD (Create, Read, Update, Delete) operations for the main LuckPerms data models. The primary resources are:

-   **/user**: Manage individual users.
-   **/group**: Manage permission groups.
-   **/track**: Manage promotion/demotion tracks.
-   **/action**: Query and submit entries to the action log.
-   **/messaging**: Interact with the LuckPerms messaging service.
-   **/event**: Subscribe to LuckPerms events via Server-Sent Events (SSE).
-   **/health**: Check the health of the API and its connection to the database.

For a complete and detailed list of all endpoints and their usage, refer to the **`src/main/resources/luckperms-openapi.yml`** file.

## Configuration

The application is configured via environment variables or Java system properties, as defined in `RestConfig.java`. Key configuration options include:

-   `LUCKPERMS_REST_HTTP_PORT`: The port for the HTTP server (default: `8080`).
-   `LUCKPERMS_REST_AUTH`: Enables or disables API key authentication (default: `false`).
-   `LUCKPERMS_REST_AUTH_KEYS`: A comma-separated list of valid API keys if auth is enabled.
-   `LUCKPERMS_DATA_*`: Standard LuckPerms environment variables for database configuration.

## How to Build and Run

### Build

The project is built using the Gradle wrapper:

```bash
./gradlew build
```

This command compiles the code and creates a shadow JAR file in `build/libs/`.

### Running with Docker (Recommended)

The `README.md` and `docker/docker-compose.yml` files provide instructions for running the API in a Docker container. This is the recommended approach for production.

1.  Configure the `docker-compose.yml` file with your database credentials.
2.  Run `docker compose up -d`.

### Running Manually

1.  Build the project using `./gradlew build`.
2.  Place the generated `luckperms-rest-api-v1.jar` into the `/plugins/LuckPerms/extensions/` directory of your server.
3.  Restart the server.

## CI/CD

The project uses a GitHub Actions workflow defined in `.github/workflows/ci.yml`. This workflow automatically:
1.  Builds the Gradle project on every push to `main`.
2.  Builds a multi-platform Docker image.
3.  Pushes the resulting image to the GitHub Container Registry (GHCR).
