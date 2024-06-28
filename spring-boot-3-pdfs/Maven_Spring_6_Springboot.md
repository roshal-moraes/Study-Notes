# **Section 1: Maven**

# Chapter 1: Introduction to Maven and Project Management

## 1.1 Overview of Project Building
- When building a project, it involves more than just coding and syntax.
- Maven is introduced as a project management tool to handle various tasks during the project development lifecycle.

## 1.2 Why Use Maven?
- Building a project involves multiple steps: compiling, running, testing, packaging, and deploying.
- While IDEs can handle these tasks, there are challenges with external libraries and dependencies.
- External libraries, like connectors for databases or frameworks like Hibernate, require specific JAR files.
- Managing dependencies manually can lead to version conflicts and additional work.
- Collaboration with colleagues may result in version mismatches, causing potential issues.
- Maven is introduced as a solution to address these challenges.

## 1.3 External Libraries and Dependencies
- External libraries, represented as JAR files in Java, are required for various functionalities.
- Example: Connecting Java code to a database requires a specific connector JAR file (e.g., MySQL connector for MySQL, Postgres connector for Postgres).
- Introduction to Hibernate, a framework that allows saving data in a database without writing SQL queries.
- Dependencies in Maven include the main JAR file and transitive dependencies.

## 1.4 Challenges Without Maven
- Manual downloading and managing of JAR files for dependencies.
- Version mismatches between libraries, requiring careful synchronization.
- Upgrading dependencies or making changes necessitates re-downloading JAR files.
- Collaborative challenges when colleagues need to set up the project with the same dependencies.

## 1.5 Introduction to Maven
- Maven is a project management tool that simplifies project building and dependency management.
- Maven is one of several tools available (others include Gradle and Ivy).
- Choice of Maven due to its beginner-friendly nature.

## 1.6 Maven vs. Other Tools
- Multiple project management tools available, including Maven, Gradle, and Ivy.
- Maven chosen for its beginner-friendly nature, especially for those new to Java project development.

## 1.7 Maven Installation
- Maven can be used as a local tool or integrated with IDEs.
- Installing Maven locally involves downloading from the official website (maven.apache.org) and setting the path.
- Maven can also be used within IDEs that provide Maven support.

## 1.8 Creating a Maven Project in IntelliJ
- Demonstration of creating a Maven project in IntelliJ IDEA.
- Selection of Maven as the build system ensures consistency across different IDEs.
- Maven project structure remains the same, irrespective of the IDE being used.

## 1.9 Maven Phases and Plugins
- Maven simplifies project development with predefined phases: compile, test, package, install, deploy.
- Plugins in Maven provide functionality for each phase.
- Introduction to Maven plugins for compiling, testing, packaging, installing, and deploying.

## 1.10 Using Maven in an IDE
- Maven can be utilized within IDEs to streamline project development.
- Demonstration of Maven support in IntelliJ IDEA.
- Utilizing Maven lifecycle options within the IDE for tasks such as compiling, testing, packaging, installing, and deploying.

## 1.11 Maven Lifecycle
- Overview of Maven lifecycle and how it aligns with project development phases.
- Lifecycle options in Maven can be accessed within the IDE, providing an easy way to execute tasks.
- Maven plugins play a crucial role in executing these tasks.

## 1.12 Maven Setup in Eclipse
- While IntelliJ IDEA was used in the demonstration, Maven can also be set up and used seamlessly with Eclipse.
- Eclipse provides Maven integration through the "m2e" plugin, making it convenient for Maven-based project development.

### 1.12.1 Installing m2e Plugin
1. Open Eclipse and navigate to "Help" -> "Eclipse Marketplace."
2. Search for "m2e" or "Maven Integration for Eclipse" in the Marketplace.
3. Install the plugin by following the on-screen instructions.
4. Restart Eclipse to apply the changes.

### 1.12.2 Creating a Maven Project in Eclipse
1. Open Eclipse and select "File" -> "New" -> "Project."
2. Choose "Maven" from the project types and select "Maven Project." Click "Next."
3. In the "New Maven Project" wizard, enter the group and artifact IDs.
4. Select the desired archetype for the project. Common choices include "maven-archetype-quickstart" for a simple Java project.
5. Click "Finish" to create the Maven project.

### 1.12.3 Maven Support in Eclipse
1. Once the Maven project is created, Eclipse will automatically download the necessary dependencies specified in the project's `pom.xml` file.
2. Eclipse will show a small "M" icon on the project, indicating Maven support.
3. Utilize the Maven context menu options by right-clicking on the project:
   - Update Project: Downloads and updates dependencies.
   - Maven -> Build: Executes the default lifecycle phases.
   - Maven -> Update Project: Updates the project configuration.

### 1.12.4 Maven Phases in Eclipse
1. In Eclipse, navigate to the "Maven" view or open the "pom.xml" file.
2. The "Maven" view provides a graphical representation of the project's lifecycle phases.
3. Right-click on the project or the `pom.xml` file and select "Run As" -> "Maven Build" to execute specific Maven goals.

### 1.12.5 Additional Maven Tasks in Eclipse
1. Eclipse supports various Maven tasks, including:
   - Running tests: Right-click on the project -> "Run As" -> "Maven Test."
   - Building a package: Right-click on the project -> "Run As" -> "Maven Install."
   - Deploying: Right-click on the project -> "Run As" -> "Maven Deploy."

### 1.12.6 Maven Configuration in Eclipse
1. Eclipse allows further Maven configuration:
   - **Settings:** Configure Maven settings by navigating to "Window" -> "Preferences" -> "Maven."
   - **Repositories:** View and manage Maven repositories under the "Maven" view.

By following these steps, developers can set up and use Maven seamlessly within the Eclipse IDE, maintaining consistency and ease of use across different Java development environments.

# Chapter 2: Maven Archetypes, Dependencies, and Project Management

## 2.1 Introduction to Maven Archetypes
- **Archetypes:** Maven provides archetypes, templating tools, to simplify project setup.
- Archetypes allow creating templates for specific project types, streamlining configuration and dependency management.

## 2.2 Creating and Using Maven Archetypes
- **Using Archetypes in Eclipse:**
  1. Open Eclipse and navigate to "File" -> "New" -> "Maven Project."
  2. In the "New Maven Project" dialog, check the option for "Create a simple project (skip archetype selection)."
  3. Click "Next" and provide the necessary details for the project (Group ID, Artifact ID, etc.).
  4. Click "Finish" to create the Maven project.

- **Creating Custom Archetypes:**
  - Developers can create custom archetypes for standardized project setups.
  - Use the `archetype:create-from-project` goal to generate an archetype from an existing project.

## 2.3 Maven Dependency Management
- Maven handles dependencies, which are JAR files representing external libraries.
- Manually downloading and managing JAR files can lead to version issues and maintenance challenges.
- Maven provides a centralized approach to handle dependencies efficiently.

## 2.4 Obtaining Dependencies in Maven
- Dependencies are specified in the `pom.xml` file using XML tags.
- Developers can manually download JAR files and add them to the project, but this requires managing versions.
- Alternatively, developers can use Maven repositories like `mvnrepository.com` to simplify dependency management.

## 2.5 Maven Coordinates: Group ID, Artifact ID, and Version
- Maven coordinates, represented as GAV (Group ID, Artifact ID, Version), uniquely identify a library.
- **Example of MySQL Connector Dependency in POM:**
  ```xml
  <dependencies>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>8.0.23</version>
      </dependency>
  </dependencies>
  ```

## 2.6 Maven POM (Project Object Model)
- The `pom.xml` file in Maven is called the Project Object Model (POM).
- POM contains essential information about the project, including dependencies, plugins, and configurations.
- Key elements in POM include group ID, artifact ID, and version.

## 2.7 Managing Dependencies in POM
- **Steps to Add Dependencies in POM in Eclipse:**
  1. Open the `pom.xml` file in Eclipse.
  2. Locate the `<dependencies>` section.
  3. Add a `<dependency>` block for each library, specifying group ID, artifact ID, and version.

## 2.8 Loading Maven Dependencies in Eclipse
- Eclipse provides seamless integration with Maven.
- Developers can add dependencies to the POM file and reload Maven to download and manage the JAR files.
- Maven dependencies are visible in the Eclipse project's "External Libraries" section.

## 2.9 Transitive Dependencies in Maven
- Maven handles transitive dependencies automatically.
- **Example:** Adding Hibernate as a dependency may result in additional dependencies required by Hibernate.
- The POM file simplifies the management of both direct and transitive dependencies.

## 2.10 Maven's Centralized Dependency Management
- Maven's centralized approach ensures that developers don't need to distribute JAR files.
- Sharing the POM file enables others to reload Maven and obtain the necessary dependencies automatically.
- Maven's simplicity and consistency make it an efficient tool for project management and dependency handling.

These detailed notes include steps for choosing archetypes in Eclipse, a sample POM file for MySQL Connector, and explanations on Maven dependency management and centralized coordination. The emphasis is on practical usage within the Eclipse IDE.

# Chapter 3: Maven POM Configuration and Effective POM

## 3.1 Overview of POM File
- The **Project Object Model (POM)** file, commonly named `pom.xml`, is crucial in Maven.
- It contains configurations and settings for Maven to manage a project effectively.

## 3.2 Configuring Maven in POM
- **Maven Configuration:** The `pom.xml` file is used to configure Maven settings.
- Essential configurations include group ID, artifact ID, and version for project identification.

### 3.2.1 Creating a JAR File
- To create a JAR file for your project, specify the following in the POM:
  ```xml
  <groupId>com.example</groupId>
  <artifactId>my-project</artifactId>
  <version>1.0.0</version>
  ```

### 3.2.2 Adding Dependencies
- **Dependencies Section:** In the `dependencies` tag, specify dependencies for your project.
- Multiple dependencies can be added for libraries required in the project.
- Example:
  ```xml
  <dependencies>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-core</artifactId>
          <version>5.3.9</version>
      </dependency>
      <!-- Additional dependencies can be added here -->
  </dependencies>
  ```

### 3.2.3 Working with Plugins
- **Plugins Section:** Add a `plugins` tag to configure plugins for additional functionalities.
- Plugins enhance build processes and provide extra features.
- Example:
  ```xml
  <build>
      <plugins>
          <!-- Example plugin configuration -->
          <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.1</version>
          </plugin>
          <!-- Additional plugins can be added here -->
      </plugins>
  </build>
  ```

## 3.3 Effective POM (Super POM)
- **Effective POM:** Maven generates an Effective POM, also known as Super POM.
- The Effective POM includes both default configurations and user-defined settings from the POM file.
- It contains additional configurations not explicitly mentioned in the POM file.

### 3.3.1 Viewing Effective POM in IntelliJ IDEA
- **IntelliJ IDEA:** To view the Effective POM in IntelliJ IDEA, follow these steps:
  1. Right-click on `pom.xml`.
  2. Navigate to "Maven" and select "Show Effective POM."
  3. The Effective POM will open, revealing all configurations, including default ones.

### 3.3.2 Importance of Effective POM
- Maven refers to the Effective POM when interacting with a project.
- Default configurations, such as plugin repositories, are present in the Effective POM.
- Developers make changes in the `pom.xml`, and Maven automatically reflects these changes in the Effective POM.

### 3.3.3 Effective POM in Other IDEs
- **Eclipse IDE:** In Eclipse, the Effective POM can be viewed through a dedicated tab or menu option.
- Effective POM is an essential concept for understanding Maven's behind-the-scenes configurations.

### What are Plugins:
In the context of Maven, plugins are tools or extensions that enhance and extend the functionality of the build process. They allow developers to perform various tasks during different phases of the software development life cycle. Maven plugins are configured in the `pom.xml` file and are executed by Maven as part of its build process.

Here are some key points about Maven plugins:

1. **Execution of Goals:**
   - Plugins are composed of goals, and each goal represents a specific task or operation that can be executed.
   - A plugin can have multiple goals, and each goal is responsible for a specific aspect of the build.

2. **Configuration in POM:**
   - Plugins are configured in the `<build>` section of the `pom.xml` file.
   - Developers can specify which plugins to use, along with their configurations and goals.

3. **Built-in and Custom Plugins:**
   - Maven comes with a set of built-in plugins that cover common tasks like compiling code, running tests, packaging artifacts, and more.
   - Developers can also use custom plugins or third-party plugins to extend Maven's functionality based on their project requirements.

4. **Lifecycle Binding:**
   - Plugins are often associated with specific phases of the Maven build lifecycle.
   - For example, the `maven-compiler-plugin` is bound to the `compile` phase, and it handles the compilation of Java source code.

5. **Dependency Management:**
   - Plugins can manage project dependencies, download external libraries, and handle transitive dependencies.
   - The `maven-dependency-plugin` is an example of a plugin used for dependency-related tasks.

6. **Example: Maven Compiler Plugin:**
   - One commonly used plugin is the `maven-compiler-plugin`, which provides options for compiling Java source code.
   - Configuration in the `pom.xml` might look like this:
     ```xml
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
     ```

7. **Custom Plugin Development:**
   - Developers can create their custom plugins if the built-in or available plugins do not meet specific requirements.
   - Custom plugins are often developed using the Maven Plugin API.

Plugins play a crucial role in automating and streamlining various tasks within a Maven project, making it a versatile and extensible build tool. They contribute to Maven's flexibility and help developers manage complex build processes efficiently.

Certainly! Let's delve deeper into Maven plugins:

1. **Plugin Configuration:**
   - Plugins are configured in the `<plugins>` section within the `<build>` element of the `pom.xml` file.
   - Configuration parameters for plugins are specified within the `<configuration>` element.

2. **Common Built-In Plugins:**
   - Maven provides a set of common built-in plugins that cover a wide range of tasks:
     - `maven-compiler-plugin`: Handles compilation of Java source code.
     - `maven-surefire-plugin`: Executes unit tests.
     - `maven-jar-plugin`: Creates a JAR file from the project output.
     - `maven-war-plugin`: Packages the project output as a WAR file for web applications.

3. **Custom Plugin Goals:**
   - Developers can define custom goals within a plugin to perform specific actions tailored to their project needs.
   - These custom goals can be executed during different phases of the Maven build lifecycle.

4. **Lifecycle and Phases:**
   - Maven follows a predefined build lifecycle composed of phases (e.g., `compile`, `test`, `package`, `install`, `deploy`).
   - Plugins are often associated with specific phases, determining when their goals are executed during the build.

5. **Plugin Repositories:**
   - Plugins and their associated dependencies are retrieved from plugin repositories.
   - Maven Central Repository is a default repository for plugins, but additional repositories can be configured.

6. **Plugin Versions:**
   - Plugins, like dependencies, have versions. Developers specify the plugin version to ensure consistency and reproducibility.
   - Updating plugin versions might provide new features or bug fixes.

7. **Plugin Execution Order:**
   - Plugins and their goals are executed in a specific order during the Maven build.
   - The order is determined by the configured phases and the order in which plugins are specified in the `pom.xml` file.

8. **Plugin Binding to Phases:**
   - A plugin can be explicitly bound to a phase or set of phases within the `<build>` configuration.
   - For example, a plugin might be bound to the `test` phase to execute its goals during the testing phase.

9. **Plugin Documentation:**
   - Each Maven plugin has associated documentation that provides information on available goals, parameters, and usage.
   - Referencing plugin documentation is crucial for effective utilization.

10. **Plugin Development:**
    - Maven allows developers to create their own custom plugins using the Maven Plugin API.
    - Custom plugins can encapsulate specific functionality needed for a project.

11. **Plugin Inheritance:**
    - Parent and child projects in a multi-module Maven project share plugin configurations.
    - This helps maintain consistency across modules.

12. **Profile-Specific Plugins:**
    - Plugins and their configurations can be specified within profiles, allowing different build behaviors for various environments.

Understanding these aspects of Maven plugins empowers developers to effectively manage and customize the build process according to project requirements. Plugins contribute to Maven's flexibility, allowing it to accommodate diverse project types and workflows.

# Chapter 4: Maven Archetypes

## Introduction to Archetypes

- **Archetypes in Maven:**
  - Archetypes are templates or blueprints for project structures.
  - Developers can create their own templates or use existing ones provided by Maven.
  - Archetypes help in avoiding manual project setup and configuration.

## Using Archetypes in Eclipse

1. **Accessing Archetypes in Eclipse:**
   - Navigate to "New Project" in Eclipse.
   - Choose the Maven Project option.
   - In the project creation wizard, select the "Maven Archetype" option.

2. **Selecting Archetype:**
   - Provide a project name (e.g., DemoSpringProject).
   - Explore the "Archetype" options, which include:
     - `j2ee-simple`: For J2EE projects.
     - `quickstart`: Basic configuration.
     - `webapp`: For web applications.

3. **Internal vs. External Catalogs:**
   - The default catalog is the Internal Catalog.
   - Explore archetypes from the internet using the Maven Central catalog.
   - External catalogs may offer additional archetypes.

4. **Downloading Dependencies:**
   - Dependencies are downloaded from the selected archetype.
   - Maven Central provides a wide range of archetypes.
   - Dependencies include libraries and frameworks relevant to the selected archetype.

5. **Example - Spring Boot MVC Archetype:**
   - Choose an archetype like `spring-boot-mvc` for a REST application.
   - Observe the downloaded dependencies, which may include:
     - `spring-boot-starter`
     - `webmvc`
     - `jersey`
     - `jdbc`
     - `h2`

6. **Generated Project Structure:**
   - Archetypes create a predefined project structure.
   - Initial code, configuration, and resources are included.
   - Key files include `pom.xml`, source files, tests, and configuration files.

## Archetypes in Eclipse vs. Command Line

- **Eclipse vs. Command Line:**
  - Eclipse provides a user-friendly interface for selecting archetypes.
  - Command-line users can utilize the `mvn archetype:generate` command.
  - Command-line usage offers more flexibility but may require manual input.

## Conclusion

- **Advantages of Archetypes:**
  - Quick project setup with predefined configurations.
  - Avoids manual dependency management.
  - Streamlines development by providing a project structure.

- **Flexibility and Customization:**
  - Developers can create their own archetypes for specific project needs.
  - Archetypes contribute to consistency across projects within an organization.

- **Efficient Project Kickstart:**
  - Archetypes serve as a powerful tool for rapidly initiating projects.
  - Ideal for beginners and experienced developers alike.

By leveraging Maven archetypes, developers can efficiently start projects with the desired configurations and dependencies, fostering consistency and accelerating development workflows.

# Chapter 6: Maven Lifecycle and Execution

## Understanding Maven Lifecycle

- **Overview of Maven Lifecycle:**
  - Maven follows a defined lifecycle that consists of phases.
  - Each phase represents a stage in the build process.

- **Maven Phases:**
  - Key phases include `validate`, `compile`, `test`, `package`, `install`, and `deploy`.
  - Phases are executed sequentially, and each phase completes specific tasks.

## Maven Lifecycle Phases

1. **validate:**
   - Validates the project structure and configurations.

2. **compile:**
   - Compiles the source code into bytecode.

3. **test:**
   - Executes unit tests.

4. **package:**
   - Packages compiled code into a distributable format, like JAR or WAR.

5. **integration-test:**
   - Performs integration tests.

6. **verify:**
   - Performs any checks to verify the package is valid.

7. **install:**
   - Installs the package in the local repository for use as a dependency in other projects.

8. **deploy:**
   - Copies the package to a remote repository for sharing.

## Executing Maven Lifecycle Phases

### From Eclipse:

1. **Run Configuration:**
   - Open the project in Eclipse.
   - Right-click on the project, go to "Run As," and select "Maven Build."

2. **Set Goals:**
   - In the dialog, set the "Goals" field to the desired Maven goals or phases.

3. **Run:**
   - Click "Run" to execute the specified Maven goals.

### From Command Line (CMD):

1. **Navigate to Project Directory:**
   - Open the CMD and navigate to the project's root directory.

2. **Execute Maven Commands:**
   - Use the `mvn` command followed by the desired Maven goals or phases.

   ```bash
   # Example: Execute Maven Compile Phase
   mvn compile
   ```

   ```bash
   # Example: Execute Maven Install Phase
   mvn install
   ```

3. **Additional Command Options:**
   - Maven commands can include additional options, such as `-D` for system properties or `-X` for debug information.

   ```bash
   # Example: Execute Maven Clean and Install Phases
   mvn clean install
   ```

## Common Maven Commands

1. **`mvn clean`:**
   - Cleans the project by removing the `target` directory.

2. **`mvn compile`:**
   - Compiles the source code.

3. **`mvn test`:**
   - Executes unit tests.

4. **`mvn package`:**
   - Packages the compiled code into a distributable format.

5. **`mvn install`:**
   - Installs the package in the local repository.

6. **`mvn deploy`:**
   - Deploys the package to a remote repository.

## Conclusion

Understanding the Maven lifecycle and how to execute phases is essential for efficient project management. Developers can run Maven phases from both Eclipse and the command line, enabling seamless integration into their development workflows. Familiarity with common Maven commands empowers developers to build, test, and deploy projects effectively.


# **Section 2: Spring Framework 6**
# Chapter 6: Introduction to Spring Framework

## Understanding Spring Framework and Spring Boot

- **Spring Framework Overview:**
  - Frameworks are essential for building large-scale applications.
  - Spring is a widely used framework for developing enterprise-level Java applications.
  - Spring simplifies development by providing a comprehensive set of features.

- **Evolution of Java Frameworks:**
  - Previous frameworks like EJB, Struts, and Hibernate served specific purposes.
  - Spring emerged as a unified framework, offering a wide range of functionalities.

- **Key Features of Spring:**
  - Lightweight: Spring is a lightweight framework, emphasizing simplicity and flexibility.
  - POJO (Plain Old Java Object): Spring promotes the use of POJOs for development.

- **Spring.io and Ecosystem:**
  - Visit [spring.io](https://spring.io/) for official Spring documentation and resources.
  - Spring is an ecosystem comprising various projects and modules.

- **Diverse Capabilities of Spring:**
  - Spring enables the development of:
    - Microservices
    - Reactive Applications
    - Cloud Applications
    - Web Applications
    - Serverless Applications
    - Event-Driven Applications
    - Batch Processing

- **Spring Code Example:**
  - A simple code snippet demonstrates a Spring Boot "Hello World" application.
  - Spring Boot simplifies development using annotations and minimal configuration.

  ```java
  @RestController
  public class HelloWorldController {
      @GetMapping("/hello")
      public String hello() {
          return "Hello, World!";
      }
  }
  ```

- **Spring Projects:**
  - Explore various Spring projects that cater to specific development needs.
  - Projects include Spring Boot, Spring Framework, Spring Data, Spring Cloud, Spring Security, Spring for GraphQL, and more.

- **Upcoming and Growing Projects:**
  - Spring continually evolves, introducing new projects to address emerging trends.
  - Examples include projects for building Agile applications, support for Scala and Kotlin in Spring.

## Course Outline

- **Course Content:**
  - The course covers multiple aspects of the Spring ecosystem.
  - Topics include Spring Framework, Spring Boot, Spring ORM, Spring AOP, Spring Security, and Microservices.

- **Hands-on Learning:**
  - Practical demonstrations and examples will be provided to reinforce theoretical concepts.
  - Focus on understanding the syntax and code structure of Spring applications.

- **Enterprise Application Development:**
  - Emphasis on how Spring is well-suited for building enterprise-level applications.
  - The course progresses from fundamental concepts to advanced topics.

  # Chapter 6 (Continued): Why Spring Framework?

  ## Factors Contributing to Spring's Popularity

  - **Features of a Successful Framework:**
    - Frameworks gain popularity based on key factors, starting with robust features.
    - Spring is renowned for its extensive feature set, providing solutions to diverse development challenges.

  - **Importance of Community:**
    - A thriving community is crucial for the success of any technology or framework.
    - Spring has garnered widespread adoption due to its active and supportive community.

  - **Documentation Excellence:**
    - Effective documentation is another hallmark of a successful framework.
    - Spring offers comprehensive and well-structured documentation to aid developers.
    - Access official Spring documentation at [Spring Docs](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html).

  - **Official Documentation Overview:**
    - The official documentation provides in-depth insights into various aspects of the Spring Framework.
    - Versions are clearly indicated, allowing users to choose the relevant documentation for their working environment.

  - **Navigating Spring Documentation:**
    - Explore specific topics by selecting appropriate sections in the documentation.
    - For instance, understanding Aspect-Oriented Programming (AOP) with Spring can be achieved by navigating to the relevant section.

  - **Version Considerations:**
    - Keep track of the Spring version used in the course (e.g., version 6.1.1).
    - Documentation is version-specific, ensuring accurate and relevant information.

  - **Reading Recommendations:**
    - Besides video content, consider delving into the official documentation for an in-depth understanding.
    - Reading documentation complements practical learning and reinforces conceptual knowledge.

  ## Accessing Spring Documentation

  - **Quick Access:**
    - Use search engines to quickly find the official Spring documentation.
    - A search for "Spring docs" typically leads to the official documentation page.

  - **Version Selection:**
    - Ensure you refer to the documentation version aligned with the course content.
    - Different versions may have variations in features and functionalities.

  - **Example Topic: AOP in Spring:**
    - Illustration of accessing documentation for a specific topic, such as Aspect-Oriented Programming (AOP).
    - Explore the documentation to gain insights into AOP implementation in Spring.

  ## Additional Learning Resources

  - **Books and Learning Materials:**
    - Complement video tutorials with additional learning resources available in books.
    - Spring-related books provide in-depth knowledge and practical insights.

  - **Ongoing Updates:**
    - Stay informed about the latest Spring updates and releases.
    - Continuous learning ensures alignment with industry best practices and emerging features.

  ## Conclusion

  Understanding why Spring is widely adopted involves recognizing its robust features, strong community support, and comprehensive documentation. The availability of official documentation allows developers to delve deep into various aspects of the Spring Framework. While video tutorials provide practical insights, the documentation serves as a valuable resource for those who prefer reading. Navigating the documentation and exploring specific topics enhances the learning experience and equips developers with a solid foundation in Spring.

  # Chapter 7: IoC (Inversion of Control) and DI (Dependency Injection)

  ## Understanding IoC (Inversion of Control)

  - **IoC Full Form:**
    - **IoC:** Inversion of Control

  - **IoC Concept:**
    - Inversion of Control means inverting control from the programmer to another entity.
    - Programmers typically control object creation, maintenance, and destruction.
    - IoC shifts the responsibility of object creation and management to an external entity.

  - **Programmer Roles:**
    - Programmers traditionally handle object creation using the `new` keyword.
    - Managing objects becomes a responsibility, which can divert focus from the application's core business logic.

  - **IoC Principle:**
    - IoC is a principle where control shifts from the programmer to an external entity for object creation and management.
    - The main focus of the programmer should be on the business logic of the application.

  - **Spring IoC Container:**
    - In Spring, the IoC principle is implemented using an IoC container.
    - The IoC container acts as a box where objects are created and managed.
    - Spring, as an IoC container, takes charge of object creation and management.

  ## Introduction to Dependency Injection (DI)

  - **DI Full Form:**
    - **DI:** Dependency Injection

  - **DI Concept:**
    - Dependency Injection is a design pattern.
    - It addresses the need to inject dependencies into an application.

  - **Example Scenario:**
    - Consider a `Laptop` class that depends on a `CPU` class.
    - Both objects exist, but Dependency Injection is required to inject the `CPU` object into the `Laptop` object.

  - **Spring and DI:**
    - Spring employs Dependency Injection to implement the IoC principle.
    - Objects are created by the IoC container and injected into the application as needed.

  ## Relationship Between IoC and DI

  - **Interchanging Terms:**
    - While IoC and Dependency Injection are often used interchangeably, they represent different aspects.
    - IoC is a principle, and DI is a design pattern that implements IoC.

  - **IoC Principle Overview:**
    - In IoC, control is shifted from the programmer to an external entity for object creation and management.
    - The focus shifts from managing objects to concentrating on the business logic of the application.

  - **DI Design Pattern:**
    - Dependency Injection is a design pattern that facilitates the injection of dependencies into an application.
    - It ensures that objects are appropriately linked and injected as needed.

  - **IoC Container in Spring:**
    - In Spring, the IoC container is integral to implementing IoC.
    - Dependency Injection is used to inject objects created by the IoC container into the application.

  ## Conclusion

  Understanding IoC and DI is foundational to comprehending the Spring Framework's approach to application development. IoC involves shifting control from programmers to an external entity, while DI is a design pattern for injecting dependencies. Spring employs an IoC container to manage object creation and uses Dependency Injection to seamlessly inject these objects into the application. The distinction between IoC as a principle and DI as a design pattern provides clarity on their roles in Spring development.

  # Chapter 7 (Continued): Implementing Dependency Injection with Spring Boot

  ## Understanding Spring and Spring Boot Relationship

  - **Spring and Spring Boot:**
    - **Spring:** Framework for building enterprise-level Java applications.
    - **Spring Boot:** Built on top of Spring, provides an opinionated framework for quick project setup and ease of configuration.

  - **Evolution of Spring:**
    - In the initial days of Spring, extensive configuration was required for even simple tasks like printing "Hello, World."
    - Spring Boot was introduced to streamline project setup, making it less verbose and more efficient.

  - **Spring Boot Features:**
    - Opinionated framework: Provides sensible defaults for project configuration.
    - Quick project setup: Reduces the need for extensive manual configuration.
    - Eases the process of creating and running Spring projects.

  ## Importance of Spring Boot in Dependency Injection

  - **Challenges with Spring:**
    - Traditional Spring projects involved substantial configuration efforts.
    - Creating projects required configuring XML files, defining beans, etc.

  - **Spring Boot's Solution:**
    - Spring Boot simplifies project creation by offering a structure that works "out of the box."
    - It minimizes the need for extensive manual configuration.

  - **Versioning:**
    - Spring Boot versions work in conjunction with Spring versions.
    - Example: Spring Boot 3 works with Spring 6.

  ## Spring Framework and Spring Boot in Practice

  - **Working with Spring Boot:**
    - Preferred choice for project development due to its simplicity and quick setup.
    - Generates a project structure that is ready to run without additional configuration.
    - Allows for customization if needed.

  - **Understanding the Role of Spring Framework:**
    - Even when using Spring Boot, understanding the underlying Spring framework is crucial.
    - Spring Boot operates on top of the Spring framework.
    - Both Spring and Spring Boot contribute to the development process.

  ## Implementing Dependency Injection with Spring Boot

  - **Objective:**
    - Implement Dependency Injection using Spring Boot for practical demonstration.

  - **Working with Spring 6 and Spring Boot 3:**
    - Current versions used in the demonstration.

  - **Code Example:**
    - Showcase a simple Spring Boot project with Dependency Injection.

  ### Spring Boot Dependency Injection Example

  ```java
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.stereotype.Component;

  @Component
  class MessageService {
      public String getMessage() {
          return "Hello, Dependency Injection!";
      }
  }

  @Component
  class MyComponent {
      private final MessageService messageService;

      @Autowired
      public MyComponent(MessageService messageService) {
          this.messageService = messageService;
      }

      public void displayMessage() {
          System.out.println(messageService.getMessage());
      }
  }

  @SpringBootApplication
  public class SpringBootApp {
      public static void main(String[] args) {
          SpringApplication.run(SpringBootApp.class, args);
      }
  }
  ```

  In this example:
  - `MessageService` is a simple service class.
  - `MyComponent` is a component that relies on `MessageService` through constructor injection.
  - `SpringBootApp` is the main Spring Boot application class.

  ## Conclusion

  Understanding the relationship between Spring and Spring Boot is essential for effective project development. While Spring Boot simplifies project setup and reduces manual configuration efforts, it is crucial to recognize that Spring Boot operates on the foundation of the Spring framework. The seamless integration of Spring Boot with the underlying Spring framework ensures that developers benefit from both worlds. The practical implementation of Dependency Injection using Spring Boot provides insights into its efficiency and ease of use.


  # Chapter 8 (Continued): Implementing Dependency Injection in Spring

  ## Introduction

  In this section, we will explore the practical implementation of dependency injection in a Spring Boot application. We'll create a simple `Alien` class, run the Spring Boot application, and delve into the details of obtaining managed objects from the IoC container.

  ## Practical Implementation

  ### 1. Creating the Alien Class

  Let's start by creating a straightforward `Alien` class representing our imaginary programmers.

  ```java
  import org.springframework.stereotype.Component;

  @Component
  public class Alien {
      public void code() {
          System.out.println("Coding");
      }
  }
  ```

  Here, we've annotated the class with `@Component` to inform Spring that it should manage instances of this class.

  ### 2. Running the Spring Boot Application

  In our main application file, we initiate the Spring Boot application, obtaining an `ApplicationContext` to interact with the IoC container.

  ```java
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.context.ApplicationContext;

  @SpringBootApplication
  public class SpringBootFirstApplication {
      public static void main(String[] args) {
          ApplicationContext context = SpringApplication.run(SpringBootFirstApplication.class, args);

          // Retrieve the Alien object from the container
          Alien alienObj = context.getBean(Alien.class);

          // Calling the code method of the Alien object
          alienObj.code();
      }
  }
  ```

  By running this application, we witness the "Coding" output, indicating the successful creation and utilization of the `Alien` object.

  ### 3. Utilizing @Component Annotation

  The `@Component` annotation plays a crucial role here, signaling to Spring that the `Alien` class should be managed by the IoC container.

  ```java
  @Component
  public class Alien {
      public void code() {
          System.out.println("Coding");
      }
  }
  ```

  ### 4. Creating Multiple Instances

  We further explore the default behavior of Spring in creating singleton objects by default. Adding the following code creates a second `Alien` object.

  ```java
  Alien alienObj2 = context.getBean(Alien.class);
  alienObj2.code();
  ```

  When executed, the "Coding" message appears twice, showcasing that Spring, by default, creates singleton instances.

  ## Key Takeaways

  - The `@Component` annotation is pivotal in informing Spring about the classes it should manage.
  - The IoC container, accessed through `ApplicationContext`, handles object creation and management.
  - Spring Boot facilitates the process of initiating a Spring application, creating a structured and manageable project.

  By practicing these concepts, developers gain a practical understanding of dependency injection in Spring applications.


  # Chapter 8 (Continued): Implementing Dependency Injection in Spring

  ## Introduction

  In the preceding section, we successfully implemented a basic example of dependency injection using Spring Boot. Now, let's extend our understanding by introducing dependencies between objects, exemplified by an `Alien` class dependent on a `Laptop` class.

  ## Handling Dependencies

  ### 1. Creating the Laptop Class

  Let's start by creating a simple `Laptop` class.

  ```java
  import org.springframework.stereotype.Component;

  @Component
  public class Laptop {
      public void compile() {
          System.out.println("Compiling");
      }
  }
  ```

  As before, we use the `@Component` annotation to inform Spring that this class should be managed.

  ### 2. Modifying the Alien Class

  Now, let's modify the `Alien` class to include a dependency on the `Laptop` class.

  ```java
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Component;

  @Component
  public class Alien {
      private final Laptop laptop;

      @Autowired
      public Alien(Laptop laptop) {
          this.laptop = laptop;
      }

      public void code() {
          laptop.compile();
          System.out.println("Coding");
      }
  }
  ```

  By adding the `@Autowired` annotation to the constructor, we indicate to Spring that an instance of `Laptop` is required to create an `Alien` object.

  ### 3. Testing the Dependency

  Now, in the main application class, let's retrieve the `Alien` object and call its `code` method.

  ```java
  public class SpringBootFirstApplication {
      public static void main(String[] args) {
          ApplicationContext context = SpringApplication.run(SpringBootFirstApplication.class, args);

          // Retrieve the Alien object from the container
          Alien alienObj = context.getBean(Alien.class);

          // Calling the code method of the Alien object
          alienObj.code();
      }
  }
  ```

  ### 4. Understanding AutoWiring

  The `@Autowired` annotation provides automatic wiring between dependent objects, establishing a connection between `Alien` and `Laptop`. This way, when an `Alien` is created, Spring automatically injects the required `Laptop` instance.

  ## Conclusion

  In this section, we explored the concept of dependency injection with an example involving multiple classes. We learned how to annotate classes for component scanning, leverage constructor injection, and enable Spring to handle object dependencies automatically. Understanding these principles is crucial for comprehending the inner workings of the Spring framework.

  In the next section, we will delve into the Spring framework, exploring different configurations, such as XML-based, Java-based, and annotation-based approaches. This knowledge will enhance our understanding of how dependency injection is achieved without the conveniences provided by Spring Boot. Stay tuned for a deeper dive into the core concepts of Spring.

# **Section 3: Spring Framework**
# Chapter 10: Introduction to Spring Framework

## Overview

In this chapter, we will transition from working with Spring Boot to exploring the Spring Framework itself. Understanding the inner workings of Spring is essential for gaining a deeper comprehension of how Spring Boot simplifies many tasks for developers.

## Creating a New Spring Project

1. **Choosing the IDE and Creating a Maven Project:**
   - Open your preferred IDE (IntelliJ or Eclipse).
   - Create a new Maven project named "SpringDemo."

2. **Configuring Maven Project:**
   - Set the project location and ensure Maven is selected.
   - Define the group ID (e.g., `com.telusko`), artifact ID (e.g., `Spring1`), and version (e.g., `1.0`).

3. **Adding Maven Dependencies:**
   - Add the Spring Context dependency to the `pom.xml` file.

   ```xml
   <dependencies>
       <!-- Other dependencies -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-context</artifactId>
           <version>6.1.0</version>
       </dependency>
   </dependencies>
   ```

   - Refresh Maven in IntelliJ or reload the project in Eclipse.

## Writing Basic Spring Code

4. **Creating the Alien Class:**
   - In the `src/main` directory, create a simple `Alien` class with a method (e.g., `code`) to represent business logic.

   ```java
   public class Alien {
       public void code() {
           System.out.println("Coding");
       }
   }
   ```

5. **Setting Up the Application Context:**
   - Import the necessary classes for Spring, including `ApplicationContext`.
   - Use Maven to download dependencies.

   ```java
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   public class SpringDemoApplication {
       public static void main(String[] args) {
           // Creating the application context
           ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");

           // Getting the Alien object from the container
           Alien alienObj = (Alien) context.getBean("alien");

           // Calling the code method of the Alien object
           alienObj.code();
       }
   }
   ```

   - Note: The `spring.xml` file will be created in the next steps.

6. **Configuring the Spring XML File (`spring.xml`):**
   - Create a new XML file named `spring.xml` in the `src/main/resources` directory.

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd">

       <!-- Defining the Alien bean -->
       <bean id="alien" class="com.telusko.Spring1.Alien"/>
   </beans>
   ```

   - Define the bean with an ID (`alien`) and specify the class.

7. **Running the Spring Application:**
   - Run the `SpringDemoApplication` class to execute the Spring application.

## Understanding the Application Context

8. **Introduction to `ApplicationContext`:**
   - The `ApplicationContext` is responsible for creating and managing the Spring IoC (Inversion of Control) container.
   - It provides methods like `getBean` to retrieve objects from the container.

9. **Choosing `ClassPathXmlApplicationContext`:**
   - We use `ClassPathXmlApplicationContext` for configuration through an XML file.

10. **Troubleshooting Errors:**
    - If you encounter the "BeanFactory not initialized or already closed" error, it implies that the container is not properly initialized. The resolution will be covered in the next section.

## Conclusion

In this chapter, we laid the foundation for a Spring project by setting up a Maven project, adding Spring dependencies, creating a basic class (`Alien`), and configuring the application context through an XML file. We also briefly discussed the role of `ApplicationContext` in managing the IoC container. The next section will address the error encountered during execution and delve deeper into the Spring Framework's functionality.

# Chapter 10 (Continued): Configuring Spring with XML

## Troubleshooting the "BeanFactory not initialized or already closed" Error

In the previous video, we encountered an error indicating that the `BeanFactory` was not initialized or already closed. This error occurred at line 10, indicating an issue with retrieving the object from the container. The problem lies in the container not being able to find the `alien` bean.

## Configuring Spring with XML

1. **Creating a Resources Folder:**
   - In the `src/main` directory, create a folder named `resources`.
   - This is where Spring will look for XML configuration files.

2. **Creating a Spring XML Configuration File:**
   - Create a new XML file in the `resources` folder (e.g., `spring.xml`).
   - Open the XML file in the IDE.

3. **Defining the `bean` in Spring XML:**
   - Use the `bean` tag to define the Spring beans (objects).
   - Specify the `id` for the bean, which is the name used to retrieve it.
   - Provide the fully qualified class name for the `class` attribute.

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd">

       <!-- Defining the Alien bean -->
       <bean id="alien" class="com.telusko.Spring1.Alien"/>
   </beans>
   ```

   - Ensure that the XML file adheres to Spring's configuration standards.

4. **Resolving XML Configuration Issues:**
   - The initial XML might trigger an error. Copy the necessary XML namespace definitions and bean declaration from the Spring documentation.

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd">
   </beans>
   ```

   - Make sure to close the `beans` tag properly.

5. **Refreshing the Project:**
   - Save the XML file and refresh your Maven project in the IDE.

6. **Rerunning the Application:**
   - Rerun the Spring application (`SpringDemoApplication`) to check if the issue is resolved.

7. **Verification:**
   - Upon successful execution, you should see the expected output, confirming that the Spring framework has successfully managed the `alien` bean.

## Understanding the Configuration Process

In this configuration, we informed the Spring framework about the `alien` bean and its associated class through XML. The `ApplicationContext` now knows how to initialize the bean when requested.

This approach allows for easy management of various classes and objects by the Spring framework. Future additions of classes require updating the Spring XML configuration to include the new beans.

# Chapter 10 (Continued): Object Creation and Bean Scopes in Spring

## Creating Objects with the ApplicationContext

Now that we've successfully configured Spring using XML, let's proceed to create objects of the configured class (`Alien`) using the `ApplicationContext`. In our `App` class, we'll now retrieve the `Alien` object from the container and invoke its methods.

### Modifying the App Class

Open the `App` class (`App.java`) and make the following modifications:

```java
package com.telusko.Spring1;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args) {

        // Creating the ApplicationContext
        ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");

        // Retrieving the Alien object from the container
        Alien obj = (Alien) context.getBean("alien");

        // Invoking methods on the Alien object
        obj.code();
    }
}
```

In this updated code:
- We create the `ApplicationContext` using `ClassPathXmlApplicationContext` and provide the XML configuration file name (`spring.xml`).
- The `getBean("alien")` method retrieves the `Alien` object from the container, casting it to the appropriate type.
- We then invoke the `code` method on the `Alien` object.

### Running the Application

Run the application to verify that the `code` method is executed, confirming that the `Alien` object has been successfully created and managed by Spring.

## Understanding Bean Scopes

In Spring, beans can have different scopes that define the lifecycle and visibility of the bean instances. The two common scopes are:

1. **Singleton Scope:**
   - The default scope in Spring.
   - A single instance of the bean is created for the entire application context.
   - Every request for the bean returns the same instance.

   ```xml
   <bean id="alien" class="com.telusko.Spring1.Alien" scope="singleton"/>
   ```

2. **Prototype Scope:**
   - A new instance of the bean is created whenever it is requested.
   - This scope is useful when you want a fresh instance each time the bean is needed.

   ```xml
   <bean id="alien" class="com.telusko.Spring1.Alien" scope="prototype"/>
   ```

### Updating the Spring XML Configuration

In the `spring.xml` file, you can specify the scope for the `alien` bean using the `scope` attribute. For example:

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" scope="singleton"/>
<!-- OR -->
<bean id="alien" class="com.telusko.Spring1.Alien" scope="prototype"/>
```

Choose the appropriate scope based on your application's requirements.

### Testing Bean Scopes

1. **Singleton Scope:**
   - With the `singleton` scope, the same instance of the `Alien` object is shared among different parts of the application.

2. **Prototype Scope:**
   - With the `prototype` scope, a new instance of the `Alien` object is created each time it is requested.

### Running the Application with Different Scopes

1. Update the `spring.xml` file with the desired scope (either `singleton` or `prototype`).
2. Rerun the `App` class to observe the behavior based on the chosen scope.

Experiment with both scopes to understand how they impact the lifecycle and usage of the `Alien` bean.

## Conclusion

By creating objects of the `Alien` class through the `ApplicationContext` and exploring different bean scopes, we've expanded our understanding of Spring's capabilities. The choice of scope depends on the intended use and requirements of the beans in your application. In the next sections, we will explore alternative ways of configuring Spring using Java-based configuration and annotations.


# Chapter 11: Setter Injection in Spring

In this chapter, we will dive into the concept of setter injection in Spring. Setter injection is a method of injecting values into the properties of a bean using setter methods. To demonstrate this, we'll focus on the `Alien` class, specifically its `age` property.

## Modifying Alien Class for Setter Injection

Firstly, we'll make the `age` variable private and generate getter and setter methods for it.

```java
package com.telusko.Spring1;

public class Alien {
    private int age;

    // Constructor, getter, and setter methods

    public void code() {
        System.out.println("Coding...");
    }
}
```

After generating the getter and setter methods, the `Alien` class is ready for setter injection.

## Using Setter Injection in App Class

Now, let's modify the `App` class (`App.java`) to demonstrate setter injection:

```java
package com.telusko.Spring1;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {

    public static void main(String[] args) {
        // Creating the ApplicationContext
        ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");

        // Retrieving the Alien object from the container
        Alien obj = (Alien) context.getBean("alien");

        // Invoking methods on the Alien object
        obj.code();

        // Displaying the age using the getter method
        System.out.println("Age: " + obj.getAge());
    }
}
```

In this modified code:
- We retrieve the `Alien` object from the container.
- We invoke the `code` method to ensure the basic functionality.
- We use the `getAge` method to display the `age` property.

## Implementing Setter Injection in Spring XML Configuration

Next, let's see how to perform setter injection in the `spring.xml` configuration file:

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <!-- Setter Injection for age property -->
    <property name="age" value="21"/>
</bean>
```

In this configuration:
- The `<property>` tag is used for setter injection.
- The `name` attribute specifies the property name (`age`).
- The `value` attribute provides the value to be injected into the `age` property.

Now, when the `Alien` bean is created, Spring will automatically call the setter method (`setAge`) to set the value.

## Understanding Setter Injection

Setter injection is a form of dependency injection where Spring injects values into the properties of a bean through setter methods. In this example, we injected the `age` property using setter injection.

### Benefits of Setter Injection:
- Flexibility: Values can be easily changed without modifying the Java code.
- Clear Separation: The initialization of properties is distinct from the object creation.

### Recap of Steps:
1. Make properties private in the bean class.
2. Generate getter and setter methods.
3. Configure setter injection in the Spring XML file.

### Testing the Application

1. Run the `App` class to observe the setter injection in action.
2. Check the output, which should include "Coding..." and "Age: 21".

With this, we've successfully implemented setter injection in Spring, enhancing the configurability of our beans. In the next chapters, we'll explore more advanced features and injection techniques.

# Chapter 11 (Continued): Setter Injection for Object References

In the previous section, we explored setter injection for primitive variables. Now, let's extend our understanding to setter injection for object references using the example of the `Laptop` class.

## Modifying Alien and Laptop Classes

Firstly, let's introduce a method `compile` in the `Laptop` class, simulating a functionality related to programming.

```java
package com.telusko.Spring1;

public class Laptop {
    public void compile() {
        System.out.println("Compiling...");
    }

    // Getter and setter methods
}
```

Now, let's modify the `Alien` class to include a `Laptop` reference.

```java
package com.telusko.Spring1;

public class Alien {
    private int age;
    private Laptop lap;

    // Constructor, getter, and setter methods for age and lap

    public void code() {
        System.out.println("Coding...");
        lap.compile(); // Calling compile method from the Laptop object
    }
}
```

Ensure that you generate the necessary getter and setter methods for the `lap` variable.

## Setter Injection in Spring XML

Now, let's update the `spring.xml` file to include the necessary configurations for setter injection of the `lap` property.

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <!-- Setter Injection for age property -->
    <property name="age" value="21"/>
    <!-- Setter Injection for lap property (Object Reference) -->
    <property name="lap" ref="lap1"/>
</bean>

<bean id="lap1" class="com.telusko.Spring1.Laptop"/>
<bean id="lap2" class="com.telusko.Spring1.Laptop"/>
```

Here:
- We introduce a property named `lap` in the `Alien` bean configuration.
- The `ref` attribute is used to inject a reference to the `Laptop` bean with the ID `lap1`.
- We also create two `Laptop` beans with distinct IDs (`lap1` and `lap2`).

## Understanding Setter Injection with Object References

1. **Private Variable and Setter Method:** Ensure that the object reference variable (`lap`) in the `Alien` class is private and has a corresponding setter method.

2. **Configure in Spring XML:** In the `spring.xml` file, use the `<property>` tag to configure the setter injection for the object reference.
   - The `name` attribute specifies the property name (`lap`).
   - The `ref` attribute injects the reference to the desired bean (`lap1` in this case).

3. **Bean Dependency Management:** The dependency between the `Alien` and `Laptop` beans is managed by the Spring container.

4. **Testing the Application:** Run the application and observe the output, which should include both "Coding..." and "Compiling..." messages.

By following these steps, we have successfully implemented setter injection for object references. The Spring framework ensures that the appropriate objects are injected, allowing for clean separation of concerns and improved modularity in your application. In the next chapters, we will explore more advanced features and injection techniques offered by Spring.

# Chapter 12: Constructor Injection in Spring

In this chapter, we will delve into constructor injection in the context of the Spring framework. Constructor injection is an alternative method to set properties in a class, particularly useful when initializing values upon object creation. Let's explore this concept using the `Alien` class and introduce various approaches to constructor injection.

## Basic Concept of Constructor Injection

In the `Alien` class, we have two properties: `age` and `lap` (Laptop). Traditionally, values for these properties are assigned using setter methods, especially when configuring Spring beans in an XML file.

```java
public class Alien {
    private int age;
    private Laptop lap;

    // Constructors, getter, and setter methods
}
```

### Using Setter Methods

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <property name="age" value="21"/>
    <property name="lap" ref="lap1"/>
</bean>

<bean id="lap1" class="com.telusko.Spring1.Laptop"/>
```

In this scenario, values are set using setter methods (`setAge` and `setLap`) via the Spring XML configuration.

## Introducing Constructor Injection

### Parameterized Constructor

Now, let's explore constructor injection. We can create a parameterized constructor in the `Alien` class to directly initialize properties upon object creation.

```java
public class Alien {
    private int age;
    private Laptop lap;

    // Default constructor
    public Alien() {
        System.out.println("Default Constructor Called");
    }

    // Parameterized constructor
    public Alien(int age) {
        this.age = age;
        System.out.println("Para Constructor Called");
    }

    // Parameterized constructor with multiple parameters
    public Alien(int age, Laptop lap) {
        this.age = age;
        this.lap = lap;
        System.out.println("Para Constructor Called");
    }

    // Other methods, getter, and setter methods
}
```

### Spring XML Configuration for Constructor Injection

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <!-- Constructor Injection with a single parameter -->
    <constructor-arg value="21"/>

    <!-- Constructor Injection with multiple parameters -->
    <constructor-arg value="21"/>
    <constructor-arg ref="lap1"/>
</bean>

<bean id="lap1" class="com.telusko.Spring1.Laptop"/>
```

Here, the `<constructor-arg>` tag is used to inject values into the constructor. For the second example with multiple parameters, we also create a `Laptop` bean (`lap1`) to be injected.

### Handling Multiple Parameters

```java
public class Alien {
    private int age;
    private Laptop lap;
    private int salary;

    // Parameterized constructor with multiple parameters
    public Alien(int age, Laptop lap, int salary) {
        this.age = age;
        this.lap = lap;
        this.salary = salary;
        System.out.println("Para Constructor Called");
    }

    // Other methods, getter, and setter methods
}
```

#### **Solution 1: Using Type**

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <constructor-arg type="int" value="21"/>
    <constructor-arg ref="lap1"/>
    <constructor-arg type="int" value="50000"/>
</bean>
```

#### **Solution 2: Using Index**

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <constructor-arg index="0" value="21"/>
    <constructor-arg index="1" ref="lap1"/>
    <constructor-arg index="2" value="50000"/>
</bean>
```

#### **Solution 3: Using Name**

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <constructor-arg name="age" value="21"/>
    <constructor-arg name="lap" ref="lap1"/>
    <constructor-arg name="salary" value="50000"/>
</bean>
```

### Handling Ambiguity in Parameters

```java
public class Alien {
    private int age;
    private Laptop lap;
    private int salary;

    // Parameterized constructor with multiple parameters
    public Alien(int age, Laptop lap, int salary) {
        this.age = age;
        this.lap = lap;
        this.salary = salary;
        System.out.println("Para Constructor Called");
    }

    // Other methods, getter, and setter methods
}
```

#### **Solution: Using Index Numbers**

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <constructor-arg index="0" value="21"/>
    <constructor-arg index="1" ref="lap1"/>
    <constructor-arg index="2" value="50000"/>
</bean>
```

### Handling Constructor Ambiguity with `ConstructorProperties`

```java
import java.beans.ConstructorProperties;

public class Alien {
    private int age;
    private Laptop lap;
    private int salary;

    @ConstructorProperties({"age", "lap", "salary"})
    public Alien(int age, Laptop lap, int salary) {
        this.age = age;
        this.lap = lap;
        this.salary = salary;
        System.out.println("Para Constructor Called");
    }

    // Other methods, getter, and setter methods
}
```

#### **Solution: Using `ConstructorProperties`**

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <constructor-arg value="21"/>
    <constructor-arg ref="lap1"/>
    <constructor-arg value="50000"/>
</bean>
```

## Conclusion

Constructor injection is a powerful feature in Spring for initializing class properties during object creation. It offers alternatives to setter injection, providing more control over the initialization process. Choose between type, index, name, or `ConstructorProperties` based on your specific requirements. If you have optional properties, setter injection might be more suitable; otherwise, constructor injection is the preferred choice for mandatory properties.

# Chapter 13: Advanced Dependency Injection in Spring

In this chapter, we will explore advanced dependency injection concepts in Spring, specifically focusing on autowiring and resolving issues related to multiple bean implementations.

## Recap: Interface and Implementations

Previously, we created an interface (`Computer`) and two implementations (`Laptop` and `Desktop`). Now, let's revisit the code and run it to check if everything is functioning as expected.

```java
public interface Computer {
    void compile();
}

public class Laptop implements Computer {
    public void compile() {
        System.out.println("Compiling using Laptop.");
    }
}

public class Desktop implements Computer {
    public void compile() {
        System.out.println("Compiling using Desktop.");
    }
}
```

### Spring Configuration with Property Injection

```xml
<bean id="alien" class="com.telusko.Spring1.Alien">
    <property name="age" value="21"/>
    <property name="lap" ref="com1"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

## Issues with Property Injection

Upon running the code, an error is encountered: "Invalid property 'lap' for the bean."

### Addressing the Issue with Autowiring

To address this issue and make the wiring between `Alien` and `Computer` automatic, we can use the `autowire` attribute in the XML configuration.

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byName">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

Now, the property `lap` in the `Alien` class is automatically wired to the corresponding bean (`com1` in this case) based on its name.

## Autowiring byName and byType

### Autowiring byName

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byName">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

Here, autowiring is done based on the name of the bean. If the names match, the dependency is resolved.

### Autowiring byType

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

Autowiring is done based on the type of the property. If the type matches, the dependency is resolved.

## Resolving Conflicts with Multiple Implementations

### Issue: Conflicting Implementations

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

In this scenario, Spring encounters an error: "Expected single matching bean but found 2."

### Resolving Conflicts with Autowiring

#### Autowiring byType and byName Together

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType, byName">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

By specifying both `byType` and `byName`, Spring resolves the ambiguity by considering both type and name for autowiring.

# Chapter 13: Advanced Dependency Injection in Spring (Continued)

In the previous section, we explored autowiring in Spring and encountered issues when multiple implementations of a bean exist. To address such conflicts, we introduced the concept of primary beans.

## Primary Beans to Resolve Conflicts

Consider a scenario where you have multiple implementations of the `Computer` interface – `Laptop` and `Desktop`. By default, when using autowiring by type, Spring may encounter ambiguity. To resolve this, we can designate one of the beans as the primary bean.

### Example: Database Connection

Imagine a database connection scenario where you have a primary connection and a backup connection. By marking one connection as primary, you indicate a preference for the primary connection over others.

### Using Primary Attribute

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop" primary="true"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

In this example, the `Laptop` bean is marked as the primary bean by setting the `primary` attribute to `true`.

### Primary Bean and Explicit Naming

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType">
    <property name="age" value="21"/>
    <property name="lap" ref="com2"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop" primary="true"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

Even if a bean is marked as primary, if you explicitly mention the bean by name (e.g., `com2`), Spring will prioritize the explicitly mentioned bean over the primary bean.

### Primary Bean and Autowiring

```xml
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType">
    <property name="age" value="21"/>
</bean>

<bean id="com1" class="com.telusko.Spring1.Laptop" primary="true"/>
<bean id="com2" class="com.telusko.Spring1.Desktop"/>
```

If autowiring is set by type and there is confusion between multiple implementations, Spring will prioritize the primary bean.

# Chapter 13: Advanced Dependency Injection in Spring (Continued)

In the previous sections, we discussed primary beans and how they can be used to resolve conflicts when multiple implementations of a bean exist. Now, let's delve into the concept of lazy initialization for beans in a Spring application.

## Lazy Initialization of Beans

In a typical Spring application, beans are eagerly initialized by default. This means that when the Spring container loads, it creates instances of all singleton beans, even if they are not immediately used. However, there might be scenarios where you want to delay the creation of certain beans until they are explicitly requested.

### Example Scenario

Consider an application with three beans: `Alien`, `Laptop`, and `Desktop`. By default, all these beans are eagerly initialized because their scope is set to singleton. Now, imagine a situation where you want the `Desktop` bean to be created only when it is explicitly requested, not during the initial application startup.

### Enabling Lazy Initialization

To achieve lazy initialization, you can simply set the `lazy-init` attribute to `true` in the bean definition.

#### Example: Enabling Lazy Initialization for `Desktop`

```xml
<bean id="com2" class="com.telusko.Spring1.Desktop" lazy-init="true"/>
```

With this configuration, the `Desktop` bean will not be created during the initial application startup. Instead, it will be created only when it is explicitly requested.

### Demonstration

In the example, we modified the `Desktop` bean to use lazy initialization. When the Spring container is started, the `Desktop` object is not created immediately. Only when we explicitly request the `Desktop` bean using the `getBean` method, it gets instantiated.

#### Lazy Initialization in Action

```java
Desktop desktopObj = (Desktop) context.getBean("com2");
```

By using lazy initialization, you can optimize the startup time of your application, especially when dealing with a large number of beans. Beans marked as lazy are only created when they are actually needed, providing a more efficient resource utilization strategy.

### Lazy Bean as a Dependency

Moreover, if a lazy bean is a dependency for another non-lazy (eager) bean, Spring will still create the lazy bean when the eager bean is initialized. This ensures that all dependencies are satisfied, even if some beans are marked for lazy initialization.

```java
<bean id="alien" class="com.telusko.Spring1.Alien" autowire="byType">
    <property name="com" ref="com2"/> <!-- com2 is a lazy bean -->
</bean>
```

Even though `Desktop` is marked as lazy, it will be created if an eager bean, such as `Alien`, depends on it.

# Chapter 13: Advanced Dependency Injection in Spring (Continued)

## Specifying Class Type for Bean Retrieval

In this section, we will explore how to specify the class type when retrieving beans from the Spring container. By default, the `getBean` method returns an object of type `Object`, and you need to perform type casting to get the desired class type. However, Spring provides an alternative method that allows you to retrieve beans directly with the desired class type.

### Retrieving Beans by Specifying Class Type

In the traditional approach, when you retrieve a bean using `getBean`:

```java
Alien alienObj = (Alien) context.getBean("alien");
```

You need to perform type casting because the returned object is of type `Object`. To simplify this process, you can specify the class type directly in the `getBean` method:

```java
Alien alienObj = context.getBean("alien", Alien.class);
```

This way, you avoid the need for type casting, and the object returned is already of the specified class type.

### Example: Specifying Class Type for Desktop Bean

Let's consider the `Desktop` bean and how you can retrieve it by specifying the class type:

```java
Desktop desktopObj = context.getBean("com2", Desktop.class);
```

Here, we are explicitly mentioning that we want an object of type `Desktop`. Similarly, this can be done for the `Alien` bean:

```java
Alien alienObj = context.getBean(Alien.class);
```

This syntax can be used when you don't want to specify the name of the bean and prefer searching by type.

### Consideration for Interface Types

When dealing with interface types, such as the `Computer` interface, you can use the class type for retrieval:

```java
Computer comObj = context.getBean(Computer.class);
```

However, if there are multiple implementations of the interface, you might encounter issues. In such cases, the `@Primary` annotation or setting a primary bean can help resolve conflicts. Alternatively, you can use the `getBean` method with the bean name.

### Bean Retrieval without Specifying Bean Name

If you don't specify the bean name and rely on searching by type, make sure that the bean you are requesting has a unique implementation. If there are multiple beans of the same type, Spring might not be able to determine which one to return.

### Conclusion

By specifying the class type when retrieving beans, you can enhance code readability and avoid type casting. It provides a cleaner and more straightforward approach to obtaining beans from the Spring container. Consider the context of your application and choose the most suitable method for bean retrieval based on your specific requirements and dependencies.

# Chapter 13: Advanced Dependency Injection in Spring (Continued)

## Inner Beans in Spring

In this section, we'll explore the concept of inner beans in Spring, a feature that allows you to define a bean within the scope of another bean.

### Need for Inner Beans

Consider a scenario where you have a dependency that is used only by a specific bean and is not intended for external use. In such cases, creating an inner bean helps encapsulate the dependency within the scope of the outer bean, promoting better encapsulation and limiting the accessibility of the inner bean.

### Implementing Inner Beans

Let's modify the existing example to demonstrate the use of inner beans. We have an `Alien` bean that depends on a `Computer`. For simplicity, let's focus on using only a `Laptop` bean as the implementation of `Computer`.

#### Original Configuration (Desktop as External Bean)

Previously, the configuration looked like this:

```xml
<bean id="laptop" class="com.example.Laptop">
    <!-- Laptop properties configuration -->
</bean>

<bean id="alien" class="com.example.Alien">
    <property name="com" ref="laptop" />
</bean>
```

Here, the `Laptop` bean is defined separately and is accessible globally.

#### Inner Bean Configuration

Now, let's modify it to use an inner bean:

```xml
<bean id="alien" class="com.example.Alien">
    <property name="com">
        <bean class="com.example.Laptop">
            <!-- Laptop properties configuration -->
        </bean>
    </property>
</bean>
```

In this configuration, the `Laptop` bean is defined directly within the `Alien` bean's property. This makes it an inner bean, accessible only within the scope of the `Alien` bean.

### Benefits of Inner Beans

1. **Encapsulation:** The `Laptop` bean is encapsulated within the `Alien` bean, limiting its visibility to the outside world.

2. **Scope Limitation:** The `Laptop` bean is specific to the `Alien` bean and cannot be referenced or used globally.

3. **Simplified Configuration:** Inner beans are defined inline, leading to a more concise and readable configuration, especially when the bean is used only within a specific context.

### Conclusion

The concept of inner beans in Spring provides a way to structure your bean configuration with a focus on encapsulation and scope limitation. It's a useful feature when you want to create beans that are closely tied to and used only within the context of another bean. As you continue to design and implement Spring applications, consider the appropriate use of inner beans based on the encapsulation requirements of your components.


# **Next Section: Java Based Config**
# Chapter 1: Introduction to Java-Based Configuration in Spring

## Overview:
In this chapter, we explore the transition from XML configuration to Java-based configuration in a Spring project. The XML configuration was handled in a file named `spring.xml`. However, Java-based configuration provides an alternative for those who are not fond of XML. We create a Java class called `AppConfig` to handle the configuration in a package named `config`. This chapter covers the steps involved in transitioning and configuring a simple bean using Java-based configuration.

## Steps:

### 1. Create a New Package and Java Class for Configuration:
- **Create a new package called `config`:**
  - Right-click on the project, choose "New," and then select "Package."
  - Name the package as `config`.

- **Create a new Java class called `AppConfig`:**
  - Right-click on the `config` package.
  - Choose "New" and then select "Java Class."
  - Name the class as `AppConfig`.

Example Code:
```java
package config;

public class AppConfig {
    // Configuration code will be added here
}
```

### 2. Modify the Main Application Class (`App.java`):
- **Comment out existing XML-based configuration:**
  - Comment out the XML-based configuration in the `App.java` file.

- **Create an instance of `AnnotationConfigApplicationContext`:**
  - Use the `AnnotationConfigApplicationContext` class for Java-based configuration.
  - Pass the configuration class (`AppConfig.class`) as a parameter.

Example Code:
```java
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App {
    public static void main(String[] args) {
        // Comment out XML configuration

        // Create context with Java-based configuration
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        // Object creation and method invocation will be added here

        // Close the context
        context.close();
    }
}
```

### 3. Create a Bean Using Java-Based Configuration:
- **Add `@Configuration` annotation:**
  - Annotate the `AppConfig` class with `@Configuration` to indicate it contains Spring bean configurations.

- **Add `@Bean` annotation:**
  - Use `@Bean` annotation for methods that define beans.
  - Specify the bean type and return the bean instance.

Example Code:
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
    @Bean
    public Desktop desktop() {
        return new Desktop();
    }
}
```

### 4. Test the Java-Based Configuration:
- **Retrieve and use the bean in the main application:**
  - Use the `getBean` method on the context to retrieve the bean instance.
  - Demonstrate calling a method on the bean.

Example Code:
```java
public class App {
    public static void main(String[] args) {
        // Create context with Java-based configuration
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        // Retrieve bean and invoke method
        Desktop desktop = context.getBean(Desktop.class);
        desktop.compile(); // Example method invocation

        // Close the context
        context.close();
    }
}
```

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Understanding Bean Names and Defaults:

### 1. Bean Naming in Java-Based Configuration:
- When requesting a bean, we usually specify its type but not its name.
- Unlike XML configuration, the default name for a bean in Java-based configuration is the method name.
- If the method name is `desktop`, the default bean name is also `desktop`.
- Attempting to use a specific name without mentioning it in the configuration results in an error.

Example Code:
```java
public class App {
    public static void main(String[] args) {
        // Attempting to use a specific bean name
        Desktop desktop = context.getBean("com2", Desktop.class); // Results in an error
    }
}
```

### 2. Using Default Bean Names:
- By default, the bean name is the same as the method name.
- To use the default name, simply specify the method name when requesting the bean.

Example Code:
```java
public class App {
    public static void main(String[] args) {
        // Using the default bean name (method name)
        Desktop desktop = context.getBean("desktop", Desktop.class);
    }
}
```

### 3. Changing Bean Name:
- To change the bean name, add the `name` attribute to the `@Bean` annotation in the configuration class.
- Specify the desired name within double quotes.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean(name = "com2")
    public Desktop desktop() {
        return new Desktop();
    }
}
```

### 4. Using Multiple Bean Names:
- It's possible to assign multiple names to a bean using the `name` attribute in the configuration class.
- Separate each name with a comma.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean(name = {"com2", "desktop1", "Beast"})
    public Desktop desktop() {
        return new Desktop();
    }
}
```

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Customizing Bean Scopes:

### 1. Default Singleton Scope:
- By default, every bean in Spring is a singleton.
- Singleton beans have a single instance per container, created when the application loads.

### 2. Prototype Scope:
- Prototype scope allows the creation of a new instance for each request.
- To configure a bean with the prototype scope, use the `@Scope` annotation with the `ConfigurableBeanFactory.SCOPE_PROTOTYPE` value.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean(name = "desktop", scope = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public Desktop desktop() {
        return new Desktop();
    }
}
```

### 3. Demonstration of Prototype Scope:
- In the main application (`App.java`), requesting the same bean multiple times results in the creation of distinct objects.
- The `@Scope` annotation is used in Java-based configuration to achieve prototype scope.
- When running the application, the output should indicate that the object is created multiple times.

Example Code:
```java
public class App {
    public static void main(String[] args) {
        // Requesting the bean multiple times
        Desktop desktop1 = context.getBean(Desktop.class);
        Desktop desktop2 = context.getBean(Desktop.class);

        // Output should indicate that the object is created multiple times
        // "Desktop object created"
    }
}
```

### 4. Annotation-Based Configuration for Scope:
- Use the `@Scope` annotation directly on the `@Bean` method to set the bean scope.
- The value should be set to `ConfigurableBeanFactory.SCOPE_PROTOTYPE`.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean(name = "desktop")
    @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public Desktop desktop() {
        return new Desktop();
    }
}
```

### 5. Summary:
- Understanding default singleton scope and prototype scope in Spring.
- Configuring prototype scope using `@Scope` annotation.
- Demonstrating the creation of distinct objects for beans with prototype scope.

## Making a Bean Primary:

### 1. Introduction to Primary Beans:
- In scenarios where multiple beans of the same type exist, marking one as primary helps Spring resolve ambiguity.
- Use the `@Primary` annotation on the preferred bean.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean(name = "desktop", scope = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public Desktop desktop() {
        return new Desktop();
    }

    @Bean(name = "laptop")
    @Primary
    public Laptop laptop() {
        return new Laptop();
    }
}
```

### 2. Demonstration of Primary Beans:
- When autowiring a bean of the same type, the primary bean is selected.
- Use `@Autowired` to demonstrate autowiring.

Example Code:
```java
public class AnotherClass {
    @Autowired
    private Laptop laptop; // Autowire the Laptop bean, marked as @Primary
}
```

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Autowiring Dependencies in Java-Based Configuration:

### 1. Creating a Bean for the Alien Object:
- To work with the `alien` object, create a bean for it in the `AppConfig` class.
- Demonstrate the creation of a new bean and setting an initial age value.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean
    public Alien alien() {
        Alien obj = new Alien();
        obj.setAge(25); // Set the initial age value
        return obj;
    }
}
```

### 2. Establishing Dependency between Alien and Computer:
- The `alien` object has a dependency on the `computer` object.
- Use the `setCom` method to set the computer object, which is implemented by `Desktop` in this example.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean
    public Alien alien(Computer com) {
        Alien obj = new Alien();
        obj.setCom(com); // Set the computer object (com) as a dependency
        obj.setAge(25); // Set the initial age value
        return obj;
    }
}
```

### 3. Introduction to Autowiring:
- Autowiring eliminates the need to explicitly provide dependencies.
- Spring automatically resolves dependencies by examining the container.

### 4. Demonstrating Autowiring in Java-Based Configuration:
- Remove the specific bean creation (`obj.setCom(new Desktop());`) and allow Spring to autowire the dependency.
- Use the `@Autowired` annotation on the constructor or setter method to enable autowiring.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean
    public Alien alien(Computer com) {
        Alien obj = new Alien();
        obj.setCom(com); // Autowire the dependency (computer object)
        obj.setAge(25); // Set the initial age value
        return obj;
    }
}
```

### 5. Resolving Multiple Beans of the Same Type:
- When multiple beans of the same type exist, Spring needs a way to determine which one to inject.
- Use the `@Primary` annotation on the preferred bean to specify the primary candidate.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean
    @Primary
    public Computer desktop() {
        return new Desktop();
    }

    @Bean
    public Computer laptop() {
        return new Laptop();
    }

    @Bean
    public Alien alien(@Autowired Computer com) {
        Alien obj = new Alien();
        obj.setCom(com); // Autowire the dependency (computer object)
        obj.setAge(25); // Set the initial age value
        return obj;
    }
}
```

### 6. Summary:
- Creating a bean for the `alien` object and establishing a dependency on the `computer` object.
- Demonstrating autowiring by allowing Spring to resolve dependencies automatically.
- Introducing the `@Autowired` annotation to enable autowiring.
- Resolving conflicts in autowiring when multiple beans of the same type exist using the `@Primary` annotation.

## Next Steps:
- The next chapter will delve into annotation-based configuration, providing an alternative way to configure Spring beans.

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Resolving Bean Name Conflicts:

### 1. Introduction:
- When there are multiple beans of the same type, Spring needs guidance on which one to inject.
- Discussing the problem of having two beans (`Desktop` and `Laptop`) causing a conflict.

### 2. Using Qualifier Annotation:
- In Java-based configuration, use the `@Qualifier` annotation to specify the name of the desired bean.
- The `@Qualifier` annotation is equivalent to specifying the bean name in XML configuration using the `ref` attribute.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean
    @Qualifier("desktop") // Specify the name of the bean to resolve ambiguity
    public Computer desktop() {
        return new Desktop();
    }

    @Bean
    @Qualifier("laptop") // Specify the name of the bean to resolve ambiguity
    public Computer laptop() {
        return new Laptop();
    }

    @Bean
    public Alien alien(@Autowired @Qualifier("desktop") Computer com) {
        Alien obj = new Alien();
        obj.setCom(com);
        obj.setAge(25);
        return obj;
    }
}
```

### 3. Using Primary Annotation:
- The `@Primary` annotation can be used to mark a preferred bean when multiple beans of the same type exist.
- This is similar to the `primary` attribute in XML configuration.

Example Code:
```java
@Configuration
public class AppConfig {
    @Bean
    @Primary // Marking Desktop as the primary bean
    public Computer desktop() {
        return new Desktop();
    }

    @Bean
    public Computer laptop() {
        return new Laptop();
    }

    @Bean
    public Alien alien(@Autowired Computer com) {
        Alien obj = new Alien();
        obj.setCom(com);
        obj.setAge(25);
        return obj;
    }
}
```

### 4. Summary:
- Explaining the problem of having multiple beans of the same type causing conflicts.
- Introducing the `@Qualifier` annotation to specify the name of the desired bean.
- Demonstrating the use of the `@Primary` annotation to mark a preferred bean.

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Simplifying Bean Management with Stereotype Annotations:

### 1. Introduction:
- Desire for simpler bean management in Spring.
- Introducing `@Component` and `@ComponentScan` annotations.

### 2. Using `@Component` Annotation:
- `@Component` marks a class for Spring bean management.
- Example Code:
    ```java
    @Component
    public class Alien {
        // Alien class definition
    }

    @Component
    public class Desktop implements Computer {
        // Desktop class definition
    }

    @Component
    public class Laptop implements Computer {
        // Laptop class definition
    }
    ```

### 3. Using `@ComponentScan` Annotation:
- `@ComponentScan` enables Spring to scan specified packages.
- Ensures automatic bean detection and management.
- Example Code in App.java:
    ```java
    @SpringBootApplication
    public class App {
        public static void main(String[] args) {
            SpringApplication.run(App.class, args);
        }
    }
    ```

## Enhancing Object Configuration:

### 1. Introduction:
- Desire for more automatic object management.
- Exploring how to connect Spring-managed beans.

### 2. Autowiring and Connecting Beans:
- Introducing `@Autowired` to establish connections between beans.
- Example Code in AppConfig.java:
    ```java
    @Component
    public class Alien {
        private Computer com;

        @Autowired
        public Alien(Computer com) {
            this.com = com;
        }

        // Alien class definition
    }

    @Component
    public class Desktop implements Computer {
        // Desktop class definition
    }

    @Component
    public class Laptop implements Computer {
        // Laptop class definition
    }
    ```
- Demonstration of autowiring connections between `Alien` and `Computer` beans.

### 3. Summary:
- Stereotype annotations (`@Component`, `@ComponentScan`) simplify bean management.
- Autowiring (`@Autowired`) enhances automatic bean connections.

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Enhancing Object Configuration:

### 4. Addressing Autowiring Issues:

#### a. Handling Default Values:
- Default values for `age` and `com` in `Alien` class.
- Setting default `age` to 0.
- Using `@Autowired` to connect `com` with the Spring-managed bean.

#### b. Resolving Bean Name Conflicts:
- Conflicts arise when multiple beans implement the same interface.
- Using `@Qualifier` to specify the desired bean name.
    ```java
    @Component
    public class Alien {
        @Autowired
        @Qualifier("laptop")
        private Computer com;

        // Alien class definition
    }

    @Component("desktop")
    public class Desktop implements Computer {
        // Desktop class definition
    }

    @Component("laptop")
    public class Laptop implements Computer {
        // Laptop class definition
    }
    ```

#### c. Utilizing Primary Annotation:
- Resolving conflicts using the `@Primary` annotation.
- Applying `@Primary` to the preferred bean.
    ```java
    @Component("desktop")
    @Primary
    public class Desktop implements Computer {
        // Desktop class definition
    }
    ```

#### d. Autowiring Injection Types:
- Three injection types: Field, Constructor, Setter.
- Demonstrating field injection with `@Autowired`.
- Suggesting setter injection as a preferred approach.

### 5. Conclusion:
- Improved bean management with `@Autowired`, `@Qualifier`, and `@Primary`.
- Injection options: Field, Constructor, Setter.
- Further exploration of bean scope in the next video.

# Chapter 1 (Continued): Java-Based Configuration in Spring

## Enhancing Object Configuration:

### 6. Managing Bean Scope and Injecting Values:

#### a. Configuring Bean Scope with @Scope:
- Utilizing the `@Scope` annotation to specify the bean scope.
- Example: Changing the scope to prototype.
    ```java
    @Component("desktop")
    @Scope("prototype")
    public class Desktop implements Computer {
        // Desktop class definition
    }
    ```

#### b. Injecting Values Using @Value:
- Using the `@Value` annotation for injecting values into fields.
- Example: Injecting a value for the `age` field.
    ```java
    @Component
    public class Alien {
        @Autowired
        @Qualifier("laptop")
        private Computer com;

        @Value("21")
        private int age;

        // Alien class definition
    }
    ```
- `@Value` allows injecting values from external sources, such as property files.

### 7. Conclusion:
- Customizing bean behavior with `@Scope` for managing bean scopes.
- Injecting values into fields using `@Value`.
- Preparing for further exploration of advanced Spring features.

#**Next Section: SpringBoot**
# Chapter 1: Working with Spring Annotations and Spring Boot

## Overview:

Till this point in our Spring Core Project, we've explored various annotations and built a project without utilizing Spring Boot. We've employed both XML configuration and Java-based configuration, utilizing annotations such as `@Component` for component scanning.

### Transition to Spring Boot:

Our journey began with Spring Boot, and the transition is apparent in the project structure. Unlike the Spring Core Project, Spring Boot requires minimal configuration. The presence of the `@SpringBootApplication` annotation signifies that we are dealing with a Spring Boot application. Spring Boot relies on conventions and automates many configurations behind the scenes.

## Recap of Spring Core Project:

In our Spring Core Project, we extensively used Java-based configuration, defined beans, and addressed dependency injection issues using annotations like `@Primary` and `@Qualifier`. Two classes, `Desktop` and `Laptop`, implemented the `Computer` interface, resolving ambiguity in component wiring. Additionally, we introduced the `@Value` annotation to inject values into fields.

## Translating Spring Core Project to Spring Boot:

### 1. Spring Boot Structure:
   - Spring Boot projects follow a simplified structure with minimal configuration files.
   - Notable absence of extensive Java-based or XML configurations.

### 2. Spring Boot Opinionated Approach:
   - Spring Boot adopts an opinionated approach, making assumptions about project structure and configuration.
   - The `@SpringBootApplication` annotation serves as an entry point, indicating a Spring Boot application.

### 3. Component Scanning in Spring Boot:
   - Spring Boot automatically scans components in the same package as the main application class.
   - Annotations like `@Component` are implicitly understood by Spring Boot.

### 4. Solving Dependency Injection:
   - Spring Boot leverages the same dependency injection mechanisms as the Spring Core Project.
   - The `@Autowired` annotation is used to wire dependencies.

### 5. Transitioning and Future Work:
   - We will introduce additional variables and methods in the Spring Boot project.
   - Considerations for component ambiguity, such as `@Primary` and `@Qualifier`, are still applicable.

## Conclusion:

- Spring Boot simplifies the development process by providing defaults and conventions.
- Transitioning from Spring Core to Spring Boot involves leveraging Spring Boot's opinionated approach.
- The fundamental concepts of dependency injection and component management remain consistent between the two paradigms.

In the next sections, we will further enhance our Spring Boot project by introducing variables, methods, and addressing additional considerations.

# Chapter 2: Transitioning to Spring Boot

## Overview:

In this chapter, we'll enhance our Spring Boot project by introducing new classes and making necessary modifications to existing ones. The goal is to align our Spring Boot project with the structure and functionality of the Spring Core Project.

### Changes in Alien Class:

1. **Adding Variables:**
   - Introduced a private integer variable for age.
   - Changed the `laptop` variable to `computer` to accommodate both `Laptop` and `Desktop` implementations.

```java
@Component
public class Alien {
    @Value("${alien.age:25}")
    private int age;

    private Computer com;

    // Getter and Setter for Age
    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    // Setter for Computer with Autowired
    @Autowired
    public void setCom(Computer com) {
        this.com = com;
    }

    public void code() {
        com.compile();
        System.out.println("Age: " + age);
    }
}
```

### Introduction of Computer Interface:

2. **Computer Interface:**
   - Created a new `Computer` interface with a `compile` method, which both `Laptop` and `Desktop` will implement.

```java
public interface Computer {
    void compile();
}
```

### Implementation Classes: Laptop and Desktop:

3. **Implementation Classes:**
   - Introduced two classes, `Laptop` and `Desktop`, implementing the `Computer` interface.

```java
@Component("laptop")
public class Laptop implements Computer {
    @Override
    public void compile() {
        System.out.println("Compiling in Laptop");
    }
}

@Component("desktop")
public class Desktop implements Computer {
    @Override
    public void compile() {
        System.out.println("Compiling in Desktop");
    }
}
```

### Handling Ambiguity:

4. **Handling Ambiguity:**
   - Utilized `@Primary` on the `Desktop` class to make it the primary bean.
   - Used `@Qualifier("laptop")` on the setter for `Computer` in the `Alien` class to specify which bean to inject.

```java
@Component("desktop")
@Primary
public class Desktop implements Computer {
    // Implementation remains unchanged
}

@Component("laptop")
public class Laptop implements Computer {
    // Implementation remains unchanged
}
```

### Running the Spring Boot Application:

5. **Running the Application:**
   - Executed the Spring Boot application to observe the behavior.

## Conclusion:

- The transition to Spring Boot involves minimal configuration.
- Introduced new classes and modified existing ones to align with Spring Boot conventions.
- Leveraged annotations like `@Value`, `@Autowired`, `@Primary`, and `@Qualifier`.
- The simplicity of Spring Boot allows for a smoother development experience.

In the subsequent chapters, we will delve deeper into Spring Boot features, exploring additional annotations and functionalities.

# Chapter 2 Continued: Implementing Layered Architecture

## Overview:

In this section, we'll explore the concept of layered architecture in a Spring Boot application. The typical layers include the client layer (or controller layer), service layer, and repository layer. We'll discuss the responsibilities of each layer and use appropriate annotations to create the corresponding classes.

### Layered Architecture:

1. **Client/Controller Layer:**
   - Represents the initial point of interaction with the application.
   - Sends requests to the service layer.

2. **Service Layer:**
   - Responsible for business logic and processing.
   - Communicates with the repository layer to fetch or persist data.
   - Does not interact directly with the database.

3. **Repository Layer:**
   - Manages the interaction with the database.
   - Retrieves or stores data based on the service layer's requests.

### Implementation:

Let's implement the layered architecture for our Spring Boot application.

### Creating Service Interface:

1. **Service Interface:**
   - Define a service interface to declare the methods for processing logic.

```java
public interface LaptopService {
    List<String> getAllLaptops();
}
```

### Implementing Service:

2. **Service Implementation:**
   - Create a service class implementing the service interface.
   - Add the `@Service` annotation to indicate that it's a service component.

```java
@Service
public class LaptopServiceImpl implements LaptopService {
    @Autowired
    private LaptopRepository laptopRepository;

    @Override
    public List<String> getAllLaptops() {
        return laptopRepository.getAllLaptops();
    }
}
```

### Creating Repository Interface:

3. **Repository Interface:**
   - Define a repository interface to declare methods for database interaction.

```java
public interface LaptopRepository {
    List<String> getAllLaptops();
}
```

### Implementing Repository:

4. **Repository Implementation:**
   - Create a repository class implementing the repository interface.
   - Add the `@Repository` annotation to indicate that it's a repository component.

```java
@Repository
public class LaptopRepositoryImpl implements LaptopRepository {
    @Override
    public List<String> getAllLaptops() {
        // Simulate database interaction, actual logic will vary
        return Arrays.asList("Laptop A", "Laptop B", "Laptop C");
    }
}
```

### Injecting Service in Controller:

5. **Controller Modification:**
   - Inject the service into the controller to handle client requests.

```java
@RestController
@RequestMapping("/laptops")
public class LaptopController {
    @Autowired
    private LaptopService laptopService;

    @GetMapping
    public List<String> getAllLaptops() {
        return laptopService.getAllLaptops();
    }
}
```

### Testing the Application:

6. **Testing the Layers:**
   - Run the application and test the endpoint to see the interaction between the layers.

```java
@SpringBootApplication
public class LayeredApplication {
    public static void main(String[] args) {
        SpringApplication.run(LayeredApplication.class, args);
    }
}
```

Now, the application has a structured layered architecture with the client, service, and repository components interacting seamlessly. In the next chapter, we'll explore more advanced features of Spring Boot and how it simplifies various aspects of application development


#**Next Section: Spring JDBC**
# Chapter 1: Introduction to Spring JDBC

## Overview:

In this module, we will explore the significance of Spring JDBC in simplifying database connectivity for Java applications. The discussion emphasizes the essential role of databases, even in applications with minimal data interactions. While JDBC is the conventional approach for database connectivity, its manual steps and complexities prompt the need for a more efficient solution—Spring JDBC.

### Importance of Databases:

- **Central Role:**
  - Databases play a crucial role in applications, storing and managing data.
  - Most applications rely heavily on data for their functionality.

- **Connectivity Requirement:**
  - Regardless of an application's data-centric nature, some form of data storage is always necessary.
  - Connection with a database becomes essential for data handling.

### Challenges with Traditional JDBC:

- **Manual Steps:**
  - Traditional JDBC involves writing numerous manual steps for database interactions.
  - Developers need to manage various aspects, including opening and closing connections, creating statements, and loading drivers.

- **Issues Created:**
  - This manual approach consumes time and effort.
  - Developers face challenges in managing connections and other low-level details.

### Introduction to Spring JDBC:

- **Streamlining Database Operations:**
  - Spring JDBC provides a solution to the complexities of traditional JDBC.
  - Aims to streamline database operations and reduce the burden on developers.

- **Key Component - JdbcTemplate:**
  - Introduces the crucial component called `JdbcTemplate`.
  - Simplifies database operations, query execution, and result processing.

- **Efficient Connection Management:**
  - Addresses the issue of multiple connection creation.
  - Introduces the concept of a data source, enabling efficient management of connections.

### Project Setup:

- **Adding Spring JDBC Library:**
  - Create a new project and incorporate the Spring JDBC library.
  - Gain access to essential components for streamlined database connectivity.

- **Choosing Database:**
  - Consideration of different database management systems (DBMS) such as PostgreSQL, H2, MySQL, Oracle, and MS SQL.
  - Selection of H2, an in-memory database, for demonstration purposes.

- **Including JDBC Driver:**
  - Highlight the importance of JDBC drivers corresponding to the chosen DBMS.
  - Usage of the H2 JDBC driver for the selected H2 in-memory database.


  # Chapter 2: Setting Up Spring JDBC Project with H2 Database

  ## Project Initialization:

  ### 1. Using Spring Initializer:
  - Begin by visiting the [Spring Initializer](https://start.spring.io/) website.
  - Select Maven as the project type.
  - Set the language to Java.
  - Choose the appropriate Spring Boot version (e.g., 3.2).
  - Group ID: com.telusko
  - Artifact ID: Spring JDBC example
  - Description: Demo project for Spring Boot and JDBC
  - Packaging: Jar
  - Java Version: Java 17 (or Java 21, based on preference)

  ### 2. Adding Dependencies:
  - Click on "Add Dependency" to include necessary libraries.
  - Search and add "Spring Data JDBC."
  - Search and add "H2" for the H2 database.

  ### 3. Project Generation:
  - Click on "Generate" to create the project archive.
  - Download and unzip the generated project folder.
  - Open the project in IntelliJ IDEA.

  ## Project Structure and Configuration:

  ### 1. Reviewing `pom.xml`:
  - Open the `pom.xml` file to examine the dependencies.
  - Dependencies include `spring-boot-starter-data-jdbc` and `h2` for Spring JDBC and H2 database support.

  ### 2. Creating Student Class:
  - Create a `Student` class in the `model` package to represent data.
    - Properties: rollNumber, name, marks.
    - Generate getter and setter methods.
    - Implement `toString()` method.

  ### 3. Application Context Setup:
  - In the main class, instantiate the `Student` class using the Spring application context.
  - Set values for the student object.
  - Demonstrate the use of prototype scope for multiple student instances.

  ### 4. Building Service and Repository Layers:
  - Understand the need for layers in the application architecture.
  - **Repository Layer:** Represents the interaction with the database.
  - **Service Layer:** Acts as an intermediary between the client and the repository.
  - Implement these layers in the next video.

  ## Conclusion:
  In this chapter, we initiated the creation of a Spring JDBC project with an H2 database. The project setup involved using the Spring Initializer, adding necessary dependencies, and reviewing the `pom.xml` file. We also created a `Student` class to represent the data entity and demonstrated the initialization of student objects using the Spring application context. The focus now shifts to building the repository and service layers to facilitate database interaction. The architecture aims to establish a structured and efficient approach to handling student data in the application.

  # Chapter 2: Setting Up Spring JDBC Project with H2 Database

  ## Project Initialization:

  ### 1. Using Spring Initializer:
  - Begin by visiting the [Spring Initializer](https://start.spring.io/) website.
  - Select Maven as the project type.
  - Set the language to Java.
  - Choose the appropriate Spring Boot version (e.g., 3.2).
  - Group ID: com.telusko
  - Artifact ID: Spring JDBC example
  - Description: Demo project for Spring Boot and JDBC
  - Packaging: Jar
  - Java Version: Java 17 (or Java 21, based on preference)

  ### 2. Adding Dependencies:
  - Click on "Add Dependency" to include necessary libraries.
  - Search and add "Spring Data JDBC."
  - Search and add "H2" for the H2 database.

  ### 3. Project Generation:
  - Click on "Generate" to create the project archive.
  - Download and unzip the generated project folder.
  - Open the project in IntelliJ IDEA.

  ## Project Structure and Configuration:

  ### 1. Reviewing `pom.xml`:
  - Open the `pom.xml` file to examine the dependencies.
  - Dependencies include `spring-boot-starter-data-jdbc` and `h2` for Spring JDBC and H2 database support.

  ### 2. Creating Student Class:
  - Create a `Student` class in the `model` package to represent data.
    - Properties: rollNumber, name, marks.
    - Generate getter and setter methods.
    - Implement `toString()` method.

  ### 3. Application Context Setup:
  - In the main class, instantiate the `Student` class using the Spring application context.
  - Set values for the student object.
  - Demonstrate the use of prototype scope for multiple student instances.

  ### 4. Building Service and Repository Layers:
  - Understand the need for layers in the application architecture.
  - **Repository Layer:** Represents the interaction with the database.
  - **Service Layer:** Acts as an intermediary between the client and the repository.
  - Implement these layers in the next video.

  ## Conclusion:
  In this chapter, we initiated the creation of a Spring JDBC project with an H2 database. The project setup involved using the Spring Initializer, adding necessary dependencies, and reviewing the `pom.xml` file. We also created a `Student` class to represent the data entity and demonstrated the initialization of student objects using the Spring application context. The focus now shifts to building the repository and service layers to facilitate database interaction. The architecture aims to establish a structured and efficient approach to handling student data in the application.



  # Chapter 3: Implementing Service and Repository Layers with JDBC Template

  ## Service Layer Implementation:

  ### 1. Create StudentService Class:
  - Create a new class named `StudentService` in the `service` package.
  - Add an `addStudent` method in the service class.
    - This method will be responsible for calling the repository layer.
    - Use the `@Service` annotation to indicate that it is a service class.

  ```java
  @Service
  public class StudentService {

      @Autowired
      private StudentRepo repo;

      public void addStudent() {
          System.out.println("Added");
          repo.save(new Student());
      }

      // Additional method for fetching and printing all students
      public List<Student> getStudents() {
          List<Student> students = repo.findAll();
          System.out.println(students);
          return students;
      }
  }
  ```

  ## Repository Layer Implementation:

  ### 1. Create StudentRepo Class:
  - Create a new class named `StudentRepo` in the `repo` (or `dao`) package.
  - Add an `addStudent` method in the repository class.
    - Use the `@Repository` annotation to indicate that it is a repository class.

  ```java
  @Repository
  public class StudentRepo {

      // Autowire the JDBC template
      @Autowired
      private JdbcTemplate jdbc;

      public void save(Student s) {
          String SQL = "INSERT INTO student(roll_number, name, marks) VALUES(?, ?, ?)";
          int rowsAffected = jdbc.update(SQL, s.getRollNumber(), s.getName(), s.getMarks());
          System.out.println(rowsAffected + " rows affected");
      }

      // Additional method for fetching all students
      public List<Student> findAll() {
          // Dummy data for testing (replace with actual data retrieval from database)
          List<Student> students = new ArrayList<>();
          return students;
      }
  }
  ```

  ### 2. Create SQL Schema and Data Files:
  - Create a file named `schema.sql` in the `resources` folder and define the table schema.
  - Create a file named `data.sql` in the `resources` folder and insert sample data.

  ```sql
  -- schema.sql
  CREATE TABLE student (
      roll_number INT PRIMARY KEY,
      name VARCHAR(50),
      marks INT
  );

  -- data.sql
  INSERT INTO student(roll_number, name, marks) VALUES(101, 'Navin', 78);
  INSERT INTO student(roll_number, name, marks) VALUES(104, 'Kiran', 79);
  INSERT INTO student(roll_number, name, marks) VALUES(102, 'Harsh', 68);
  INSERT INTO student(roll_number, name, marks) VALUES(103, 'Sushil', 82);
  ```

  ### 3. Resolve Schema and Data Loading Issues:
  - Ensure correct SQL syntax, especially for data.sql, considering single quotes for string values.
  - Check for potential issues related to double quotes in the SQL queries.

  ### 4. Execute the Application:
  - Run the application and verify that the `addStudent` method successfully adds data to the H2 database.
  - Observe the console output for the number of rows affected.

  ### 5. Add Data Retrieval Method:
  - Enhance the `findAll` method in `StudentRepo` to fetch actual data from the database.
  - Use `jdbc.query` or other suitable methods to retrieve data.

  ```java
  // Updated findAll method
  public List<Student> findAll() {
      String SQL = "SELECT * FROM student";
      return jdbc.query(SQL, (rs, rowNum) ->
              new Student(rs.getInt("roll_number"), rs.getString("name"), rs.getInt("marks"))
      );
  }
  ```

  ### 6. Verify Data Retrieval:
  - Modify the `getStudents` method in `StudentService` to call `repo.findAll()` and print the retrieved data.
  - Run the application and ensure that data is retrieved from the database.

  ```java
  // Updated getStudents method
  public List<Student> getStudents() {
      List<Student> students = repo.findAll();
      System.out.println(students);
      return students;
  }
  ```

  ## Conclusion:
  In this chapter, the service and repository layers were implemented to work with the H2 database using Spring JDBC and `JdbcTemplate`. The `StudentService` class contains an `addStudent` method responsible for adding student data, and the `StudentRepo` class handles the actual database interaction. The SQL schema and data files were created to define the table structure and insert sample data. Some issues related to SQL syntax and data loading were addressed, and additional methods for fetching and printing data were added. The `JdbcTemplate` was utilized for executing SQL queries. The final steps involved executing the application to ensure successful data addition and retrieval from the H2 database.

  # Chapter 3 (Continued): Fetching Data using JDBC Template and RowMapper

  ## Fetching Data from Database:

  ### 1. Update `findAll` Method in `StudentRepo`:
  - Enhance the `findAll` method to use `jdbc.query` with a `RowMapper`.
  - Implement the `RowMapper` interface using an anonymous class or a lambda expression.

  ```java
  // Updated findAll method with RowMapper using an anonymous class
  public List<Student> findAll() {
      String SQL = "SELECT * FROM student";
      return jdbc.query(SQL, new RowMapper<Student>() {
          @Override
          public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
              Student s = new Student();
              s.setRollNo(rs.getInt("roll_number"));
              s.setName(rs.getString("name"));
              s.setMarks(rs.getInt("marks"));
              return s;
          }
      });
  }

  // Updated findAll method with RowMapper using a lambda expression
  public List<Student> findAllLambda() {
      String SQL = "SELECT * FROM student";
      return jdbc.query(SQL, (rs, rowNum) -> {
          Student s = new Student();
          s.setRollNo(rs.getInt("roll_number"));
          s.setName(rs.getString("name"));
          s.setMarks(rs.getInt("marks"));
          return s;
      });
  }
  ```

  ### 2. Execute the Application:
  - Run the application and verify that the `findAll` method successfully retrieves data from the H2 database.
  - Observe the console output, which should display the retrieved student objects.

  ### 3. Using Lambda Expression for RowMapper:
  - Optionally, use a lambda expression to simplify the `RowMapper` implementation.

  ```java
  // Using lambda expression for RowMapper
  public List<Student> findAllLambda() {
      String SQL = "SELECT * FROM student";
      return jdbc.query(SQL, (rs, rowNum) ->
          new Student(rs.getInt("roll_number"), rs.getString("name"), rs.getInt("marks"))
      );
  }
  ```

  ### Conclusion:
  In this section, the process of fetching data from the H2 database using the `findAll` method was implemented. The `RowMapper` interface was used to map the ResultSet data to `Student` objects. The `RowMapper` interface was implemented using both an anonymous class and a lambda expression to demonstrate different approaches. The application was executed to ensure the successful retrieval of data from the database. Additionally, the usage of lambda expressions to simplify code was demonstrated. The chapter concludes by highlighting the benefits of using Spring JDBC and the `JdbcTemplate` for database operations.

  # Chapter 4: Configuring Database Connection in Spring Boot

In this chapter, we will explore how to configure the database connection in a Spring Boot application using the `application.properties` file. We'll focus on connecting to a Microsoft SQL Server (MS SQL) database, and we'll cover the necessary properties and configurations.

## 1. Add Dependencies:
Ensure that you have the required dependencies in your `pom.xml` or `build.gradle` file. For connecting to MS SQL Server, you might want to include the following dependency:

### For Maven:
```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>9.4.0.jre11</version> <!-- Check for the latest version -->
</dependency>
```

### For Gradle:
```gradle
implementation 'com.microsoft.sqlserver:mssql-jdbc:9.4.0.jre11' // Check for the latest version
```

## 2. Configure `application.properties`:
Create or update the `application.properties` file in the `src/main/resources` directory. Add the following properties to configure the MS SQL Server database connection:

```properties
# Database Configuration
spring.datasource.url=jdbc:sqlserver://<your-server>:<port>;databaseName=<your-database>
spring.datasource.username=<your-username>
spring.datasource.password=<your-password>

# Hibernate Configuration
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.SQLServer2012Dialect
```

Replace the placeholders `<your-server>`, `<port>`, `<your-database>`, `<your-username>`, and `<your-password>` with your MS SQL Server details.

- `spring.datasource.url`: The JDBC URL for connecting to the MS SQL Server database.
- `spring.datasource.username` and `spring.datasource.password`: Database credentials.
- `spring.jpa.show-sql`: Enable SQL logging.
- `spring.jpa.hibernate.ddl-auto`: Hibernate behavior for database schema updates (e.g., `update`).
- `spring.jpa.properties.hibernate.dialect`: Hibernate dialect for MS SQL Server.

## 3. Verify and Execute:
Ensure that your Spring Boot application is running. Spring Boot will automatically read the `application.properties` file and use the specified configurations to establish a connection with the MS SQL Server database.

Verify the console logs for any connection-related messages or errors during the application startup.

## Conclusion:
In this chapter, we discussed how to configure the database connection in a Spring Boot application using the `application.properties` file. Specifically, we focused on connecting to a Microsoft SQL Server database and provided the necessary properties for configuring the datasource and Hibernate settings. This configuration is essential for establishing a connection to the database and performing CRUD operations in a Spring Boot application.


# **Next Section: Spring Web**
# Chapter 1: Introduction to Web Applications and Servlets

## 1.1 Overview of Web Applications in Spring

### 1.1.1 Spring Framework Structure
- Spring is an umbrella framework with multiple projects.
- It encompasses various projects, each serving different purposes.
- One significant category of projects deals with web applications.

### 1.1.2 Spring Web and Spring MVC
- Spring Web is used for building web applications in Spring.
- Alternatively, Spring MVC can be utilized if Spring Boot is not the preferred choice.

## 1.2 Importance of Web Applications

### 1.2.1 Ubiquity of Web Applications
- In the contemporary world, web applications are ubiquitous.
- Although mobile applications exist, most have corresponding web applications.
- Both desktop and mobile applications often share the same server.

### 1.2.2 Backend for Frontend (BFF) Concept
- Emphasis on the server being shared between browser clients and mobile applications.
- The server, which acts as the Backend for Frontend (BFF), is crucial for data management.

### 1.2.3 Dynamic Content vs. Static Content
- Building static content using HTML and CSS is straightforward.
- For dynamic content, backend programming is essential.
- Java is a viable language for backend development.

## 1.3 Role of Backend Programming in Web Applications

### 1.3.1 Handling Client Requests
- Backend receives and processes requests from clients (browser or mobile).
- The server must be capable of responding to client requests effectively.

### 1.3.2 Data Interchange
- Frontends (built with React, Angular, or Vanilla JS) often communicate with backends using JSON data.
- Clients can send data to the server in JSON format.

### 1.3.3 Server Responsibilities
- Backend is responsible for generating and providing data to the client.
- Data can be sourced from databases or other servers.
- JSON data plays a key role in facilitating data exchange.

## 1.4 Introduction to Servlets

### 1.4.1 Servlets as Server Components
- Servlets process and accept requests, sending data back to clients.
- Essential components for backend development in Java.

### 1.4.2 Servlet Containers
- Servlets cannot run directly on the JVM; they require a special container.
- Servlet container, also known as a web container, is necessary for running servlets.

### 1.4.3 Tomcat as a Servlet Container
- Tomcat is a lightweight and popular server for running servlets.

## 1.5 Servlets in the Context of Spring

### 1.5.1 Spring and Servlets Integration
- Spring works with servlets behind the scenes.
- Servlets are integral components even when using Spring for web applications.

### 1.5.2 Servlets vs. Reactive
- Servlets and Reactive are options for web applications.
- Focus in this module will be on servlets.

## 1.6 Getting Started with Servlets

### 1.6.1 Importance of Servlets
- Servlets are foundational for building backends that handle client requests.
- Spring Boot MVC will be explored later; initially, emphasis on servlets.

### 1.6.2 Servlet Creation
- Servlets are created to handle client requests and responses.

### 1.6.3 Servlet Containers for Execution
- Tomcat is an excellent option for running servlets.

### 1.6.4 Evolution Towards Spring
- Introduction to servlets precedes the transition to using Spring for web application development.

## 1.7 Conclusion
- Understanding the role of web applications in the Spring framework.
- Recognizing the importance of servlets in building dynamic backends.
- Preparing for the subsequent exploration of Spring Boot MVC.

# Chapter 2: Working with Servlets and Embedded Tomcat

## 2.1 Introduction to Servlet Deployment

### 2.1.1 Servlet Container Requirement
- Running servlets necessitates a servlet container like Tomcat.
- Two options for deployment: external Tomcat server or embedded Tomcat within the project.

### 2.1.2 Package Creation for Deployment
- Web applications are packaged with the extension .war (Web Archive) for deployment on Tomcat.
- Console applications utilize .jar (Java Archive), but web applications use .war.

## 2.2 External vs. Embedded Tomcat

### 2.2.1 External Tomcat Configuration
- External Tomcat involves additional configuration steps.
- Requires manual setup, package creation (.war), and deployment on the Tomcat server.

### 2.2.2 Embedded Tomcat Advantages
- Embedded Tomcat eliminates the need for external configuration.
- Tomcat is integrated into the project, simplifying the deployment process.
- Suitable for learning purposes and initial development.

### 2.2.3 External Tomcat for Advanced Features
- External Tomcat offers more features and customization options.
- Recommended for production-level applications.

## 2.3 Setting Up Embedded Tomcat in a Project

### 2.3.1 Downloading Apache Tomcat
- Tomcat can be downloaded from the Apache website.
- The presenter uses version 10.1.16 for demonstration.

### 2.3.2 Unzipping Tomcat and Project Structure
- Unzipping Tomcat reveals various folders.
- The "webapps" folder is where the project package (.war) is placed.

### 2.3.3 Starting and Stopping Tomcat
- Tomcat can be started using `startup.sh` and stopped using `shutdown.sh` in the "bin" folder.

## 2.4 Embedded Tomcat in Maven Project

### 2.4.1 Creating a Maven Project
- Demonstrated using an integrated development environment (IDE).
- A Maven project named "example" is created.

### 2.4.2 Adding Dependencies
- Dependencies for Servlet and embedded Tomcat are required in the Maven project.
- Jakarta Servlet API (4.0.4) and embedded Tomcat (8.5.96) dependencies are added to the project.

### 2.4.3 Maven Project Structure
- The project structure includes the Maven dependencies for Servlet and embedded Tomcat.

### 2.4.4 Reloading Maven Changes
- After adding dependencies, Maven changes must be reloaded or refreshed.

## 2.5 Writing Servlet Code

### 2.5.1 Setting Up the IDE
- The presenter opens the integrated development environment (IDE) to write servlet code.

### 2.5.2 Creating a Servlet
- A new Maven Java class is created as a simple example servlet.

### 2.5.3 Adding Servlet Dependencies
- Servlet and Tomcat dependencies must be added to the Maven project for writing servlet code.

### 2.5.4 Writing a Basic Servlet
- A basic example servlet code is written to handle HTTP GET requests.

### 2.5.5 Servlet Execution with Embedded Tomcat
- Executing the servlet using embedded Tomcat within the project.

# Chapter 2 (Continued): Working with Servlets and Embedded Tomcat

## 2.7 Handling Servlet Mapping

### 2.7.1 Servlet Class Creation
- The project structure is set up, and a new class, `HelloServlet`, is created.

### 2.7.2 Extending HttpServlet
- To define a servlet, extend the `HttpServlet` class, which provides essential features for handling requests and responses.

### 2.7.3 Creating the Service Method
- The `service` method is crucial in servlets, executed when a request is sent.
- Two parameters, `HttpServletRequest` and `HttpServletResponse`, are needed for handling client-server communication.

### 2.7.4 Basic Service Method Implementation
- For now, the `service` method is implemented to print a message, indicating its invocation.

## 2.8 Running the Servlet

### 2.8.1 Starting Tomcat in the Project
- Tomcat can be embedded within the project using the `Tomcat` class.
- The `start` method initiates the Tomcat server.

### 2.8.2 Ensuring Tomcat Continuity
- After starting Tomcat, it's essential to ensure it doesn't terminate immediately.
- The `getServer().await()` method keeps Tomcat running.

### 2.8.3 Troubleshooting Process Exit
- If the Tomcat process exits immediately, it's crucial to make Tomcat wait using `getServer().await()`.

### 2.8.4 Tomcat Startup Confirmation
- Console messages confirm the successful startup of Tomcat.

### 2.8.5 Initial Request Failure
- Attempting to access the servlet using the browser results in a 404 error.

## 2.9 Handling Servlet Mapping

### 2.9.1 Defining Servlet Mapping
- Tomcat needs to know when to call the servlet in response to specific URLs.
- Servlet mapping is required to link a URL pattern to the servlet.

### 2.9.2 URL Mapping Configuration
- To map the `/hello` URL to the `HelloServlet`, a configuration step is necessary.

### 2.9.3 Adding URL Mapping to Tomcat
- The `addServletMappingDecoded` method is used to specify the URL pattern mapped to the servlet.

### 2.9.4 Servlet Mapping Confirmation
- Console messages confirm the successful mapping of the servlet.

### 2.9.5 Testing the Servlet
- Reloading the browser and accessing the `/hello` URL successfully invokes the servlet's `service` method.

## 2.10 Conclusion

- Understanding the servlet creation process in a Java project.
- Extending `HttpServlet` for essential servlet features.
- Implementing the `service` method to handle client requests.
- Starting Tomcat within the project and troubleshooting process exit issues.
- Configuring servlet mapping to link a URL pattern to a servlet.
- Testing the servlet by accessing the specified URL.

### Example Servlet Code (Java):
```java
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {

    public void service(HttpServletRequest request, HttpServletResponse response) throws IOException {
        System.out.println("In Service method");
    }
}
```

Note: The provided example servlet code is a basic illustration and might need additional configurations based on the application's requirements.

# Chapter 2 (Continued): Working with Servlets and Embedded Tomcat

## 2.11 Servlet Mapping and Configuration (Continued)

### 2.11.1 Introduction to Servlet Mapping
- Servlet mapping is essential to connect URLs with corresponding servlets.
- Two approaches are discussed: XML-based configuration and annotation-based configuration.

### 2.11.2 Configuring Servlet Mapping via web.xml (External Tomcat)
- In an external Tomcat setup, `web.xml` file is used for servlet mapping configuration.
- Developers specify URL patterns and associate them with respective servlets.

### 2.11.3 Annotation-Based Mapping (External Tomcat)
- For external Tomcat, developers can use the `@WebServlet` annotation.
- The annotation helps specify URL patterns for servlets directly in the servlet class.

### 2.11.4 Embedded Tomcat Mapping Configuration
- In the case of embedded Tomcat, developers need to manually configure servlet mapping.
- Mapping is done programmatically within the Java code.

### 2.11.5 Setting Up Servlet Mapping Programmatically
- The process involves obtaining a `Context` object from Tomcat and using it to add servlets and mappings.

### 2.11.6 Tomcat Context Setup
```java
// Create a Tomcat instance
Tomcat tomcat = new Tomcat();

// Set the port number
tomcat.setPort(8081);

// Obtain the default context
Context context = tomcat.addContext("", null);
```

### 2.11.7 Adding Servlet to Tomcat Context
```java
// Create an instance of HelloServlet
HelloServlet helloServlet = new HelloServlet();

// Add the servlet to the context
Tomcat.addServlet(context, "HelloServlet", helloServlet);
```

### 2.11.8 Mapping Servlet to URL
```java
// Map the servlet to the /hello URL
context.addServletMappingDecoded("/hello", "HelloServlet");
```

### 2.11.9 Configuring Port Number
```java
// Set the port number for Tomcat
tomcat.setPort(8081);
```

### 2.11.10 Rerunning the Application
- Restarting the application to apply the port number change.

### 2.11.11 Confirming Mapping and Servlet Invocation
- Accessing the `/hello` URL in the browser and verifying the "In Service" message in the console.

## 2.12 Sending Data from Servlet to Client

### 2.12.1 Updating the Service Method
- Modifying the `service` method in the `HelloServlet` class to send a simple "Hello World" message to the client.

### 2.12.2 Writing to the HttpServletResponse
```java
// Modify the service method in HelloServlet
public void service(HttpServletRequest request, HttpServletResponse response) throws IOException {
    System.out.println("In Service method");

    // Send "Hello World" to the client
    response.getWriter().write("Hello World");
}
```

### 2.12.3 Revisiting the Browser
- Reloading the browser to check for changes and the updated content.

## 2.13 Conclusion

- Understanding servlet mapping and configuration in both external and embedded Tomcat environments.
- Learning how to programmatically set up the servlet, map it to a URL, and configure the port number.
- Updating the service method in the servlet to send data to the client using `HttpServletResponse`.


# Chapter 3: Sending Responses from Servlets

## 3.1 Introduction to Sending Responses

- Sending responses is a crucial aspect of servlet functionality.
- The response object in servlets allows developers to send data back to the client.

## 3.2 Sending a Simple Response

- To send a response from a servlet, developers can use the response object.
- By default, the response is empty, and developers need to populate it.

### 3.2.1 Using PrintWriter for Sending Response
```java
// Modifying the service method in HelloServlet
public void service(HttpServletRequest request, HttpServletResponse response) throws IOException {
    System.out.println("In Service method");

    // Get the PrintWriter from the response object
    PrintWriter out = response.getWriter();

    // Send "Hello World" to the client
    out.print("Hello World");
}
```

- The `PrintWriter` obtained from the response is used to print content to the client.

### 3.2.2 Printing HTML Tags
```java
// Printing HTML tags using PrintWriter
out.println("<h2><b>Hello World</b></h2>");
```

- Developers can include HTML tags in the response to format the content.

### 3.2.3 Setting Content Type
```java
// Setting content type to text/html
response.setContentType("text/html");
```

- Setting the content type helps the client interpret the response as HTML content.

### 3.2.4 Running and Verifying the Response
- Running the servlet and verifying the response in the browser.

## 3.3 Understanding MVC (Model-View-Controller) Concept

- The servlet currently combines accepting requests, processing data, and returning responses.
- Introducing the concept of MVC for better code organization and separation of concerns.

## 3.4 Handling Different HTTP Methods

- HTTP supports various methods such as GET, POST, PUT, and DELETE.
- Servlets can handle different methods by implementing corresponding methods like `doGet` and `doPost`.

### 3.4.1 doGet Method
```java
// Implementing the doGet method
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    // Code to handle GET requests
}
```

- The `doGet` method is used to handle GET requests.

### 3.4.2 doPost Method
```java
// Implementing the doPost method
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    // Code to handle POST requests
}
```

- The `doPost` method is used to handle POST requests.

### 3.4.3 Choosing the Appropriate Method
- Developers can choose the appropriate method based on the type of HTTP request they expect.

## 3.5 Conclusion

- Understanding how to send responses from servlets.
- Exploring the use of PrintWriter for response content.
- Setting content type for proper interpretation by the client.
- Introducing the MVC concept for better code organization.
- Handling different HTTP methods using corresponding methods in servlets.


# Chapter 4: Introduction to JSP and MVC in Java Web Applications

## 4.1 Building Web Applications with Java

- Building web applications using Java involves creating a backend that processes client requests and responds to them.
- Java is not used for building the frontend; instead, HTML, CSS, and JavaScript or frontend frameworks like React are employed.

## 4.2 Role of Servlets in Web Applications

- Servlets are used in Java web applications to handle backend logic, process client requests, and send responses.

## 4.3 Introduction to JSP (JavaServer Pages)

- When returning data from a servlet to the client, writing HTML directly in Java code can become cumbersome.
- JSP (JavaServer Pages) is introduced as a view technology where HTML pages can be created, and Java code can be embedded within them.

### 4.3.1 Using JSP for Frontend
```jsp
<!-- Example JSP Page -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sample JSP Page</title>
</head>
<body>
    <h1>Welcome to JSP!</h1>
    <%-- Java code embedded in JSP --%>
    <%
        String message = "Hello from Java!";
        out.print(message);
    %>
</body>
</html>
```

- JSP allows embedding Java code within HTML pages, making it easier to handle dynamic content.

### 4.3.2 JSP Conversion to Servlet
- Behind the scenes, the JSP page gets converted into a servlet before running on the Tomcat server.
- Developers focus on writing HTML and Java code in JSP, and the conversion process is handled automatically.

## 4.4 MVC (Model-View-Controller) Architecture

- MVC is a design pattern widely used in web applications.
- It separates the application into three components: Model, View, and Controller.

### 4.4.1 Model
- The Model represents the data structure and business logic.
- In Java web applications, the Model is often implemented using POJO (Plain Old Java Object) classes.

### 4.4.2 View
- The View is responsible for presenting the data to the client.
- JSP is introduced as a view technology to create dynamic and interactive HTML pages.

### 4.4.3 Controller
- The Controller handles client requests, processes data, and interacts with the Model.
- In Java web applications, servlets act as controllers to manage the flow of data between the client and the backend.

## 4.5 Implementing MVC in Java Web Applications

- In a Java web application, servlets serve as controllers, JSP as the view, and simple Java classes or POJOs as the model.
- MVC provides a structured approach to building scalable and maintainable web applications.

## 4.6 Transition to Spring Boot

- Spring Boot is introduced as the next step to simplify web application development.
- It provides an enhanced framework for building Java-based web applications, handling MVC architecture more efficiently.

## 4.7 Conclusion

- Understanding the role of JSP in simplifying frontend development.
- Overview of MVC architecture in Java web applications.
- Servlets as controllers, JSP as the view, and POJO classes as the model in MVC.
- Transition to Spring Boot for more streamlined web application development.

# Chapter 5: Creating a Web Application with Spring Boot

## 5.1 Introduction to Spring Boot for Web Development

- Spring Boot simplifies web application development by providing a streamlined approach and reducing the need for extensive configuration.

## 5.2 Two Approaches: Spring Framework vs. Spring Boot

- Two ways of building a Spring MVC project: using the normal Spring framework or utilizing Spring Boot.
- Spring Boot is favored for its ease of use and minimal configuration compared to the traditional Spring framework.

## 5.3 Creating a Spring Boot Web Application

- To create a Spring Boot project:
  1. Visit [start.spring.io](https://start.spring.io/) (Spring Initializr).
  2. Choose project details (Maven, Java, Spring Boot version, etc.).
  3. Add dependencies, e.g., "Spring Web" for web applications.
  4. Generate the project and unzip it.

### 5.3.1 Example Project Configuration
```xml
<!-- Example Pom.xml Configuration -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- The project uses Spring Boot Starter Web as a dependency.

## 5.4 Exploring the Spring Boot Project Structure

- The project contains an embedded Tomcat server, allowing it to be run as a standalone application.

## 5.5 Running the Spring Boot Application

- Running a Spring Boot application:
  1. Open the project in an IDE.
  2. Locate the main class (usually annotated with `@SpringBootApplication`).
  3. Run the application.

### 5.5.1 Accessing the Application
- By default, the embedded Tomcat server runs on `localhost:8080`.
- Access the application in the browser: `http://localhost:8080`.

## 5.6 Creating a Controller in Spring Boot

- A controller is needed to handle incoming requests and provide a response.
- In Spring Boot, controllers are typically created using annotations.

### 5.6.1 Example Controller
```java
// Example Controller in Spring Boot
@RestController
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "Welcome to Spring Boot!";
    }
}
```

- `@RestController` annotation indicates that this class will serve as a controller.
- `@GetMapping("/")` maps the method to handle requests for the root URL.

## 5.7 Running the Spring Boot Application with a Controller

- After creating a controller, rerun the Spring Boot application.
- Access the root URL (`http://localhost:8080`) in the browser.

## 5.8 Conclusion

- Introduction to creating a web application using Spring Boot.
- Utilizing Spring Initializr for project setup.
- Exploring the Spring Boot project structure.
- Running a Spring Boot application and accessing it in the browser.
- Creating a simple controller to handle requests and provide a response.

This chapter provides a foundational understanding of creating a Spring Boot web application and sets the stage for further exploration of Spring Boot features and capabilities.

# Chapter 6: Handling Requests and Form Submission in Spring Boot

## 6.1 Overview of the Homepage

- Currently, attempting to access the homepage results in a "404" error.
- The goal is to create a homepage with a form to input two numbers.

## 6.2 Creating a Simple HTML Form

- To capture user input for adding two numbers, a simple HTML form is created.
- The form includes text fields for the two numbers and a submit button.

### 6.2.1 Example HTML Form
```html
<!-- index.jsp -->
<form action="/add" method="GET">
    <label for="num1">Enter the first number:</label>
    <input type="text" id="num1" name="num1" required>

    <label for="num2">Enter the second number:</label>
    <input type="text" id="num2" name="num2" required>

    <button type="submit">Submit</button>
</form>
```

## 6.3 Adding Styling with CSS

- A CSS file (`style.css`) is created to add basic styling to the form.

### 6.3.1 Example CSS
```css
/* style.css */
body {
    background-color: #f2e6d9;
    font-family: 'Coffee';
    text-align: center;
    margin-top: 50px;
}

form {
    background-color: #d9bf9e;
    padding: 20px;
    border-radius: 10px;
    width: 300px;
    margin: 0 auto;
}

label {
    font-size: 18px;
}

input {
    margin-bottom: 10px;
    padding: 8px;
    font-size: 16px;
}

button {
    padding: 10px;
    font-size: 16px;
    background-color: #50394c;
    color: #ffffff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}
```

## 6.4 Implementing a Controller to Handle Form Submission

- A controller, `HomeController`, is created to handle the form submission and perform an operation (e.g., addition of two numbers).

### 6.4.1 Example Controller
```java
// HomeController.java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "index";
    }

    @RequestMapping("/add")
    @ResponseBody
    public String addNumbers(@RequestParam int num1, @RequestParam int num2) {
        int sum = num1 + num2;
        return "The sum of " + num1 + " and " + num2 + " is: " + sum;
    }
}
```

## 6.5 Mapping the Controller to Handle Form Submission

- The `HomeController` is annotated with `@RequestMapping("/add")` to map the URL "/add" to the `addNumbers` method.
- `@RequestParam` is used to retrieve values from the form submission.

## 6.6 Running the Application and Handling Form Submission

- Running the Spring Boot application and submitting the form.
- Observing the URL changes to "/add" with the provided values.

### 6.6.1 Example Output
```
The sum of 6 and 7 is: 13
```

## 6.7 Conclusion

- Creating a form in HTML for user input.
- Styling the form using CSS.
- Implementing a controller in Spring Boot to handle form submission.
- Using `@RequestParam` to retrieve values from the form submission.
- Running the application and observing the result of the form submission.


# Chapter 7: Mapping Requests with RequestMapping

## Mapping the Request
When working with Spring controllers, mapping requests is a crucial step. In Spring, you utilize annotations for this purpose. The `@RequestMapping` annotation is commonly used to map a specific URL to a method in your controller.

Example:
```java
@Controller
public class HomeController {

    @RequestMapping("/")
    public String home() {
        return "index.jsp";
    }
}
```

In this example, the `home()` method is mapped to the root URL ("/"). When a request is made to this URL, the method is executed.

## Using RequestMapping with Path
You can further specify the path in the `@RequestMapping` annotation. For instance, if you want to map the `/homepage` URL, you can do so by adding the path.

Example:
```java
@Controller
public class HomeController {

    @RequestMapping("/homepage")
    public String home() {
        return "index.jsp";
    }
}
```

## Handling Downloads Issue
By default, Spring Boot may not fully support JSP pages, leading to unexpected behavior, such as a download prompt instead of rendering the page. To address this, you need to add the Tomcat Jasper dependency to convert JSP into servlets.

Add the Tomcat Jasper dependency in your `pom.xml`:
```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <version>10.1.16</version> <!-- Adjust based on your Tomcat version -->
</dependency>
```

This dependency helps in converting JSP into servlets, allowing Spring Boot to handle JSP pages.

## Mapping Requests with GetMapping and PostMapping
Aside from `@RequestMapping`, Spring provides specialized annotations like `@GetMapping` and `@PostMapping` for mapping GET and POST requests, respectively.

Example:
```java
@Controller
public class HomeController {

    @GetMapping("/getExample")
    public String handleGetRequest() {
        return "getExample.jsp";
    }

    @PostMapping("/postExample")
    public String handlePostRequest() {
        return "postExample.jsp";
    }
}
```

## Conclusion
Mapping requests is a fundamental aspect of building web applications with Spring. Utilizing annotations like `@RequestMapping`, `@GetMapping`, and `@PostMapping` enables you to specify how different URLs should be handled by your controller methods. Additionally, addressing JSP-related issues, such as downloads, ensures proper rendering of pages in Spring Boot applications. With these concepts, you're equipped to manage incoming requests effectively.

# Chapter 8: Handling User Input and Displaying Results

## Performing Operations on the Server
Now that we have our basic homepage set up, let's move beyond a simple display. The essence of a backend is in processing requests and providing meaningful results. We'll delve into a straightforward example: adding two numbers.

## Building a Simple Calculator
Our objective is to create a form where a user can input two numbers, submit the form, and see the result. We'll start by crafting a form with two text fields and a submit button. The form will then send a request to the server for processing.

Here's a basic HTML form:
```html
<form action="/add" method="post">
    <label for="num1">Enter the first number:</label>
    <input type="text" id="num1" name="num1">

    <label for="num2">Enter the second number:</label>
    <input type="text" id="num2" name="num2">

    <button type="submit">Submit</button>
</form>
```

To style our page, we'll use a simple CSS file (style.css) to improve the visual presentation.

## Setting Up the Controller
Now, let's create a method in our controller to handle the form submission. We'll call this method `add`. This method will accept the two numbers from the `HttpServletRequest`, perform the addition, and return the result.

```java
@Controller
public class HomeController {

    @RequestMapping("/")
    public String home() {
        return "index.jsp";
    }

    @PostMapping("/add")
    public String add(HttpServletRequest request, Model model) {
        // Extracting values from the request
        int num1 = Integer.parseInt(request.getParameter("num1"));
        int num2 = Integer.parseInt(request.getParameter("num2"));

        int result = num1 + num2;
        System.out.println("Result: " + result);

        // Add the result to the model to display it on the page
        model.addAttribute("result", result);

        // Return the name of the result page
        return "result.jsp";
    }
}
```

In this `add` method, we directly use `HttpServletRequest` to extract the values of `num1` and `num2` from the request. The result is calculated, printed to the console, and added to the model so that it can be displayed on the result page.

## Displaying Results
Now, let's create the result.jsp page to display the result.

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Result Page</title>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h2>Result Page</h2>
    <p>Result is: ${result}</p>
</body>
</html>
```

In this JSP page, we use the expression language (`${result}`) to display the calculated result.

## Verifying the Setup
After restarting the application, go to the browser and submit the form. Check if the result is displayed on the result.jsp page. If everything is set up correctly, you should see the addition result.

Congratulations! You've successfully created a simple calculator application that accepts user input, processes it on the server, and displays the result on a different page. This lays the groundwork for handling user interactions in a Spring web application.

# Chapter 8: Handling User Input and Displaying Results (Continued)

## Passing Data to the Result Page

Now that we've calculated the result on the server, we want to display it on the result.jsp page. To achieve this, we need to send the result data from the controller to the result.jsp page. There are multiple ways to pass data between pages, and we'll use the session object in this example.

### Using the HttpSession Object

In the controller's `add` method, we declare an `HttpSession` object and pass it as a parameter to the method:

```java
@PostMapping("/add")
public String add(HttpServletRequest request, HttpSession session, Model model) {
    // Extracting values from the request
    int num1 = Integer.parseInt(request.getParameter("num1"));
    int num2 = Integer.parseInt(request.getParameter("num2"));

    int result = num1 + num2;
    System.out.println("Result: " + result);

    // Adding the result to the session
    session.setAttribute("result", result);

    // Return the name of the result page
    return "result.jsp";
}
```

In this example, we use the `setAttribute` method of the `HttpSession` object to store the result data with the name "result."

### Displaying Result on the Result Page

Now, on the result.jsp page, we need to retrieve the result from the session object and display it. We can achieve this using either the traditional JSP syntax or JSTL (JSP Standard Tag Library).

#### Traditional JSP Syntax

Using traditional JSP syntax, we retrieve the result from the session and display it:

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Result Page</title>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h2>Result Page</h2>
    <p>Result is: <% out.print(session.getAttribute("result")); %></p>
</body>
</html>
```

Here, `<% out.print(session.getAttribute("result")); %>` retrieves the result from the session and prints it on the page.

#### JSTL Syntax

Alternatively, we can use JSTL to simplify the syntax:

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <title>Result Page</title>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
    <h2>Result Page</h2>
    <p>Result is: ${result}</p>
</body>
</html>
```

Here, `${result}` directly retrieves the result from the session using JSTL.

## Verifying the Displayed Result

After making these changes, restart your application and submit the form with different values. Check if the result is correctly displayed on the result.jsp page. The session object helps in passing data between different pages in a Spring web application.

### Note on JSTL
JSTL provides convenient tags and functions for JSP. Ensure you have the JSTL library included in your project for the JSTL syntax to work. You can add the JSTL dependency in your project's configuration.

This concludes our discussion on handling user input, processing it on the server, and displaying the results in a Spring web application. In the next chapters, we'll explore more advanced features and functionalities of the Spring framework.

# Chapter 9: Simplifying Code with @RequestParam

In this chapter, we'll explore how to simplify the code in a Spring application, specifically when dealing with request parameters. The Spring framework provides a convenient way to handle query parameters using the `@RequestParam` annotation, making the code more concise and readable.

## Using @RequestParam to Simplify Code

Consider the previous example where we were manually extracting values from the request object:

```java
@PostMapping("/add")
public String add(HttpServletRequest request, HttpSession session, Model model) {
    // Extracting values from the request
    int num1 = Integer.parseInt(request.getParameter("num1"));
    int num2 = Integer.parseInt(request.getParameter("num2"));

    // Calculation logic
    int result = num1 + num2;

    // Adding the result to the session
    session.setAttribute("result", result);

    // Return the name of the result page
    return "result.jsp";
}
```

Now, with `@RequestParam`, we can simplify this code:

```java
@PostMapping("/add")
public String add(@RequestParam int num1, @RequestParam int num2, HttpSession session, Model model) {
    // Calculation logic
    int result = num1 + num2;

    // Adding the result to the session
    session.setAttribute("result", result);

    // Return the name of the result page
    return "result.jsp";
}
```

By using `@RequestParam`, we directly declare the parameters `num1` and `num2` in the method signature. Spring automatically extracts these values from the request's query parameters, eliminating the need for manually parsing the request.

## Handling Different Parameter Names

If the parameter names in the method do not match the query parameter names, you can use the `name` attribute of `@RequestParam`:

```java
@PostMapping("/add")
public String add(@RequestParam(name = "num1") int number1, @RequestParam(name = "num2") int number2, HttpSession session, Model model) {
    // Calculation logic
    int result = number1 + number2;

    // Adding the result to the session
    session.setAttribute("result", result);

    // Return the name of the result page
    return "result.jsp";
}
```

Here, the `name` attribute specifies the corresponding query parameter names.

## Optional Parameters

If a parameter is optional, you can use the `required` attribute of `@RequestParam`:

```java
@PostMapping("/add")
public String add(@RequestParam(required = false) Integer num1, @RequestParam(required = false) Integer num2, HttpSession session, Model model) {
    // Check if the parameters are present
    if (num1 != null && num2 != null) {
        // Calculation logic
        int result = num1 + num2;

        // Adding the result to the session
        session.setAttribute("result", result);
    }

    // Return the name of the result page
    return "result.jsp";
}
```

By setting `required` to `false`, the parameters become optional.

## Conclusion

Using `@RequestParam` in Spring simplifies the code by directly mapping query parameters to method parameters. It enhances readability and reduces the need for manual request handling, making the code more concise and expressive. In the next chapter, we'll explore additional features and annotations in the Spring framework.

# Chapter 9 (Continued): Enhancing the Application

In this section, we will further enhance the Spring application by leveraging the Model object to transfer data between the controller and the view. Additionally, we will remove the explicit mention of the view's extension (.jsp) and move the view files to a different folder.

## Leveraging Model Object

Previously, we used the HttpSession object to pass data between the controller and the view. Now, we will replace it with the Model object, which is a more Spring-centric approach for transferring data.

```java
@PostMapping("/add")
public String add(@RequestParam int num1, @RequestParam int num2, Model model) {
    // Calculation logic
    int result = num1 + num2;

    // Adding the result to the model
    model.addAttribute("result", result);

    // Return the logical view name
    return "result";
}
```

By using `model.addAttribute`, we directly add the result to the model. The method now returns the logical view name "result" without specifying the file extension.

## Changing View Folder Structure

Let's organize the project by moving the view files to a separate folder named "views." This change provides better structure and prepares the application for potential view technology switches.

1. Create a new folder named "views" in the resources directory.

2. Move the existing JSP files (e.g., index.jsp and result.jsp) into the "views" folder.

After making these changes, ensure that the application can still locate the views.

## Testing the Enhanced Application

After restarting the application, test it by navigating to the homepage and performing addition operations. Verify that the changes made, such as using the Model object and updating the view folder structure, do not impact the functionality.

By eliminating the explicit mention of the view's extension and organizing views in a separate folder, the application becomes more modular and adaptable to changes in the view layer.

# Chapter 9 (Continued): Configuring ViewResolver

In this section, we will address the issue of finding and resolving views by configuring a ViewResolver in the Spring application. By specifying the location and extension of the view files, we can ensure that the ViewResolver can locate the correct views.

## Configuring ViewResolver in application.properties

To configure the ViewResolver, we need to update the `application.properties` file with the necessary properties. We are specifically interested in setting the prefix and suffix for locating and identifying view files.

1. Open the `application.properties` file in the `src/main/resources` directory.

2. Add the following properties to set the view prefix and suffix:

```properties
# Set the prefix for views (location)
spring.mvc.view.prefix=/views/

# Set the suffix for views (file extension)
spring.mvc.view.suffix=.jsp
```

These properties specify that views are located in the `/views/` folder and have a `.jsp` file extension.

## Restarting the Application

After making these changes, restart the Spring Boot application to apply the new configurations.

## Testing the Enhanced Configuration

Once the application has restarted, test it by navigating to the homepage and performing addition operations. Ensure that the views are located in the correct folder and that the file extension is not explicitly mentioned in the controller.

## Addressing CSS Issue

If there is an issue with loading CSS, it may be due to the changes in folder structure. Ensure that the CSS files are correctly referenced in the JSP files with the updated view folder structure.

## Conclusion

By configuring a ViewResolver, we have made our Spring application more flexible and modular. Changes in the view layer, such as moving to a different technology or updating the folder structure, can now be accommodated more easily. This is in line with the principles of Spring MVC, promoting separation of concerns and ease of configuration.

In the next chapter, we will explore further optimizations and features to enhance the overall functionality and user experience of our Spring application.

# Chapter 9: Model and View in Spring MVC

## Introduction
In this chapter, we delve into the concepts of Model and View in Spring MVC. We've been using the Model object to pass data between pages, but now we'll explore a more comprehensive approach by introducing the Model and View object.

### Structuring the Project
Before discussing Model and View, an issue with loading CSS in the application was addressed. The CSS file was moved to a more appropriate location, either in the `static` folder or the `web app` folder. The `static` folder is recommended for static resources like CSS files, images, etc.

```plaintext
/src
   /main
      /resources
         /static
            /css
               styles.css
         application.properties
      /webapp
         /WEB-INF
            /views
               result.jsp
```

## Loading CSS Issue Resolution
The issue of CSS not loading properly was resolved by placing the CSS file in the `static` folder within the `resources` directory. Alternatively, the `webapp` folder could also be used. The `static` folder is preferable for structuring static content.

```plaintext
/src
   /main
      /resources
         /static
            /css
               styles.css
```

## Model and View Object
### Enhancing the Model
Instead of using only the Model object, a more versatile approach involves using the Model and View (MV) object. This approach allows for setting both data and view name in a single object. This is achieved by creating a `ModelAndView` (MV) object.

```java
import org.springframework.web.servlet.ModelAndView;

@RequestMapping("/add")
public ModelAndView add(@RequestParam("num1") int num1, @RequestParam("num2") int num2) {
    int result = num1 + num2;
    ModelAndView modelAndView = new ModelAndView("result"); // Set the view name
    modelAndView.addObject("result", result); // Add data to the model
    return modelAndView;
}
```

### Updating the Controller
The controller's return type is updated to return `ModelAndView`. This reflects the change in the controller's logic to use the `ModelAndView` object instead of a simple view name.

```java
@Controller
public class HomeController {

    @RequestMapping("/")
    public String home() {
        return "home";
    }

    @RequestMapping("/add")
    public ModelAndView add(@RequestParam("num1") int num1, @RequestParam("num2") int num2) {
        int result = num1 + num2;
        ModelAndView modelAndView = new ModelAndView("result"); // Set the view name
        modelAndView.addObject("result", result); // Add data to the model
        return modelAndView;
    }
}
```

### Conclusion
This approach of using the `ModelAndView` object provides a cleaner and more structured way of handling both data and view name in a Spring MVC application. Developers can choose between returning a `String` (view name) or `ModelAndView` based on their specific requirements. The versatility offered by Spring MVC empowers developers to make informed decisions about their application's architecture.

# Chapter 10: Model Attribute in Spring MVC

## Introduction
In this chapter, we explore the concept of `@ModelAttribute` in Spring MVC. We'll understand the need for `@ModelAttribute` by taking an example where we want to send data related to an entity (in this case, an alien) from a form to the server.

### Background
So far, we've been working with basic data types, like numbers, in our Spring MVC application. Now, we encounter a scenario where we want to send more complex data, such as an entity with multiple attributes, to the server.

## Example Scenario: Alien Entity
Consider a scenario where we want to send data related to an "alien" entity from a form to the server. The alien entity has two attributes: `aid` (alien ID) and `aname` (alien name). Instead of sending individual parameters for `aid` and `aname`, we want to send the entire "alien" object to the server.

### Entity Class: Alien
```java
public class Alien {
    private int aid;
    private String aname;

    // Constructors, getters, setters, and toString methods
}
```

## Updating the Controller
### Old Approach: Accepting Individual Parameters
Previously, we were accepting individual parameters (`aid` and `aname`) for the alien entity in the controller method. This becomes cumbersome when dealing with entities with multiple attributes.

```java
@RequestMapping("/addAlien")
public String addAlien(@RequestParam("aid") int aid, @RequestParam("aname") String aname, Model model) {
    Alien alien = new Alien();
    alien.setAid(aid);
    alien.setAname(aname);

    model.addAttribute("alien", alien);

    return "result";
}
```

### Need for Improvement
While the old approach works, it becomes impractical when dealing with entities with numerous attributes. A better approach is needed to handle complex objects more efficiently.

## Introduction to `@ModelAttribute`
### Simplifying Data Binding
The `@ModelAttribute` annotation simplifies the process of binding form data to a model attribute. Instead of accepting individual parameters, we can directly accept the entire object.

```java
@RequestMapping("/addAlien")
public String addAlien(@ModelAttribute("alien") Alien alien) {
    return "result";
}
```

### Automatically Populating Object
With `@ModelAttribute`, Spring MVC automatically populates the `Alien` object's attributes from the form data.

## Conclusion
In this chapter, we've addressed the need for `@ModelAttribute` in a Spring MVC application. By using this annotation, we can simplify the data-binding process, especially when dealing with entities with multiple attributes. The `@ModelAttribute` annotation enhances code readability and reduces the manual effort required for data binding. In the next video, we'll delve deeper into the practical implementation of `@ModelAttribute` in the context of our alien entity example.

# Chapter 10: Model Attribute in Spring MVC (Continued)

## Using `@ModelAttribute` to Simplify Data Binding

In this section, we continue our discussion on the use of `@ModelAttribute` in Spring MVC to simplify the process of data binding. We explore scenarios where using `@ModelAttribute` becomes optional and how it can be employed at both the method and parameter levels.

### Making `@ModelAttribute` Optional

Previously, we saw that when dealing with a form where an entire object (e.g., an alien entity) needs to be sent to the server, the use of `@ModelAttribute` simplifies the process. However, in some cases, it becomes optional based on certain conditions.

#### Example: Accepting Object without `@ModelAttribute`
```java
@RequestMapping("/addAlien")
public String addAlien(Alien alien, Model model) {
    return "result";
}
```
Here, Spring automatically binds form data to the `Alien` object without explicitly using `@ModelAttribute`. This is possible when the variable name in the method parameter matches the object name sent from the form.

#### Specifying a Different Variable Name
```java
@RequestMapping("/addAlien")
public String addAlien(@ModelAttribute("alienOne") Alien alien) {
    return "result";
}
```
In cases where the variable name in the method parameter differs from the object name in the form, using `@ModelAttribute` with a specified name becomes necessary.

### Use of `@ModelAttribute` at the Method Level

#### Example: Dynamic Course Name
```java
@ModelAttribute("course")
public String courseName() {
    return "Java"; // This can be dynamic, fetched from a database, etc.
}

@RequestMapping("/homepage")
public String homepage() {
    return "homepage";
}
```
Here, the `courseName` method returns the value for the `course` attribute. The `@ModelAttribute` annotation at the method level ensures that this value is available for use in the corresponding view.

## Conclusion

The use of `@ModelAttribute` in Spring MVC provides a convenient way to simplify data binding. While it's commonly used at the parameter level to bind form data to objects, it's essential to understand when its usage becomes optional based on variable names. Additionally, applying `@ModelAttribute` at the method level allows for the inclusion of dynamic attributes in the model. This flexibility contributes to cleaner and more maintainable Spring MVC code. In the next chapter, we will explore further topics related to Spring MVC.

When an object needs to be sent from an HTML form to a Spring MVC controller, the object's attributes are typically mapped to form input fields using their names. Here's a step-by-step explanation:

1. **HTML Form Setup:**
   - In your HTML form, input fields are used to collect data for each attribute of the object.
   - Each input field has a `name` attribute, which is essential for mapping the data to the object.

   ```html
   <form action="/addAlien" method="post">
       <label for="aid">Alien ID:</label>
       <input type="text" id="aid" name="aid" />

       <label for="aname">Alien Name:</label>
       <input type="text" id="aname" name="aname" />

       <input type="submit" value="Submit" />
   </form>
   ```

2. **Controller Method:**
   - The Spring MVC controller method that handles the form submission should have a parameter representing the object.
   - The parameter type is the class of the object you want to receive.

   ```java
   @RequestMapping("/addAlien")
   public String addAlien(Alien alien, Model model) {
       // Logic to process the Alien object
       return "result";
   }
   ```

   In this example, Spring will automatically map the form input fields (`aid` and `aname`) to the attributes of the `Alien` class.

3. **Object Binding:**
   - When the form is submitted, Spring MVC uses a process called data binding to bind the form data to the object.
   - It matches the `name` attributes of the form fields with the attribute names of the object.
   - The object is then populated with the values submitted through the form.

   For example, if the form is submitted with `aid=101` and `aname=Navin`, Spring MVC will create an `Alien` object with `aid=101` and `aname=Navin`.

4. **@ModelAttribute (Optional):**
   - The `@ModelAttribute` annotation is used at the parameter level in the controller method to explicitly indicate that the parameter should be populated from the model.
   - If the parameter name matches the object name (e.g., `Alien`), Spring MVC will recognize it even without the `@ModelAttribute` annotation.

   ```java
   @RequestMapping("/addAlien")
   public String addAlien(@ModelAttribute("alien") Alien alien, Model model) {
       // Logic to process the Alien object
       return "result";
   }
   ```

   In this case, `@ModelAttribute` becomes optional if the parameter name (`alien`) matches the object name.

In summary, the object is sent from the form by mapping the input field names to the attribute names of the object. Spring MVC handles the process of binding the form data to the object, and the controller method receives the populated object for further processing. The `@ModelAttribute` annotation is used to enhance control over the binding process when needed.

# Chapter 11: Spring MVC Without Spring Boot

## Introduction
In this module, we'll delve into Spring MVC without the assistance of Spring Boot. While Spring Boot simplifies our tasks, understanding the manual configuration process in Spring MVC is crucial. We'll focus on configuring Spring MVC with an external Tomcat server and using Eclipse as the IDE for this section.

### Prerequisites
1. **Download External Tomcat:**
   - Visit [Apache Tomcat Download](http://tomcat.apache.org/download.cgi) and download the desired version (e.g., version 10).
   - Extract the zip folder to obtain the Tomcat server.

2. **Use an IDE:**
   - We'll use Eclipse for this section since it supports external servers. If using IntelliJ, consider the ultimate version for external server support.
# Chapter 11 (Continued): Spring MVC Without Spring Boot

## Setting Up Eclipse and Maven Project
1. **Download Eclipse EE Version:**
  - Download the Eclipse IDE for Enterprise Java Developers from [Eclipse Downloads](https://www.eclipse.org/downloads/).
  - Ensure it's the EE (Enterprise Edition) version.

2. **Create Maven Project:**
  - Open Eclipse and create a new Maven project.
  - Choose `File` -> `New` -> `Maven Project`.
  - Select `Internal` catalog, then choose `webapp` archetype.
  - Set the group ID as `com.telusko`, artifact ID as `SpringMVCdemo`, and click `Finish`.

3. **Project Structure:**
  - Observe the project structure with `src/main` containing `webapp` and `java` folders.

## Copy Code and Adjust Package
1. **Copy Existing Code:**
  - Copy the `Alien.java` and `HomeController.java` files from the Spring Boot project.

2. **Create Package:**
  - Create a package named `com.telusko.SpringMVCDemo` in the `src/main/java` folder.

3. **Paste Code:**
  - Paste the copied files into the created package.
  - Adjust the package name in the files to match the new structure.

## Add Spring MVC Dependency
1. **Add Dependency:**
  - Search for "Spring Web MVC" dependency on [Maven Repository](https://mvnrepository.com/).
  - Copy the XML code for the latest version (e.g., 6.1.0).
  - Paste the dependency in the `pom.xml` file of the Eclipse project.

2. **Refresh Maven Project:**
  - Right-click on the project in Eclipse and choose `Refresh`.
  - This should download the necessary dependencies.

3. **Verify Dependencies:**
  - Check the `Maven Dependencies` in the project. You should see Spring-related libraries.

4. **Adjust JSP Files:**
  - Copy the `views` folder (containing JSP files) from the Spring Boot project to `src/main/webapp/`.

## Set Up External Tomcat Server
1. **Download Apache Tomcat:**
  - Download Apache Tomcat 10 from [Tomcat Downloads](http://tomcat.apache.org/download.cgi).
  - Extract the downloaded zip file to obtain the Tomcat server.

2. **Configure External Server in Eclipse:**
  - Open Eclipse and go to `Window` -> `Preferences`.
  - Navigate to `Server` -> `Runtime Environments`.
  - Add an external Tomcat server by selecting the Tomcat installation directory.

# Chapter 11 (Continued): Configuring Dispatcher Servlet

## Configuring Dispatcher Servlet
1. **Introduction:**
   - The Spring project, though set up with Maven dependencies and an external Tomcat server, encounters a 500 internal server error.
   - The issue arises because the Dispatcher Servlet needs additional configuration.

2. **Dispatcher Servlet Configuration:**
   - Dispatcher Servlet is a crucial component responsible for handling requests in a Spring MVC application.
   - Configuration is required to help Dispatcher Servlet understand controllers and their mappings.

3. **Creating the Servlet Configuration File:**
   - Create a new XML file named `telusko-servlet.xml`.
   - This file will contain the necessary configurations for the Dispatcher Servlet.

4. **Defining Dispatcher Servlet in the Configuration File:**
   - Open the `telusko-servlet.xml` file.
   - Use the `<beans>` tag to define the Spring beans.
   - Inside the `<beans>` tag, define the `DispatcherServlet` bean.

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">

      <!-- Define the Dispatcher Servlet bean -->
      <bean id="telusko" class="org.springframework.web.servlet.DispatcherServlet">
         <property name="contextConfigLocation" value="/WEB-INF/telusko-servlet.xml"/>
      </bean>

   </beans>
   ```

   - In this configuration, `telusko` is the ID for the Dispatcher Servlet bean.
   - `contextConfigLocation` specifies the location of the configuration file for the Dispatcher Servlet.

5. **Controller Scanning Configuration:**
   - Configure the Dispatcher Servlet to scan for controllers.
   - Add the `<context:component-scan>` element inside the `<beans>` tag.

   ```xml
   <context:component-scan base-package="com.telusko.SpringMVCDemo"/>
   ```

   - This scans the specified package for Spring components, including controllers.

6. **View Resolver Configuration:**
   - Configure the view resolver to resolve JSP views.
   - Add the `<bean>` element for `InternalResourceViewResolver`.

   ```xml
   <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      <property name="prefix" value="/views/"/>
      <property name="suffix" value=".jsp"/>
   </bean>
   ```

   - This configuration sets the prefix and suffix for resolving JSP views.

7. **Place the Configuration File:**
   - Move the `telusko-servlet.xml` file to the `WEB-INF` folder.
   - This location is specified in the `contextConfigLocation` property of the Dispatcher Servlet.
   The file is placed in the WEB-INF folder, and its name corresponds to the Servlet name (telusko) with a -servlet.xml suffix.

8. **Re-run the Project:**
   - Restart the Tomcat server in Eclipse.
   - Right-click on the project, choose `Run As` -> `Run on Server`.

9. **Access the Application:**
   - Open a web browser and navigate to `http://localhost:8080/SpringMVCDemo/`.
   - The application should now display the index page without errors.

## Conclusion:
In this section, we addressed the internal server error by configuring the Dispatcher Servlet. The `telusko-servlet.xml` file is crucial for specifying Dispatcher Servlet configurations, including component scanning and view resolver settings. The application should now run successfully, and further controllers can be added and configured within the Spring MVC structure.


# **Next Section: Project**
Certainly! Let's create detailed notes based on the provided transcript for Chapter 1.

### Chapter 1: Overview and Project Introduction

- **Summary:**
  - Discussion on topics covered so far, focusing on Spring and Spring Boot.
  - Emphasis on building a web application using the Spring framework.
  - Introduction to JavaServer Pages (JSP) for building views in a Spring project.
  - Project description: a job application or portal for job posting and searching.

- **User Interactions:**
  - Users can view job listings.
  - Job details include title, experience, and technology stack.
  - Functionality for job seekers to apply and companies to post listings.

- **Visual Representation:**
  - Visual representation of job listings and their details.
  - Example job listings for Java and front-end developers.

- **Project Setup:**
  - Usage of Spring Boot for project creation.
  - Project created using Spring Initializer (`start.spring.io`).
  - Dependencies added: Spring Web and Lombok.

#### Example Code for Project Setup:

```java
// JobController.java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class JobController {

    @RequestMapping({"/", "/home"})
    public String home() {
        return "home";
    }

    @RequestMapping("/addJob")
    public String addJob() {
        return "addJob";
    }

    // More mappings and methods will be added as the project progresses
}
```

### Chapter 1 (Continued): Views and Frontend Setup

- **Introduction to Views:**
  - Emphasis on building the backend before focusing on views.
  - Views stored in the `webapp` folder with CSS files.

- **Maven Project Dependencies:**
  - Addition of dependencies for JSP and JSTL (JavaServer Pages Standard Tag Library).
  - Explanation of Jasper for converting JSP pages.

#### Example Code for Maven Dependencies:

```xml
<!-- pom.xml -->
<dependencies>
    <!-- Other dependencies -->
    <dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-jasper</artifactId>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
    </dependency>
</dependencies>
```

- **Creation of Views:**
  - Placement of views in the `webapp` folder.
  - Example of JSP pages with basic structure.

#### Example Code for JSP Views:

```jsp
<!-- home.jsp -->
<html>
    <!-- HTML structure with dynamic content -->
</html>
```

### Chapter 1 (Continued): Backend Development

- **Building the Controller:**
  - Creation of a `JobController` class annotated with `@Controller`.
  - Methods for handling different URLs, including home and addJob.
  - Mapping URLs to these methods.

#### Example Code for Controller:

```java
// JobController.java
@Controller
public class JobController {

    @RequestMapping({"/", "/home"})
    public String home() {
        return "home";
    }

    @RequestMapping("/addJob")
    public String addJob() {
        return "addJob";
    }

    // More mappings and methods will be added as the project progresses
}
```
### Chapter 1 (Continued): Form Handling and Model Class

#### Form Handling in JobController:

In this part of Chapter 1, the instructor focuses on handling the form submission, specifically for a POST request. The goal is to create a method in the `JobController` named `handleForm`. This method is designed to handle the data submitted through the form and return the success page.

```java
// JobController.java
@PostMapping("/handleForm")
public String handleForm(@RequestParam("postId") String postId,
                         @RequestParam("postProfile") String postProfile,
                         @RequestParam("postDescription") String postDescription,
                         @RequestParam("requiredExperience") String requiredExperience,
                         @RequestParam("techStack") List<String> techStack) {
    // Logic to handle the form submission
    // Processing form data, saving to a database, etc.

    // For now, just print the data to the console
    System.out.println("Post ID: " + postId);
    // Additional processing code can be added here
    return "success";
}
```

#### Introduction to Model Class (jobPost):

The instructor introduces the concept of a model class named `jobPost`. This class is essential for encapsulating the data entered through the form. The class includes private variables for postId, postProfile, postDescription, requiredExperience, and techStack. Additionally, the instructor utilizes Lombok annotations to reduce boilerplate code.

```java
// jobPost.java (inside the 'model' package)
import lombok.Data;

@Data
public class jobPost {
    private String postId;
    private String postProfile;
    private String postDescription;
    private String requiredExperience;
    private List<String> techStack;
}
```

By using Lombok's `@Data` annotation, the need for explicit getters, setters, toString, and other methods is eliminated. The class is also annotated with `@Component` to make it a Spring component for easy dependency injection.

#### Handling Model Class in Success Page:

The instructor revisits the success.jsp page, emphasizing the need for the `jobPost` class to display the form data properly. The usage of `request.getAttribute("jobPost")` is demonstrated to retrieve the `jobPost` object and display its attributes in the HTML.

```jsp
<!-- success.jsp -->
<%@ page import="com.telusko.JobApp.model.jobPost" %>

<%
    jobPost post = (jobPost) request.getAttribute("jobPost");
%>

<html>
    <!-- Displaying data from the jobPost object -->
    <body>
        <h2>Details of the Submitted Job Post:</h2>
        <p>Post ID: <%= post.getPostId() %></p>
        <p>Profile: <%= post.getPostProfile() %></p>
        <p>Description: <%= post.getPostDescription() %></p>
        <p>Required Experience: <%= post.getRequiredExperience() %></p>
        <p>Tech Stack: <%= post.getTechStack() %></p>
    </body>
</html>
```

#### Troubleshooting and Final Outcome:

The instructor encounters and addresses issues related to package imports and resolves them to ensure the successful execution of the form submission. The final output demonstrates the ability to capture form data, process it in the controller, and display the details on the success page.

#### Conclusion of Form Handling:

Chapter 1, in this segment, dives into the practical aspects of handling form submissions in a Spring web application. The utilization of the `jobPost` model class and Lombok annotations simplifies the code structure. The successful display of form data on the success page marks a crucial step in the development process, setting the stage for further enhancements in the upcoming chapters.

### Chapter 1 (Continued): Data Storage and Viewing in Service and Repo Layers

#### Introduction to Data Storage:

In this section, the instructor addresses the need for data storage after form submission. While recognizing the absence of a database, the decision is made to use an array list to temporarily store the job post data. The data storage responsibilities are divided into different layers: Controller, Service, and Repository.

#### Creating Service and Repository Layers:

1. **Job Service Layer:**
   - A new class named `JobService` is created in the `service` package.
   - This class includes two methods: `addJob` and `getAllJobs`.
   - The `addJob` method takes a `jobPost` object and adds it to the list of jobs.
   - The `getAllJobs` method returns a list of all job posts.

```java
// JobService.java (inside the 'service' package)
import org.springframework.stereotype.Service;

@Service
public class JobService {
    // List to temporarily store job posts
    private List<jobPost> jobs = new ArrayList<>();

    public void addJob(jobPost job) {
        jobs.add(job);
        // Additional processing or logging can be added here
    }

    public List<jobPost> getAllJobs() {
        return jobs;
    }
}
```

2. **Job Repository Layer:**
   - A new class named `JobRepo` is created in the `repo` package.
   - This class includes a method `getAllJobs` that returns the list of job posts.
   - The `JobRepo` class is designated as a repository using the `@Repository` annotation.

```java
// JobRepo.java (inside the 'repo' package)
import org.springframework.stereotype.Repository;

@Repository
public class JobRepo {
    // List to simulate data storage
    private List<jobPost> jobs = new ArrayList<>();

    public List<jobPost> getAllJobs() {
        return jobs;
    }
}
```

#### Connecting Layers and Wiring Dependencies:

The instructor emphasizes the need for interaction between the controller, service, and repository layers. The `JobController` now includes an autowired instance of `JobService`. The controller calls methods on the service layer, which, in turn, interacts with the repository layer.

```java
// JobController.java
@RestController
@RequestMapping("/jobs")
public class JobController {
    // Autowiring the JobService instance
    @Autowired
    private JobService jobService;

    // Mapping to handle form submission
    @PostMapping("/handleForm")
    public String handleForm(@ModelAttribute("jobPost") jobPost job) {
        // Adding the job post data through the service layer
        jobService.addJob(job);
        // Additional logic or redirection can be added here
        // Logging the entire list for verification
        System.out.println("All Jobs: " + jobService.getAllJobs());
        return "success";
    }
}
```

#### Recap of Data Flow:

1. **Controller Layer:**
   - Handles the form submission.
   - Communicates with the service layer.

2. **Service Layer:**
   - Manages business logic and processing.
   - Utilizes a repository instance for data storage.

3. **Repository Layer:**
   - Simulates data storage in the absence of a database.
   - Provides methods to retrieve data.

### Chapter 1 (Continued): Connecting View to Controller and Data Verification

#### Working with the View:

The instructor moves on to addressing the issue with viewing all jobs. Clicking on "All Jobs" currently leads to a 404 error, indicating that the view is not yet implemented. To resolve this, the controller needs to handle the view for all jobs.

#### Creating Controller for Viewing All Jobs:

1. **Job Controller Modification:**
  - A new method is added to the `JobController` to handle the view for all jobs.
  - The method returns a string representing the view name, and it's mapped to the URL `/viewalljobs`.

```java
// JobController.java
@RestController
@RequestMapping("/jobs")
public class JobController {
   // ... (existing code)

   // Mapping to handle viewing all jobs
   @GetMapping("/viewalljobs")
   public String viewAllJobs(Model model) {
       // Retrieving all jobs from the service layer
       List<jobPost> jobs = jobService.getAllJobs();
       // Adding job posts to the model for view rendering
       model.addAttribute("jobposts", jobs);
       return "viewalljobs";
   }
}
```

#### Updating the View Template:

1. **View Template Modification:**
  - The instructor mentions that the view name for viewing all jobs is "viewalljobs."
  - The `viewalljobs.jsp` template is expected to render the job posts.

```jsp
<!-- viewalljobs.jsp -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
   <title>All Jobs</title>
</head>
<body>
   <h2>All Jobs</h2>
   <table border="1">
       <tr>
           <th>ID</th>
           <th>Profile</th>
           <th>Description</th>
           <th>Experience</th>
           <th>Tech Stack</th>
       </tr>
       <!-- Looping through the job posts and displaying them -->
       <c:forEach items="${jobposts}" var="jobpost">
           <tr>
               <td>${jobpost.postId}</td>
               <td>${jobpost.postProfile}</td>
               <td>${jobpost.postDescription}</td>
               <td>${jobpost.requiredExperience}</td>
               <td>${jobpost.techStack}</td>
           </tr>
       </c:forEach>
   </table>
</body>
</html>
```

#### Data Verification:

The instructor emphasizes the importance of sending data to the view. The `JobController` fetches all job posts from the service layer and adds them to the model, which is then used by the view template for rendering. The instructor demonstrates the successful implementation by restarting the application and navigating to the "All Jobs" view, where the data is displayed correctly.

#### Future Steps:

The instructor briefly touches upon the upcoming sessions where they will explore connecting the application with React, creating a separate front end, and introducing a database layer for persistent data storage. The focus is on achieving modularity and ease of integration for future enhancements.
