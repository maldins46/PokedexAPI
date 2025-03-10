# Pokedex API

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/maldins46/PokedexApi/ci.yml?logo=github&label=CI)
![GitHub License](https://img.shields.io/github/license/maldins46/PokedexApi?logo=apache&label=License)
![GitHub top language](https://img.shields.io/github/languages/top/maldins46/PokedexApi)
![Coverage](.github/badges/jacoco.svg)

A REST API, built on Java Spring Boot, that provides Pokémon information as part of a software engineering challenge.

## Prerequisites - Installing Dependencies

### 1. Java 17
The application requires **Java 17** to be installed on your machine to build and run.

#### **Windows**
1. Download and install **Eclipse Temurin JDK 17** from [Adoptium](https://adoptium.net/temurin/releases/?version=17).
2. Add Java to your system PATH (if not automatically configured).
3. Verify installation:
```sh
java -version
```

#### **MacOS/Linux**
1. Install Java via Homebrew (MacOS/Linux):
```sh
brew install openjdk@17
```
2. Verify installation:
```sh
java -version
```

### 2. Maven

**Maven** is also a prerequisite. However, the project includes the Maven Wrapper (`mvnw`), so you **don't need to install Maven manually**.

### 3. Docker (optional)

Docker is only required if you choose to deploy the application in a containerized environment. If needed, follow these steps:

- Download and install Docker from [Docker Official Site](https://www.docker.com/get-started).
- Verify installation:
```sh
docker --version
```

## Running the application
### 1. Build the project
```sh
./mvnw clean install    # macOS/Linux
mvnw.cmd clean install  # Windows
```

### 2. Run the application
```sh
java -jar target/PokedexAPI-1.0.0.jar
```

## Running with Docker (optional)

### 1. Package the project

```sh
./mvnw clean package    # macOS/Linux
mvnw.cmd clean package  # Windows
```

### 2. Build the Docker image
```sh
docker build -t pokedex-api .
```

### 3. Run the container
```sh
docker run -p 8080:8080 pokedex-api
```

## Example API Calls
### Get Basic Pokémon Information
```sh
curl -X GET http://localhost:8080/pokemon/mewtwo
```
**Response:**
```json
{
  "name": "mewtwo",
  "description": "It was created by a scientist after years of horrific gene splicing and DNA engineering experiments.",
  "habitat": "rare",
  "isLegendary": true
}
```

### Get Translated Pokémon Description
```sh
curl -X GET http://localhost:8080/pokemon/translated/mewtwo
```
**Response:**
```json
{
  "name": "mewtwo",
  "description": "Created by a scientist after years of horrific gene splicing and DNA engineering experiments, it was.",
  "habitat": "rare",
  "isLegendary": true
}
```

## Design Decisions and Workflow
The two main architectural design principles followed in this project are:
- **API-first approach**: This technique, acquired through experience, helps prevent misunderstandings with API consumers and allows stakeholders to use a valid mock of the API even if development is not yet complete.
- **MCS (Model-Controller-Service) architecture**: This is similar to the MVC pattern but explicitly acknowledges the Service layer, which is a fundamental principle in Spring Boot applications.

With these principles in mind, the project was structured as follows:
1. **Initial Setup and APIs**: Defined the boilerplate structure, endpoints, and Data Transfer Object (DTO) formats.
2. **Service Layer Implementation**: Implemented logic and tests before fully integrating the data layer, following a **test-driven approach** where possible.
3. **Data Layer Implementation**: Used WebClient for API calls, ensuring resilience against connection failures and bad responses with **MockWebServer tests**.
4. **Layer Segregation**: The service layer was split into two parts:
    - A service to retrieve Pokémon data.
    - A service to handle translations, following the **Decorator design pattern** to improve modularity and testability.
5. **Error Handling**: Implemented standard HTTP error codes:
    - `502 Bad Gateway` for external API failures.
    - `404 Not Found` for missing Pokémon data.
    - `500 Internal Server Error` as a generic fallback.
6. **Final Integration and Refinements**: Integrated the components and addressed format inconsistencies (e.g., Pokémon descriptions containing carriage returns), adding unit tests to document these fixes.

## Production Considerations
If this system were to be deployed at a production level with potentially hundreds of daily users, several enhancements would be necessary:

- **Caching and Rate Limiting**: Since the external APIs have request limits, caching responses would reduce redundant API calls. We could also rate-limit outbound requests to avoid provider bans or migrate Pokémon data to an internal database for resilience.
- **Integration Testing**: Currently, only unit tests are implemented. Integration tests would ensure that multiple system components work well together.
- **Horizontal Scaling**: Running multiple instances of the application behind a **load balancer** (e.g., Kubernetes or an alternative orchestrator) would improve scalability and reliability.
- **Security**: If the API is publicly exposed, implementing authentication (e.g., API keys or OAuth) would prevent unauthorized access.
- **Comprehensive Documentation**: More detailed documentation, including **input/output formats** and a list of available Pokémon, would improve usability for API consumers.
- **Code Quality and Maintainability**: Introducing linting tools, **static code analysis** (e.g., SonarQube), and **test coverage enforcement** (e.g., JaCoCo) would ensure maintainability and enforce high-quality contributions through pull request quality gates.
