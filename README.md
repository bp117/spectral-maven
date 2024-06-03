
# Sample API Project

This is a Java microservices project with a contract-first approach. The project uses an OpenAPI specification to define the API, Spectral for linting the specification, and the OpenAPI Generator for generating the server code.

## Prerequisites

- Java 17
- Maven 3.x
- Node.js and npm

## Project Structure

```
sample-api/
├── src/
│   ├── main/
│   │   ├── java/
│   │   ├── resources/
│   │   │   └── api-sample.yaml
│   ├── test/
├── lint-api.bat
├── spectral.yml
├── pom.xml
└── README.md
```

## Setup

### Step 1: Install Dependencies

1. Ensure you have Node.js and npm installed.
2. Install Spectral as a development dependency:

    ```bash
    npm install -D @stoplight/spectral-cli
    ```

### Step 2: Define the OpenAPI Specification

1. Create your OpenAPI specification file at `src/main/resources/openapi.yml`.

### Step 3: Create Spectral Ruleset

1. Create a `spectral.yml` file in the root directory with the following content:

    ```yaml
    extends: spectral:oas
    rules:
      operation-operationId-unique:
        description: "Every operation must have a unique `operationId`."
        severity: error
        given: "$.paths[*][*].operationId"
        then:
          function: falsy
    ```

### Step 4: Create Linting Script

1. Create a `lint-api.bat` file in the root directory with the following content:

    ```bat
    @echo off
    node_modules\.bin\spectral.cmd lint src\main\resources\api-sample.yaml --ruleset spectral.yml --verbose
    ```

### Step 5: Configure Maven

1. Ensure your `pom.xml` is configured correctly:

    ```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>com.example</groupId>
        <artifactId>sample-api</artifactId>
        <version>1.0.0-SNAPSHOT</version>

        <properties>
            <java.version>17</java.version>
            <spring.boot.version>3.0.0</spring.boot.version>
            <openapi.generator.maven.plugin.version>6.0.1</openapi.generator.maven.plugin.version>
            <exec.maven.plugin.version>3.0.0</exec.maven.plugin.version>
            <spring-boot-maven-plugin.version>3.0.0</spring-boot-maven-plugin.version>
        </properties>

        <dependencyManagement>
            <dependencies>
                <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-dependencies</artifactId>
                    <version>${spring.boot.version}</version>
                    <type>pom</type>
                    <scope>import</scope>
                </dependency>
            </dependencies>
        </dependencyManagement>

        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>
        </dependencies>

        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring-boot-maven-plugin.version}</version>
                </plugin>

                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>${exec.maven.plugin.version}</version>
                    <executions>
                        <execution>
                            <id>lint-openapi</id>
                            <phase>validate</phase>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                            <configuration>
                                <executable>cmd.exe</executable>
                                <arguments>
                                    <argument>/c</argument>
                                    <argument>${project.basedir}\lint-api.bat</argument>
                                </arguments>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>

                <plugin>
                    <groupId>org.openapitools</groupId>
                    <artifactId>openapi-generator-maven-plugin</artifactId>
                    <version>${openapi.generator.maven.plugin.version}</version>
                    <executions>
                        <execution>
                            <id>generate-sources</id>
                            <goals>
                                <goal>generate</goal>
                            </goals>
                            <configuration>
                                <inputSpec>${project.basedir}/src/main/resources/api-sample.yaml</inputSpec>
                                <generatorName>spring</generatorName>
                                <output>${project.build.directory}/generated-sources/openapi</output>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

## Running the Project

1. **Lint the OpenAPI Specification**:
    - Ensure the OpenAPI specification is correct and meets the defined ruleset.
    - Run the Maven build which includes the validation phase:
      ```bash
      mvn clean validate
      ```

2. **Generate Server Code**:
    - Run the Maven build which includes the code generation phase:
      ```bash
      mvn clean install
      ```

3. **Run the Application**:
    - Start the Spring Boot application:
      ```bash
      mvn spring-boot:run
      ```

## Additional Information

- **Spectral Documentation**: [Spectral GitHub](https://github.com/stoplightio/spectral)
- **OpenAPI Generator Documentation**: [OpenAPI Generator](https://openapi-generator.tech/)
- **Spring Boot Documentation**: [Spring Boot](https://spring.io/projects/spring-boot)

By following these steps, you will have a Java microservices project set up with a contract-first approach, including OpenAPI specification, Spectral for linting, and generated server code.
