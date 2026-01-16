# Maven Notes

**Date:** 15 January 2026, 22:13

---

## Introduction to Maven

Maven is a build automation tool primarily used for Java projects. It addresses two main aspects of building software: dependency management and project build lifecycle management.

It simplifies the build process by managing dependencies, compiling source code, packaging it into a deliverable (such as a JAR file), and deploying it to a repository.

Maven is based on the concept of a Project Object Model (POM), (pom.xml) which is a central piece of information that manages a project's build, reporting, and documentation.

---

## Project Object Model (POM)

The POM (Project Object Model) is the core of a Maven project. Understanding the POM file is essential because it contains all the configuration details for a Maven project.

### Structure of POM.xml:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>My App</name>
    <url>http://maven.apache.org</url>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

---

## Key Elements in POM

### modelVersion:
This specifies the version of the POM model being used. Currently, 4.0.0 is the default and most widely used version.

### groupId:
The groupId uniquely identifies your project across all projects. Typically, it follows the reverse domain name convention (e.g., com.example).

### artifactId:
The artifactId is the name of the jar without the version. It is the name of the project.

### version:
The version is the project's current version. Maven uses this version to distinguish between different versions of the same project.

Versions can be specific like 1.0.0, or they can include SNAPSHOT, which indicates a version in development.

### packaging:
The type of artifact this project produces. Common packaging types are jar, war, pom, etc.

If omitted, jar is assumed by default.

### name and url:
These are optional elements used to provide a human-readable name and project URL.

### dependencies:
The dependencies section lists all the external libraries your project depends on. Maven will download these libraries automatically from the central repository or other specified repositories.

### build:
The build section is used to specify how the project should be built. This includes specifying plugins, custom build directories, resources, etc.

---

## Understanding GroupId and ArtifactId

Suppose a repository has P1, P2..n number of projects and we want to add our project in this repo and that project has multiple projects inside it as well (m1, m2, m3).

So to identify your group you give **groupid:** `com.substring.tech`

**Artifact id** is to identify the artifacts. It is the name of a build artifact (a library, project name, or application) within a group. (myapp, myapp2, myapp3)

### Why It Exists

A single organization (groupId) often produces many artifacts.

**Example:**
```
org.springframework
 ├── spring-core
 ├── spring-context
 ├── spring-web
 └── spring-data-jpa
```
All share the same groupId but have different artifactIds.

---

## What is an Artifact?

An artifact is the output file produced by a build and stored in a repository so it can be used by other projects.

### Common Types of Artifacts

Artifacts are usually files such as:
- **JAR** → Java library
- **WAR** → Web application
- **EAR** → Enterprise application
- **ZIP / TAR** → Distribution bundles
- **POM** → Metadata describing the artifact

---

## Build World vs Real World

| Build World | Real World |
|-------------|------------|
| Source code | Blueprint |
| Artifact | Finished product |
| Repository | Warehouse |
| Dependency | Order request |

---

## Dependencies and Dependency Management

### Dependencies:

Dependencies are external libraries that your project needs to work. You specify these in the dependencies section of the POM.

Maven automatically downloads the specified versions of dependencies and any transitive dependencies they require.

### Example:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>2.5.4</version>
    </dependency>
</dependencies>
```

### Transitive Dependencies:

Maven resolves transitive dependencies automatically. For example, if your project depends on A, and A depends on B, Maven will include B as a dependency in your project.

---

## Understanding the .m2 Directory and settings.xml

### The .m2 Directory:

Located in the user's home directory (e.g., `C:\Users\YourName\.m2` on Windows or `/home/YourName/.m2` on macOS/Linux).

Contains the repository folder where Maven stores downloaded dependencies. This is known as the local repository.

You can delete this folder if you want to force Maven to re-download dependencies, but usually, Maven manages it automatically.

### settings.xml File:

Located in the .m2 directory. This file allows you to customize the behavior of Maven globally across all projects.

Common configurations include:
- Mirror configuration to point Maven to a specific repository.
- Proxy settings if you're behind a firewall or using a corporate network.
- Local repository location customization.

---

## Dependency Management

**Date:** 16 January 2026, 12:27

### 1. Dependency Coordinates (How Maven Identifies Artifacts)

Every dependency is uniquely identified by:

```
groupId:artifactId:version[:classifier][:type]
```

Basically this group id, artifact id and version tell Maven exactly what artifact to look for.

**Example:**
```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-core</artifactId>
  <version>6.1.2</version>
</dependency>
```

### 2. Repository Resolution Order (Where Maven Looks)

When resolving a dependency, Maven searches repositories in this order:

#### 1. Local Repository
- Located at: `~/.m2/repository`
- If the artifact is already present, Maven uses it immediately.

#### 2. Remote Repositories

A remote repository is a server that hosts:
- Project dependencies (JARs, POMs, etc.)
- Plugins
- Metadata (versions, checksums)

**How Maven uses remote repositories:**
1. Checks local repository
2. If not found, checks remote repositories in order
3. Downloads artifact
4. Caches/saves it locally

#### Central Repository (default)

Maven Central Repository (often just called Maven Central) - [https://mvnrepository.com/repos/central](https://mvnrepository.com/repos/central)

It's a central online library where developers publish and download Java dependencies (JARs, POMs, etc.).

Maven Central hosts:
- Java libraries (JARs)
- Kotlin & Scala libraries
- Android libraries
- Build plugins
- Metadata (POM files, checksums, signatures)

Each artifact is identified by:
- groupId
- artifactId
- version

Maven downloads artifacts from remote repositories and stores them locally in:
```
~/.m2/repository
```

### 3. Transitive Dependency Resolution

(see page 1)

### 4. Dependency Scopes

Maven provides several scopes for dependencies, each determining at what phase of the build process the dependency is available and whether it is included in the final artifact (e.g., a JAR or WAR file). Understanding these scopes is crucial for managing project dependencies effectively.

#### a. compile Scope

- **Default Scope:** If no scope is specified, Maven uses compile by default.
- **Availability:** The dependency is available in all phases of the build lifecycle, including compilation, testing, and packaging.
- **Inclusion in Final Artifact:** The dependency is included in the final artifact.
- **Typical Use:** Used for dependencies required to build, test, and run the project.

#### b. provided Scope

- **Availability:** The dependency is available at compile-time but is not included in the final artifact.

#### c. runtime Scope

- **Availability:** The dependency is not required for compilation but is needed during runtime, including execution and tests.
- **Inclusion in Final Artifact:** The dependency is included in the final artifact.
- **Typical Use:** Used for dependencies that are needed only at runtime, like JDBC drivers.

**Example:**
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.26</version>
    <scope>runtime</scope>
</dependency>
```

In this example, the MySQL JDBC driver is not required for compiling the code but is needed at runtime when the application connects to a MySQL database.

#### d. test Scope

- **Availability:** The dependency is available only for the test compilation and execution phases.
- **Inclusion in Final Artifact:** Not included in the final artifact.
- **Typical Use:** Used for libraries that are required only for testing, such as JUnit or Mockito.

#### e. system Scope

- **Availability:** The dependency is available for compilation and testing but must be explicitly provided by the developer on the system.
- **Inclusion in Final Artifact:** Not included in the final artifact.

---

## Maven Dependency Exclusion

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-web</artifactId>
  <exclusions>
    <exclusion>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

**Short explanation:**
- spring-web pulls commons-logging transitively
- `<exclusions>` tells Maven not to include that transitive dependency
- Used to avoid conflicts, reduce classpath, or replace with another library

---

## Maven Build Lifecycle

**Date:** 16 January 2026, 12:59

A sequence of phases that define the order in which tasks are executed during the build process of a Maven project. Maven has three built-in build lifecycles:

### 1. Default Lifecycle (Build)

This is the main lifecycle that handles project deployment. It consists of 23 phases, the most important of which are:

- **validate:** Validates the project is correct and all necessary information is available.
- **compile:** Compiles the source code of the project.
- **test:** Runs the tests using a suitable unit testing framework (like JUnit).
- **package:** Packages the compiled code into a distributable format, such as a JAR or WAR file.
- **verify:** Runs any checks to verify the package is valid and meets quality criteria.
- **install:** Installs the package into the local repository (m2), for use as a dependency in other projects locally.
- **deploy:** Copies the final package to the remote repository for sharing with other developers and projects.

### 2. Clean Lifecycle

This lifecycle is used to clean up artifacts created by the previous build. It consists of three phases:

- **pre-clean:** Executes processes needed before the actual project cleaning.
- **clean:** Removes files generated during the previous build.
- **post-clean:** Executes processes needed after the project cleaning.

When you run `mvn clean`, Maven executes all phases of the Clean lifecycle.

### 3. Site Lifecycle

This lifecycle is used to create the project's site documentation. It consists of four phases:

- **pre-site:** Executes processes needed before the actual project site generation.
- **site:** Generates the project's site documentation.
- **post-site:** Executes processes needed after the site generation, preparing for site deployment.
- **site-deploy:** Deploys the generated site to a web server.

When you run `mvn site`, Maven executes all phases of the Site lifecycle to generate the project documentation.

---

## How to Use the Build Lifecycle

You can execute any of the phases using the Maven command:

```
mvn <phase>
```

When you run a Maven command, such as `mvn package`, Maven executes all the phases up to and including the package phase.

### Key Commands for the Default Lifecycle

- `mvn validate`: Validates the project.
- `mvn compile`: Compiles the source code of the project.
- `mvn test`: Runs the tests using a suitable unit testing framework.
- `mvn package`: Takes the compiled code and packages it into a JAR, WAR, etc.
- `mvn install`: Installs the package into the local repository, making it available for other projects locally.
- `mvn deploy`: Copies the final package to the remote repository.
- `mvn clean install` / `mvn clean package`: First clean then install/package.
- `mvn clean deploy`

---

## Maven Repositories

**Date:** 16 January 2026, 15:43

Maven repositories are a central part of the Maven build system. They are where Maven stores and retrieves project dependencies, plugins, and other artifacts. Understanding how repositories work and how to manage them is crucial for effectively using Maven.

### How Maven Resolves Dependencies

Maven follows a specific order to resolve dependencies:

1. **Local Repository:** Maven checks the local repository first. If the dependency is found, Maven uses it.
2. **Remote Repositories:** If the dependency is not found in the local repository, Maven checks any configured remote repositories.
3. **Central Repository:** If the dependency is not found in any remote repositories, Maven checks the central repository.
4. **Fail:** If Maven cannot find the dependency in any repository, the build fails with an error.

### Remote Repository:

A remote repository is any repository that is hosted on a web server other than the central repository. Organizations often maintain their own remote repositories to store proprietary artifacts, plugins, or third-party libraries that are not available in the central repository.

You can configure Maven to use a remote repository by adding it to your pom.xml or settings.xml file.

**Example:**

```xml
<repositories>
    <repository>
        <id>my-company-repo</id>
        <url>http://repository.mycompany.com/maven2</url>
    </repository>
</repositories>
```

---

## Maven Plugins

**Date:** 16 January 2026, 15:57

### What is a Maven Plugin?

A Maven Plugin is a collection of goals that perform specific tasks in a Maven build lifecycle.

Maven itself does very little. Almost everything Maven does is done by plugins.

**Examples of tasks done by plugins:**
- Compile source code
- Run tests
- Package JAR/WAR
- Deploy artifacts
- Generate reports
- Clean build directories

**Without plugins, Maven cannot build projects.**

### 2. Plugin vs Goal vs Lifecycle (VERY IMPORTANT)

#### 2.1 Plugin
A plugin is a container for goals.

**Example:** `maven-compiler-plugin`

#### 2.2 Goal
A goal is a single task.

**Example:**
- `compiler:compile`
- `compiler:testCompile`

#### 2.3 Lifecycle
A lifecycle is a sequence of phases. Each phase is bound to plugin goals.

```
Lifecycle → Phase → Plugin → Goal
```

### 5. Types of Maven Plugins

#### 5.1 Core Plugins (Built-in)
Provided by Maven itself.

**Examples:**
- maven-compiler-plugin
- maven-surefire-plugin
- maven-jar-plugin
- maven-clean-plugin
- maven-install-plugin
- maven-deploy-plugin

#### 5.2 Third-Party Plugins
Provided by external libraries.

**Examples:**
- spring-boot-maven-plugin

---

## Commonly Used Maven Plugins (MUST KNOW FOR INTERVIEWS)

### 6.1 Maven Compiler Plugin

Compiles Java source code.

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.11.0</version>
  <configuration>
    <source>17</source>
    <target>17</target>
  </configuration>
</plugin>
```

### 6.2 Maven Surefire Plugin

Runs unit tests.

Executes tests named:
- `*Test.java`
- `*Tests.java`
- `*TestCase.java`

### 6.3 Maven Failsafe Plugin

Used for integration tests.

Runs tests like: `*IT.java`

**Phases:**
- integration-test
- verify

> **Note:** Surefire = unit tests | Failsafe = integration tests

### 6.4 Maven Clean Plugin

Deletes target/ directory.

**Command:** `mvn clean`

**Goal:** `clean:clean`

### 6.5 Maven Jar Plugin

Creates JAR files.

```xml
<plugin>
  <artifactId>maven-jar-plugin</artifactId>
</plugin>
```

### 6.8 Spring Boot Maven Plugin

Used for Spring Boot applications.

**Features:**
- Create executable JAR
- Run app using `mvn spring-boot:run`
- Repackage dependencies

```xml
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

---

## How Plugins Work with Lifecycle Phases

Each lifecycle phase is mapped to default plugin goals.

**Example:**

When you run:
```
mvn package
```

Maven automatically runs:
- validate
- compile → `compiler:compile`
- test → `surefire:test`
- package → `jar:jar` or `war:war`

> **Note:** You don't explicitly call plugins most of the time. Maven does it for you.

### 7. Plugin Configuration in pom.xml

Plugins are configured inside:

```xml
<build>
  <plugins>
    <plugin>...</plugin>
  </plugins>
</build>
```

**Example with execution:**

```xml
<plugin>
  <artifactId>maven-compiler-plugin</artifactId>
  <executions>
    <execution>
      <phase>compile</phase>
      <goals>
        <goal>compile</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

### 8. Plugin Executions (IMPORTANT)

Plugins can have multiple executions. Each execution can:
- Run in a different phase
- Use different configuration

```xml
<executions>
  <execution>
    <id>compile-java</id>
    <phase>compile</phase>
    <goals>
      <goal>compile</goal>
    </goals>
  </execution>
</executions>
```

### 10. How to Run Plugins Manually

You can run a plugin goal directly:

```
mvn compiler:compile
mvn clean:clean
mvn spring-boot:run
```

**Syntax:**
```
mvn <plugin-prefix>:<goal>
```

**Can we bind a plugin to a lifecycle phase?**

**Answer:** Yes, using `<executions>`.

---

## Maven Wrapper

**Date:** 16 January 2026

### What is the Maven Wrapper?

The Maven Wrapper lets a project use a specific Maven version without requiring Maven to be installed globally on a developer's machine or CI server.

Instead of running `mvn`, you run:
- **Linux/macOS:** `./mvnw`
- **Windows:** `mvnw.cmd`

The wrapper:
- Downloads the correct Maven version automatically
- Ensures everyone builds with the same Maven version
- Is standard for modern Java projects and CI pipelines

### Typical files it adds

When you enable it, these files appear in your project:

```
mvnw
mvnw.cmd
.mvn/
  └── wrapper/
      ├── maven-wrapper.jar
      └── maven-wrapper.properties
```

You commit these to Git.

### How to add Maven Wrapper to a project

If you already have Maven installed:

```
mvn -N wrapper:wrapper
```

Optional (recommended) – specify Maven version:

```
mvn -N wrapper:wrapper -Dmaven=3.9.6
```

### How to use it

Replace `mvn` with the wrapper command:

```
./mvnw clean install
./mvnw test
./mvnw spring-boot:run
```

**Windows:**
```
mvnw clean install
```

### Why it's important (especially for teams & CI)

- No "works on my machine" Maven version issues
- CI servers don't need Maven preinstalled
- Reproducible builds
- Recommended by Maven and Spring Boot

---

**End of Maven Notes**
