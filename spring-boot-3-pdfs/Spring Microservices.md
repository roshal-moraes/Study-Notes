# Chapter 1: Understanding Microservices

## Introduction
- The video focuses on exploring the concept of microservices.
- The discussion traces back to the 1970s when computers were introduced.

## Evolution of Computer Use
- Initially, computers were used for simple, often scientific, problems.
- In the 1970s, the successful application of computers for scientific and research purposes led to consideration for commercial use.
- In the early to late 90s, companies began adopting software and computers for internal purposes.
- Social media websites emerged in the early 2000s.

## Software's Ubiquitous Role
- The video emphasizes that software plays a crucial role in various aspects of life today.
- Examples include booking cabs, transferring money, and online shopping.

## Building Websites and Services
- Websites are considered as applications, and services are the functionalities within them.
- The example of Amazon is used, where various services like product search, buying, cart management, and selling are present.

## Traditional Approach to Software Development
- The traditional mindset involves breaking down a project into smaller modules.
- Teams are divided to work on specific features, creating a structured development approach.

## Monolithic Architecture
- Monolithic applications encompass all services within one package.
- Advantages include ease of understanding and deployment.

## Drawbacks of Monolithic Architecture
1. **Team Dependencies:**
   - Teams need to coordinate and depend on each other, leading to potential delays in releases.
   - Example Code: Coordination for release dates in a monolithic setup.
     ```java
     // Team coordination for release date
     String releaseDate = "2024-02-01";
     ```

2. **Scalability Challenges:**
   - Scaling the entire application, even when specific services require more resources.
   - Example Code: Scaling an entire application for increased traffic.
     ```java
     // Scaling the entire application
     int numberOfServers = 5;
     scaleApplication(numberOfServers);
     ```

3. **Technology Constraints:**
   - Limited flexibility in choosing technologies as the entire application is built on a single technology stack.
   - Example Code: Limitation in using different technologies within a monolithic application.
     ```java
     // Using a single technology stack
     String technologyUsed = "Java";
     ```

## Introduction to Microservices
- Microservices are introduced as an alternative architectural approach.
- Each service is self-contained, deployable, and scalable independently.
- Services can be built using different technologies.

## Advantages of Microservices
1. **Team Independence:**
   - Teams can work on individual services independently.
   - Example Code: Independent development of microservices.
     ```java
     // Team working on a microservice
     Microservice productSearchService = new Microservice("Product Search");
     ```

2. **Scalability at the Service Level:**
   - Microservices allow scaling specific services based on demand.
   - Example Code: Scaling a specific microservice.
     ```java
     // Scaling a microservice
     Microservice paymentService = new Microservice("Payment");
     paymentService.scale(10);
     ```

3. **Technology Diversity:**
   - Different services can be built using different technologies.
   - Example Code: Using diverse technologies for microservices.
     ```java
     // Using diverse technologies
     Microservice javaService = new Microservice("Java Service");
     Microservice nodeJsService = new Microservice("Node.js Service");
     ```

4. **Fault Isolation:**
   - Failure in one microservice does not affect the entire application.
   - Example Code: Handling a service failure gracefully.
     ```java
     // Handling service failure
     try {
         // Code for microservice operation
     } catch (ServiceException ex) {
         // Handle service-specific exception
     }
     ```

## Challenges of Microservices
1. **Communication Complexity:**
   - Microservices require effective communication, often addressed through service discovery, API gateways, and resilience systems.
   - Example Code: Implementing service discovery in microservices.
     ```java
     // Service discovery setup
     ServiceDiscovery discoverer = new ServiceDiscovery();
     discoverer.registerService(productSearchService);
     ```

2. **Architectural Design Importance:**
   - Designing the architecture is crucial before coding.
   - Poorly designed microservices can lead to a system resembling a monolithic application.
   - Example Code: Planning microservices architecture.
     ```java
     // Microservices architectural design
     MicroserviceArchitecture design = new MicroserviceArchitecture();
     design.defineServices("User Management", "Order Processing", "Inventory");
     ```

3. **Security Considerations:**
   - Security becomes complex with multiple services.
   - Access control and authentication need careful consideration.
   - Example Code: Implementing security measures in microservices.
     ```java
     // Security measures in microservices
     SecurityManager manager = new SecurityManager();
     manager.authenticate(userRequest);
     ```

## Conclusion
- Microservices offer flexibility, scalability, and independence but come with communication and design challenges.
- Properly designed microservices enhance the overall system architecture.
- Despite challenges, microservices are considered a valuable architectural approach.

# Chapter 2: Cloud Computing

## Introduction to Cloud Computing
- **Definition**: Cloud computing refers to the use of a network of remote servers on the internet to store, manage, and process data, rather than a local server or a personal computer.
- **Historical Perspective**: Initially, software development involved building static websites using HTML, CSS, and JavaScript. Dynamic websites required the setup of web servers, which led to challenges such as managing servers, handling power outages, and ensuring continuous accessibility.

## Challenges with On-Premise Solutions
- **On-Premise Issues**:
  - Building and maintaining servers involves significant investment.
  - Managing hardware, networking, and storage requires technical expertise.
  - Power failures, hardware issues, and updates can disrupt services.
  - Scaling infrastructure for sudden increases in demand is cumbersome.

## Evolution of Cloud Computing
- **Purpose of Cloud Computing**: Overcoming challenges associated with on-premise solutions.
- **Basic Concept**: Instead of managing servers locally, users leverage computing resources provided by third-party cloud service providers.

## Key Concepts and Components
- **Public Cloud**: Cloud resources are owned and operated by a third-party cloud service provider and are made available to the general public.
- **Private Cloud**: Cloud resources are used exclusively by a single business or organization.
- **Hybrid Cloud**: Combines public and private cloud services to achieve a balance between cost efficiency and data privacy.

## Service Models in Cloud Computing
1. **IaaS (Infrastructure as a Service)**:
   - Provides virtualized computing infrastructure over the internet.
   - Users manage operating systems, applications, and data.
   - Example: Amazon EC2.

2. **PaaS (Platform as a Service)**:
   - Offers a platform allowing customers to develop, run, and manage applications without dealing with the complexity of building and maintaining the infrastructure.
   - Example: Google App Engine.

3. **CaaS (Container as a Service)**:
   - Provides a platform for users to deploy, manage, and scale containerized applications.
   - Example: Kubernetes.

4. **FaaS (Function as a Service)**:
   - Focuses on executing individual functions or units of code in response to events.
   - Example: AWS Lambda.

5. **SaaS (Software as a Service)**:
   - Delivers software applications over the internet, eliminating the need for users to install, manage, and maintain the software locally.
   - Example: Google Workspace.

## Benefits of Cloud Computing
1. **Cost-Efficiency**:
   - Pay-as-you-go model reduces upfront costs.
   - No need to invest in physical infrastructure.

2. **Scalability**:
   - Easily scale resources up or down based on demand.

3. **Accessibility**:
   - Enables remote access to applications and data.

4. **Uptime and Reliability**:
   - Cloud providers offer high levels of availability and reliability.

5. **Security**:
   - Cloud providers implement robust security measures.

## Considerations and Challenges
- **Security Concerns**:
  - Storing sensitive data on public clouds raises security considerations.
- **Cost Management**:
  - Improper resource allocation can lead to unexpected costs.
- **Regulatory Compliance**:
  - Compliance with industry-specific regulations may pose challenges.

## Conclusion
- **Adoption Trends**: Many companies are transitioning from on-premise solutions to cloud computing.
- **Flexibility and Optimization**: Choosing the right mix of cloud services (public, private, hybrid) is crucial for optimizing costs and addressing specific business needs.
- **Continuous Evolution**: Cloud computing continues to evolve, providing innovative solutions for various industries and applications.

### Example Code (Cloud Service Usage)
```python
# Example Python code using a cloud service (AWS S3)

import boto3

# Create an S3 client
s3 = boto3.client('s3')

# Upload a file to S3 bucket
s3.upload_file('local_file.txt', 'my_bucket', 'remote_file.txt')

# Download a file from S3 bucket
s3.download_file('my_bucket', 'remote_file.txt', 'downloaded_file.txt')
```

Note: The above code uses the AWS SDK for Python (Boto3) and assumes the existence of an AWS S3 bucket. Adjustments may be needed based on the specific cloud service used.

# Chapter 3: Cloud-Native and 12-Factor App

## Cloud-Native Applications
- Enterprise developer applications used to be on-premise, deployed on company-owned servers.
- With the rise of the cloud, many companies are transitioning from on-premise to cloud-based services.
- Cloud services offer benefits in terms of cost, scaling, and fewer issues.
- Both public and internal applications are now commonly deployed on the cloud.

### Cloud-Ready vs. Cloud-Native
- Cloud-Ready: Existing applications are adapted to run on the cloud. Changes like handling environment variables or configuration files are made to facilitate cloud deployment.
- Cloud-Native: New applications are built specifically for the cloud, leveraging its features for enhanced effectiveness.

### Cloud-Native Application Building
- Cloud-native applications are meant for the cloud environment.
- Developers can follow standards and rules to build cloud-native applications.
- The 12-factor app is a set of rules created by Heroku to guide the development of cloud-native applications.

## The 12 Factors of Cloud-Native Applications

### 1. Codebase
- A cloud-native application should have one codebase.
- The codebase should be tracked in a revision control system like Git.
- Multiple deployments (environments such as development, staging, production) can exist for a single codebase.

**Example Code:**
```bash
git init
git add .
git commit -m "Initial commit"
```

### 2. Dependencies
- Dependencies should not be bundled with the codebase.
- Dependencies should be declared in a separate manifest file.
- Manifest files (e.g., Maven in Java) specify dependencies and versions.

**Example Code (Maven):**
```xml
<dependencies>
  <dependency>
    <groupId>groupID</groupId>
    <artifactId>dependencyID</artifactId>
    <version>1.0</version>
  </dependency>
</dependencies>
```

### 3. Configuration
- Configuration settings (e.g., database connection details) should be stored outside the codebase.
- Use environment files or other external configurations.
- This separation allows for easy changes without modifying the source code.

**Example Code (Environment File):**
```env
DB_URL=your_database_url
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

### 4. Backing Services
- Backing services (databases, third-party services) should be loosely coupled with the application.
- Treat these services as resources that can be easily changed or replaced.
- Avoid hardcoding specific services in the application code.

### 5. Build, Release, Run
- Follow a three-step process: build, release, and run.
- Build the application into a package.
- Release includes configuration and environment variables.
- Run deploys the released version to the target environment.

**Example Code (Build and Release):**
```bash
# Build
mvn clean install

# Release
java -jar target/app.jar
```

### 6. Processes
- Adopt stateless processes instead of stateful ones.
- Stateless processes do not store data, improving scalability and reliability.
- Data should be retrieved from external resources, such as databases.

### 7. Port Binding
- Assign a unique port number to each service.
- Services are identified by their port numbers.
- Enables easy communication between services and scaling.

**Example Code:**
```java
// Server listening on port 8080
const server = app.listen(8080, () => {
  console.log('Server is running on port 8080');
});
```

### 8. Concurrency
- Instead of vertical scaling (increasing resources on a single machine), opt for horizontal scaling (adding more instances).
- Build applications that can scale out by creating multiple instances.

### 9. Disposability
- Ensure fast startup and graceful shutdown.
- Dispose of services easily without losing data.
- Close connections and resources properly during shutdown.

### 10. Dev/Prod Parity
- Keep development, staging, and production environments as similar as possible.
- Reduce gaps between development and production by deploying frequently.
- Embrace DevOps culture for collaboration between development and operations teams.

### 11. Logs
- Generate logs for each process in the application.
- Aggregate logs in a centralized location for monitoring.
- Use proper logging services to handle logs effectively.

### 12. Admin Processes
- Provide means for external administration of the application.
- Expose ports or use services for administrative tasks.
- Facilitates remote management of the application.

**Example Code (Admin Process):**
```python
# Flask web app for administration
@app.route('/admin', methods=['GET'])
def admin():
    return 'Admin Page'
```

Following the 12-factor app principles ensures the development of cloud-native applications that are scalable, maintainable, and efficient.

Certainly! Here's the detailed and well-formatted notes along with example code:

# Chapter 4: Building a Monolithic Application

## Introduction
In this chapter, we embark on the journey of building a monolithic application and later explore the process of converting it into a microservices architecture. The focus will be on understanding how to create microservices, the technologies involved, and the tools used to build and connect microservices.

### Key Points:
- Microservices architecture
- Building a monolithic application first
- Transitioning from monolithic to microservices

## Application Focus
The current objective is to create a monolithic application. Although the examples provided can be diverse, the emphasis is on using common tools. The series aims to keep examples simple while concentrating on essential tools for microservices development.

### Key Points:
- Common tools for microservices development
- Keeping examples simple

## Monolithic Application: Quiz Service
For the practical demonstration, the creation of a quiz service within the monolithic application is initiated. The quiz application involves a user receiving a set of questions, attempting multiple-choice questions (MCQs), and obtaining a score based on their choices.

### Key Points:
- User interaction in the quiz application
- PostgreSQL as the chosen database
- Java Spring Boot application for implementation

## Steps to Build the Quiz Service
The initial set of videos will guide the process of creating the monolithic application. The plan includes building a quiz service and subsequently breaking it down into microservices. The objective is to understand the dynamics of microservices, their functionality, and the tools required.

### Key Points:
- Creating a monolithic application
- Breaking down the application into microservices
- Understanding the power and functionality of microservices

## Starting with the Monolithic Application
The current video focuses on the monolithic application, specifically the creation of a quiz service. The functionality involves users accessing the application, receiving a set of questions, and submitting their answers to obtain a score.

### Key Points:
- Designing a simple project for the monolithic application
- Using Spring Initializr to set up the project
- Selecting project specifications (Maven, Java 17, Spring Boot 3.1)
- Configuring project dependencies (Spring Web, PostgreSQL, Data JPA, Lombok)

## Creating the Question Controller
The first step in implementing the quiz service is to create a QuestionController. This controller will handle incoming requests related to questions and is responsible for retrieving, updating, deleting, and reading questions.

### Key Points:
```java
// QuestionController.java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/question")
public class QuestionController {

    @GetMapping("/allQuestions")
    public String getAllQuestions() {
        return "Hi, these are your questions.";
    }
}
```

## Configuring the Database
Integration with PostgreSQL is a crucial aspect of the monolithic application. Configuring the database involves setting up the necessary properties in the application's POM file. This includes specifying the driver class, URL, username, password, and other relevant properties.

### Key Points:
```xml
<!-- pom.xml -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>

<!-- application.properties -->
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/questiondb
spring.datasource.username=postgres
spring.datasource.password=0000
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

## Running and Testing the Application
Running the Spring Boot application is essential for testing its functionality. The video demonstrates how to resolve potential issues, such as Lombok-related errors, and ensures the successful execution of the monolithic application.

### Key Points:
- Running the Spring Boot application
- Troubleshooting Lombok-related issues
- Confirming successful execution in the browser

## Conclusion and Next Steps
The video concludes with a brief overview of the achieved progress. The subsequent videos in the series will delve deeper into implementing additional functionalities within the monolithic application, including retrieving actual questions from the database.

### Key Points:
- Overview of progress in the current video
- Anticipation of future videos focusing on Cloud Operations in the Question Controller

# Chapter 5: Enhancing the Quiz Service

## Introduction
In this chapter, we'll focus on enhancing the Quiz Service by implementing features that allow users to fetch questions based on categories and add new questions to the database. We'll delve into the layers of the application, introducing the service layer and DAO (Data Access Object) layer to handle business logic and database interactions.

### Key Points:
- Enhancing features of the Quiz Service
- Implementing category-based question retrieval
- Adding new questions to the database

## Fetching Questions by Category
The current goal is to enable users to retrieve questions based on specific categories. This involves extending the functionality of the existing `QuestionService` to accommodate category-based queries.

### Key Points:
- Extending the `QuestionService` for category-based retrieval
- Introducing a method to fetch questions by category
- Utilizing the existing `QuestionDao` interface

## Implementing the Category Query
To achieve category-based question retrieval, modifications are made to the `QuestionService` class. A new method, `getQuestionsByCategory`, is introduced to handle queries related to a specific category.

### Key Points:
```java
// QuestionService.java
public List<Question> getQuestionsByCategory(String category) {
    return questionDao.findByCategory(category);
}
```

## Introducing the Category Parameter
Incorporating the category parameter in the `QuestionController`, the video guides users through the process of modifying the existing method to accept category-based requests.

### Key Points:
```java
// QuestionController.java
@GetMapping("/questionsByCategory")
public List<Question> getQuestionsByCategory(@RequestParam String category) {
    return questionService.getQuestionsByCategory(category);
}
```

## Testing Category-Based Retrieval
The demonstration includes testing the enhanced functionality by making requests for questions based on different categories. The importance of testing and ensuring the correctness of the implemented features is emphasized.

### Key Points:
- Making requests for questions by category
- Testing different categories for accurate retrieval
- Confirming the correctness of the implemented functionality

## Adding New Questions
Expanding the capabilities of the Quiz Service, the video explores the process of adding new questions to the database. Users will be guided through the steps of modifying the `QuestionService` and `QuestionController` to handle question addition.

### Key Points:
- Introducing the process of adding new questions
- Modifying the `QuestionService` to handle question addition
- Updating the `QuestionController` for accepting and processing new questions

## Implementing the Add Question Method
A new method, `addQuestion`, is introduced in the `QuestionService` class to facilitate the addition of new questions to the database. The process involves utilizing the `questionDao.save()` method provided by Spring Data JPA.

### Key Points:
```java
// QuestionService.java
public Question addQuestion(Question question) {
    return questionDao.save(question);
}
```

## Modifying the Controller for Adding Questions
The `QuestionController` is updated to include a new endpoint that allows users to submit new questions. The importance of proper request handling and validation is highlighted during the modification process.

### Key Points:
```java
// QuestionController.java
@PostMapping("/addQuestion")
public Question addQuestion(@RequestBody Question question) {
    return questionService.addQuestion(question);
}
```

## Testing the Addition of New Questions
The final part of the video involves testing the newly added functionality. Users are encouraged to submit sample questions through the provided endpoint to ensure the correct functioning of the question addition feature.

### Key Points:
- Submitting sample questions through the `/addQuestion` endpoint
- Verifying the addition of new questions in the database
- Ensuring the overall functionality of the enhanced Quiz Service

## Conclusion and Next Steps
The video concludes with a summary of the implemented features, emphasizing the significance of testing and validating each enhancement. Users are encouraged to explore additional improvements and functionalities on their own, setting the stage for future chapters exploring more advanced concepts in microservices development.

### Key Points:
- Summary of implemented features
- Importance of testing and validation
- Encouragement for further exploration and self-learning

# Chapter 6: Enhancements and Exception Handling

In this chapter, we will make enhancements to our existing application and introduce exception handling to handle potential errors.

## 6.1 Fetching Questions by Category

Previously, we implemented the functionality to fetch questions based on a specified category. We created a `getQuestionsByCategory` method in our controller. This method takes a category as a path variable and returns the list of questions belonging to that category.

```java
@GetMapping("/questions/category/{category}")
public List<Question> getQuestionsByCategory(@PathVariable String category) {
    return questionService.getQuestionsByCategory(category);
}
```

Remember that in the service layer, we utilized the JPA repository's magic query generation capabilities to fetch questions based on the provided category. This is possible because the JPA repository automatically creates queries based on method names.

## 6.2 Adding a New Question

Now, let's move on to adding a new question to our application. We created a method called `addQuestion` in our controller to handle this functionality. This method uses the `@PostMapping` annotation to handle HTTP POST requests. It expects a JSON payload representing a new question, which is annotated with `@RequestBody`.

```java
@PostMapping("/questions/add")
public ResponseEntity<String> addQuestion(@RequestBody Question question) {
    try {
        questionService.addQuestion(question);
        return new ResponseEntity<>("Question added successfully", HttpStatus.CREATED);
    } catch (Exception e) {
        return new ResponseEntity<>("Failed to add question: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

The `addQuestion` method utilizes the `questionService` to add the question to the database. It returns a response entity with an appropriate status code and message. If the addition is successful, it returns a `201 CREATED` status; otherwise, it returns a `500 INTERNAL_SERVER_ERROR` status with an error message.

## 6.3 Handling Exceptions

It's crucial to handle exceptions gracefully in a web application. We introduced a try-catch block to catch any exceptions that might occur during the addition of a question. If an exception occurs, we return an appropriate error message and status code.

In the catch block, you can customize the error message based on the type of exception caught. For instance, if the exception is related to duplicate keys or constraints, you can provide a more specific error message.

Handling exceptions properly ensures that our application is robust and can provide meaningful feedback to clients when errors occur.

## 6.4 Further Improvements

In the next chapter, we'll explore additional enhancements such as update and delete functionalities. Additionally, we'll dive deeper into exception handling, status codes, and response entities to make our application more robust and user-friendly.

Stay tuned for the next chapter where we'll continue to refine and improve our question and quiz application.

Chapter 7: Handling Exceptions and HTTP Response Status Codes

### 1. Overview of the Question Service
- Users can retrieve all questions.
- Questions can be filtered based on categories.
- New questions can be added.

### 2. Enhancements Planned
- Exception handling needs implementation.
- Introduction to HTTP response status codes.

### 3. HTTP Response Status Codes
- Status codes provide information about the result of the HTTP request.
- Common codes:
  - 1xx: Informational responses
  - 2xx: Successful responses (e.g., 200 for OK)
  - 3xx: Redirection messages
  - 4xx: Client errors (e.g., 404 for Not Found)
  - 5xx: Server errors (e.g., 500 for Internal Server Error)
- Developer responsibility to handle and display relevant messages to users.

### 4. Example Status Codes
- 200: OK (successful request)
- 201: Created (resource successfully created)
- 400: Bad Request (client error, e.g., invalid input)
- 401: Unauthorized (user not authenticated)
- 403: Forbidden (user does not have permission)
- 404: Not Found (resource not available)
- 405: Method Not Allowed (invalid HTTP method)
- 500: Internal Server Error (server-side issue)
- And more...

### 5. Implementation in Code
- Use `ResponseEntity` to return both data and status code.
- Example for fetching all questions:
  ```java
  @GetMapping("/questions")
  public ResponseEntity<List<Question>> getAllQuestions() {
      try {
          List<Question> questions = questionService.getAllQuestions();
          return new ResponseEntity<>(questions, HttpStatus.OK);
      } catch (Exception e) {
          e.printStackTrace();
          return new ResponseEntity<>(new ArrayList<>(), HttpStatus.BAD_REQUEST);
      }
  }
  ```
- Example for adding a question:
  ```java
  @PostMapping("/add")
  public ResponseEntity<String> addQuestion(@RequestBody Question question) {
      try {
          String result = questionService.addQuestion(question);
          return new ResponseEntity<>(result, HttpStatus.CREATED);
      } catch (Exception e) {
          e.printStackTrace();
          return new ResponseEntity<>("Error adding question", HttpStatus.BAD_REQUEST);
      }
  }
  ```

### 6. Importance of Handling Exceptions
- Ensures graceful error handling.
- Users receive meaningful error messages.
- Demonstrated through try-catch blocks.

### 7. Handling Exceptions in Service Layer
- Exception handling in service layer for `getAllQuestions` method.
- Returning an empty list and setting the status to `BAD_REQUEST` in case of an exception.

### 8. Conclusion
- Demonstrated the use of `ResponseEntity` for handling both data and status codes.
- Importance of exception handling in providing a robust user experience.
- Prepared for the next chapter on creating a quiz application using the question service.

# Chapter 8: Creating a Quiz Controller

### 1. Introduction to Quiz Operations
- CRUD operations for questions include create, read, update, and delete.
- Focus on creating a quiz for the quiz application.

### 2. Quiz Controller
- Purpose: Handle quiz-related operations.
- Initial operations include creating a quiz.
- Example URL: `/quiz/create?category=Java&numQ=5&title=JQuiz`
  - Accepts category, number of questions, and title as parameters.

### 3. Controller Implementation
- Create a new `QuizController` class annotated with `@RestController`.
- Define a method for creating a quiz using `@PostMapping` and `@RequestParam`.
- Example:
  ```java
  @RestController
  @RequestMapping("/quiz")
  public class QuizController {

      @PostMapping("/create")
      public ResponseEntity<String> createQuiz(
              @RequestParam String category,
              @RequestParam int numQ,
              @RequestParam String title) {
          // Logic for creating a quiz
          return new ResponseEntity<>("Quiz created successfully", HttpStatus.OK);
      }
  }
  ```

### 4. Quiz Creation Logic
- Quiz creation logic will be handled by the Quiz Service (not implemented yet).
- The controller delegates the creation task to the service layer.

### 5. URL and Parameters
- URL: `/quiz/create`
- Parameters:
  - `category`: Category of questions for the quiz.
  - `numQ`: Number of questions to

# Chapter 9: Fetching Quiz Data and Database Mapping

### 1. Obtaining Quiz Data
- Objective: Retrieve quiz data to display actual quizzes.
- Responsible entity: QuizService.

### 2. Introduction to QuizService
- QuizService to handle the creation and retrieval of quizzes.
- Create a QuizService class and instantiate an object in the controller.

### 3. Database Mapping for Quizzes and Questions
- Mapping quizzes and questions in the database.
- Introducing QuizDao interface using Spring Data JPA.
- Establishing relationships between quizzes and questions (Many-to-Many).
- Creation of a Quiz class with fields: id, title, and a list of questions.

#### Example Code:

```java
// Quiz Class
@Entity
@Data
public class Quiz {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    private String title;

    @ManyToMany
    @JoinTable(
        name = "quiz_question",
        joinColumns = @JoinColumn(name = "quiz_id"),
        inverseJoinColumns = @JoinColumn(name = "question_id")
    )
    private List<Question> questions;
}
```

```java
// QuizDao Interface
@Repository
public interface QuizDao extends JpaRepository<Quiz, Integer> {
}
```

### 4. QuizService Implementation
- QuizService responsible for creating and retrieving quizzes.
- Retrieving random questions by category from QuestionDao.

#### Example Code:

```java
@Service
public class QuizService {

    @Autowired
    private QuizDao quizDao;

    @Autowired
    private QuestionDao questionDao;

    public ResponseEntity<String> createQuiz(String category, int numQ, String title) {
        // Logic to create quiz and save to the database
        Quiz quiz = new Quiz();
        quiz.setTitle(title);

        List<Question> questions = questionDao.findRandomQuestionsByCategory(category, numQ);
        quiz.setQuestions(questions);

        quizDao.save(quiz);

        return new ResponseEntity<>("Quiz created successfully", HttpStatus.CREATED);
    }
}
```

### 5. Retrieving Random Questions by Category
- Introduction to native queries for custom queries.
- Writing a native query to fetch random questions by category and limit.

#### Example Code:

```java
// QuestionDao Interface
@Repository
public interface QuestionDao extends JpaRepository<Question, Integer> {

    @Query(value = "SELECT * FROM question q WHERE q.category = :category ORDER BY RANDOM() LIMIT :numQ", nativeQuery = true)
    List<Question> findRandomQuestionsByCategory(@Param("category") String category, @Param("numQ") int numQ);
}
```

### 6. Handling Exceptions and Improving Response
- Error handling in the service layer.
- Returning appropriate HTTP status codes in case of errors.

#### Example Code:

```java
// Enhanced QuizService with Exception Handling
@Service
public class QuizService {

    @Autowired
    private QuizDao quizDao;

    @Autowired
    private QuestionDao questionDao;

    public ResponseEntity<String> createQuiz(String category, int numQ, String title) {
        try {
            // Logic to create quiz and save to the database
            Quiz quiz = new Quiz();
            quiz.setTitle(title);

            List<Question> questions = questionDao.findRandomQuestionsByCategory(category, numQ);
            quiz.setQuestions(questions);

            quizDao.save(quiz);

            return new ResponseEntity<>("Quiz created successfully", HttpStatus.CREATED);
        } catch (Exception e) {
            return new ResponseEntity<>("Error creating quiz: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }
}
```

### 7. Conclusion
- Successfully implemented the creation and storage of quizzes.
- Introduced relationships between quizzes and questions in the database.
- Next steps: Implementing quiz retrieval and enhancing error handling.

# Chapter 10: Fetching Quiz Questions

### 1. Database Tables
- Two new tables created: quiz and quiz questions.
- Quiz table with columns ID and title.
- Quiz questions table with columns quiz ID and question ID.

### 2. Quiz Controller Method for Fetching Questions
- Introducing a new method in the controller for fetching quiz questions.
- Type of request: GET
- Endpoint: `/quiz/get/{id}` where the ID is the quiz ID.

#### Example Code:

```java
@RestController
@RequestMapping("/quiz")
public class QuizController {

    @Autowired
    private QuizService quizService;

    @GetMapping("/get/{id}")
    public ResponseEntity<List<QuestionWrapper>> getQuizQuestions(@PathVariable Integer id) {
        return quizService.getQuizQuestions(id);
    }
}
```

### 3. Creating a QuestionWrapper Class
- Need to create a wrapper class for questions to exclude answer, difficulty level, and category.
- Fields include ID, title, option 1, option 2, option 3, and option 4.

#### Example Code:

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class QuestionWrapper {

    private Integer id;
    private String title;
    private String option1;
    private String option2;
    private String option3;
    private String option4;

}
```

### 4. QuizService Implementation for Fetching Quiz Questions
- Using the QuizDao to fetch a quiz by ID.
- Handling the case where the quiz may not be found (using Optional).
- Converting the list of questions to a list of QuestionWrapper.

#### Example Code:

```java
@Service
public class QuizService {

    @Autowired
    private QuizDao quizDao;

    public ResponseEntity<List<QuestionWrapper>> getQuizQuestions(Integer id) {
        Optional<Quiz> optionalQuiz = quizDao.findById(id);

        if (optionalQuiz.isPresent()) {
            Quiz quiz = optionalQuiz.get();
            List<Question> questionsFromDb = quiz.getQuestions();

            List<QuestionWrapper> questionsForUser = new ArrayList<>();
            for (Question q : questionsFromDb) {
                QuestionWrapper qw = new QuestionWrapper(
                        q.getId(),
                        q.getTitle(),
                        q.getOption1(),
                        q.getOption2(),
                        q.getOption3(),
                        q.getOption4()
                );
                questionsForUser.add(qw);
            }

            return new ResponseEntity<>(questionsForUser, HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }
}
```

### 5. Conclusion
- Successfully implemented the fetching of quiz questions.
- Introduced the concept of a wrapper class to exclude certain fields.
- Next steps: Implementing the calculation of the quiz score on the server side.

#Chapter 11: Submitting Quiz Responses and Calculating Score

### 1. Client-Side Submission
- Users submit quiz responses to the server to calculate their scores.
- Each response includes the question ID and the user's chosen response.

#### Example Code:

```json
{
  "quizId": 1,
  "responses": [
    { "id": 18, "response": "3" },
    { "id": 8, "response": "D" },
    { "id": 17, "response": "A" },
    { "id": 6, "response": "B" },
    { "id": 19, "response": "C" }
  ]
}
```

### 2. Controller Method for Submitting Quiz Responses
- Controller method to handle the submission of quiz responses.
- Endpoint: `/quiz/submit/{id}` where the ID is the quiz ID.

#### Example Code:

```java
@RestController
@RequestMapping("/quiz")
public class QuizController {

    @Autowired
    private QuizService quizService;

    @PostMapping("/submit/{id}")
    public ResponseEntity<Integer> submitQuiz(
        @PathVariable Integer id,
        @RequestBody List<Response> responses
    ) {
        return quizService.submitQuiz(id, responses);
    }
}
```

### 3. Creating a Response Class
- A new class to represent the response sent by the client.
- Includes question ID and the user's response.

#### Example Code:

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Response {

    private Integer id;
    private String response;

}
```

### 4. QuizService Implementation for Calculating Score
- Service method to calculate the user's score based on submitted responses.
- Compares user responses with the correct answers stored in the database.

#### Example Code:

```java
@Service
public class QuizService {

    @Autowired
    private QuizDao quizDao;

    public ResponseEntity<Integer> submitQuiz(Integer id, List<Response> responses) {
        Optional<Quiz> optionalQuiz = quizDao.findById(id);

        if (optionalQuiz.isPresent()) {
            Quiz quiz = optionalQuiz.get();
            List<Question> questions = quiz.getQuestions();

            int right = 0;
            for (int i = 0; i < responses.size(); i++) {
                Response userResponse = responses.get(i);
                Question question = questions.get(i);

                if (userResponse.getResponse().equals(question.getRightAnswer())) {
                    right++;
                }
            }

            return new ResponseEntity<>(right, HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }
}
```

### 5. Conclusion
- Users can submit quiz responses, and the server calculates and returns their scores.
- Demonstrates the flow of communication between the client and the server.
- The application structure is discussed, paving the way for microservices architecture.

#Chapter 12: Transitioning from Monolithic to Microservices

### 1. Understanding Microservices
- Transitioning from monolithic to microservices architecture.
- Discussion on the practical implementation of microservices in a project.

### 2. Introduction to the Quiz Application
- The project is called a quiz application.
- Initially built as a monolithic application to understand the transition process.

### 3. Components of the Quiz Application
- Two main controllers: `QuestionController` and `QuizController`.
- `QuestionController` manages CRUD operations for questions.
- `QuizController` handles quiz creation, retrieval, submission, and scoring.

### 4. Challenges in Monolithic Architecture
- In a monolithic application, all functionalities are tightly integrated.
- Scaling the entire application is cumbersome.
- Difficulty in decoupling components for independent scaling and management.

### 5. Transitioning to Microservices
- Goal: Decouple the Quiz and Question functionalities into separate microservices.
- Microservices communicate over the network rather than being tightly integrated.
- Each microservice will have its own database and functionalities.

### 6. Separation of Concerns
- Emphasis on breaking down functionalities into separate services.
- Example: QuestionService and QuizService will run independently.

### 7. Scaling Microservices
- Microservices can be scaled individually based on requirements.
- Load balancing is essential for distributing traffic across multiple instances of services.

### 8. Implementing Microservices Infrastructure
- Introduction to API gateways, load balancers, and service registries.
- API gateway serves as a single entry point for clients to access microservices.
- Service registry helps services locate and communicate with each other.
- Load balancers distribute incoming requests among multiple instances of services.

### 9. Handling Failures and Circuit Breakers
- Implementing fail-fast mechanisms in microservices architecture.
- Circuit breakers help prevent cascading failures by providing fallback mechanisms.

### 10. Future Plans
- Implementing microservices infrastructure components in subsequent videos.
- Building the first microservices: Question Microservice and Quiz Microservice.

### Conclusion
- Transitioning from monolithic to microservices architecture involves decoupling functionalities and implementing robust infrastructure.
- Microservices offer scalability, flexibility, and resilience, but require careful design and implementation.

# Chapter 13: Implementing Additional Endpoints

### 1. Introduction
- Focus on implementing three key endpoints: generate, getQuestions, and getScore.
- Explanation of how the quizService will interact with the questionService for these functionalities.

### 2. Implementing the "generate" Endpoint
- Using the `@GetMapping` annotation to define the endpoint.
- Creating a method named `getQuestionsForQuiz` that returns a list of integers (question ids).
- Accepting parameters for category name and number of questions using `@RequestParam`.
- Explaining that the quizService will call this endpoint to request questions for generating a quiz.

```java
@GetMapping("/generate")
public ResponseEntity<List<Integer>> getQuestionsForQuiz(
        @RequestParam String categoryName,
        @RequestParam int numberOfQuestions) {
    List<Integer> questionIds = questionService.getQuestionsForQuiz(categoryName, numberOfQuestions);
    return new ResponseEntity<>(questionIds, HttpStatus.OK);
}
```

### 3. Implementing the "getQuestions" Endpoint
- Using the `@PostMapping` annotation to define the endpoint.
- Creating a method named `getQuestions` that returns a list of `QuestionWrapper`.
- Accepting a list of question ids from the quizService.
- Iterating through the question ids, fetching questions from the database, and converting them into `QuestionWrapper`.

```java
@PostMapping("/getQuestions")
public ResponseEntity<List<QuestionWrapper>> getQuestions(
        @RequestBody List<Integer> questionIds) {
    List<QuestionWrapper> wrappers = questionService.getQuestionsFromId(questionIds);
    return new ResponseEntity<>(wrappers, HttpStatus.OK);
}
```

### 4. Implementing the "getScore" Endpoint
- Using the `@PostMapping` annotation to define the endpoint.
- Creating a method named `getScore` that returns an integer (the calculated score).
- Accepting a list of responses from the quizService.
- Iterating through the responses, fetching corresponding questions, and calculating the score.

```java
@PostMapping("/getScore")
public ResponseEntity<Integer> getScore(@RequestBody List<Response> responses) {
    int score = questionService.getScore(responses);
    return new ResponseEntity<>(score, HttpStatus.OK);
}
```

### 5. Explanation and Summary
- Explaining the purpose and flow of each implemented endpoint.
- Highlighting the communication between the quizService and questionService.
- Overview of the completed questionService with new functionalities.

### 6. Conclusion
- Recap of the progress made in implementing additional endpoints.
- Upcoming tasks, including integration testing and potential improvements.
- Anticipation of further development in the quizService.

# Chapter 14: Microservices and Instance Scaling

In this chapter, we explore the process of testing a microservice application, specifically focusing on a service related to pillow ideas. The goal is to create multiple instances of the service, simulating the scalability aspect of microservices.

## 1. **Creating Endpoints:**
   - Multiple endpoints were created, including:
     - Retrieving all questions.
     - Fetching questions by category.
     - Generating questions based on specific IDs.
     - Evaluating scores based on user responses.

   Example Code:
   ```java
   @RestController
   @RequestMapping("/question")
   public class QuestionController {
       // Endpoint for all questions
       @GetMapping("/all-questions")
       public List<Question> getAllQuestions() {
           // Implementation
       }

       // Endpoint for questions by category
       @GetMapping("/category/{category}")
       public List<Question> getQuestionsByCategory(@PathVariable String category) {
           // Implementation
       }

       // Endpoint for generating questions
       @PostMapping("/generate")
       public List<Question> generateQuestions(@RequestBody List<Integer> questionIds) {
           // Implementation
       }

       // Endpoint for getting scores
       @PostMapping("/get-score")
       public int getScore(@RequestBody List<Response> responses) {
           // Implementation
       }
   }
   ```

## 2. **Testing Endpoints with Postman:**
   - Demonstrated testing endpoints using Postman.
   - Tested retrieving questions, generating questions, and evaluating scores.

   Example Postman Requests:
   - Retrieve all questions:
     ```http
     GET http://localhost:8080/question/all-questions
     ```

   - Retrieve questions by category (e.g., "Java"):
     ```http
     GET http://localhost:8080/question/category/Java
     ```

   - Generate questions based on specific IDs (e.g., 2, 4, 7, 9, 5):
     ```http
     POST http://localhost:8080/question/generate
     Content-Type: application/json

     [2, 4, 7, 9, 5]
     ```

   - Get the score by submitting responses:
     ```http
     POST http://localhost:8080/question/get-score
     Content-Type: application/json

     [
       {"id": 1, "response": "A"},
       {"id": 2, "response": "B"},
       // ...
     ]
     ```

## 3. **Creating Multiple Instances:**
   - Explained the need for multiple instances in a microservices architecture.
   - Detailed the process of starting multiple instances with different port numbers.

   Example IntelliJ Configuration for Another Instance:
   ```shell
   -Dserver.port=8081
   ```

## 4. **Scaling Instances:**
   - Showcased the ability to run multiple instances simultaneously.
   - Demonstrated how to modify configuration for different port numbers.

   Example IntelliJ VM Options:
   ```shell
   -Dserver.port=8081
   ```

   - Illustrated how to interact with different instances using distinct port numbers.

## 5. **Future Considerations:**
   - Discussed the relevance of these concepts for a quiz service.
   - Emphasized the importance of choosing between single or multiple instances based on scalability needs.

   Example Future Steps:
   - Implementing a quiz service with considerations for multiple instances.

In conclusion, this chapter provides a comprehensive guide to testing microservices, creating multiple instances, and understanding the scalability options in a microservices architecture. The practical examples using Postman and IntelliJ enhance the learning experience.

# Chapter 15: Building the Quiz Microservice

In this chapter, the focus is on creating the second microservice - the quiz service. The intent is to establish communication between the quiz service and the question service to share data. Key functionalities include creating quizzes, retrieving questions for a specific quiz, and calculating scores.

## 1. **Overview:**
   - The quiz service is designed to interact with the client, handling quiz-related operations.
   - Communication with the question service is necessary for accessing question data.

## 2. **Setting Up Quiz Service:**
   - A new project is created using [start.spring.io](https://start.spring.io/).
   - Dependencies include web, data JPA, PostgreSQL, and Lombok.
   - A Maven project is generated with the artifact id as "quiz-service."

   Example Maven Dependencies:
   ```xml
   <dependencies>
       <!-- Other dependencies -->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-data-jpa</artifactId>
       </dependency>
       <dependency>
           <groupId>org.postgresql</groupId>
           <artifactId>postgresql</artifactId>
           <scope>runtime</scope>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <optional>true</optional>
       </dependency>
   </dependencies>
   ```

## 3. **Controller and Dependencies:**
   - The controller is established with endpoints for creating quizzes, retrieving questions, and calculating scores.
   - Unnecessary files copied from the monolithic application are removed.
   - The application.properties file is updated with database configurations.

   Example Quiz Controller Endpoint:
   ```java
   @RestController
   @RequestMapping("/quiz")
   public class QuizController {
       // Endpoint to create a quiz
       @PostMapping("/create")
       public Quiz createQuiz(@RequestBody Quiz quiz) {
           // Implementation
       }

       // Endpoint to get questions for a specific quiz
       @GetMapping("/{quizNumber}/questions")
       public List<Question> getQuestionsForQuiz(@PathVariable int quizNumber) {
           // Implementation
       }

       // Endpoint to get the score for a quiz submission
       @PostMapping("/{quizNumber}/submit")
       public int getScoreForQuiz(@PathVariable int quizNumber, @RequestBody List<Response> responses) {
           // Implementation
       }
   }
   ```

## 4. **Database Setup:**
   - The quiz database ("quizdb") is created in PostgreSQL using pgAdmin.
   - Necessary tables, including the "quiz" table, will be generated by the application based on entity classes.

## 5. **Refactoring Models and Services:**
   - Unnecessary files related to questions are removed.
   - Changes are made in the model to align with the quiz service's purpose.
   - Response class is retained for handling quiz submissions.

   Example Quiz Entity Class:
   ```java
   @Entity
   @Data
   public class Quiz {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private int id;

       // Other fields
   }
   ```

## 6. **Interacting with Question Service:**
   - The quiz service cannot directly access the question database.
   - Communication with the question service is established using Feign clients.
   - Feign clients are configured to interact with the question service's endpoints.

   Example Feign Client Configuration:
   ```java
   @FeignClient(name = "question-service", url = "http://localhost:8080")
   public interface QuestionClient {
       @GetMapping("/question/all-questions")
       List<Question> getAllQuestions();

       // Other endpoints
   }
   ```

## 7. **Next Steps:**
   - The groundwork for the quiz service is laid out.
   - Future steps involve implementing logic for quiz creation, question retrieval, and score calculation.
   - Continued collaboration with the question service for seamless data sharing.

In the upcoming sections, the focus will be on the detailed implementation of quiz service functionalities and establishing smooth communication with the question service.

# Chapter 15 (Continued): Building the Quiz Microservice

## 8. **Refactoring Existing Code:**
   - Copied code from the monolithic application needs adjustments for the new microservice structure.
   - Imports, dependencies, and unnecessary files are removed or commented out.
   - A new class `QuizDto` is introduced to handle quiz creation input more effectively.

   Example QuizDto Class:
   ```java
   @Data
   public class QuizDto {
       private String categoryName;
       private int numQuestions;
       private String title;
   }
   ```

## 9. **Interacting with Question Service:**
   - The challenge is connecting the QuizService to the QuestionService for question retrieval.
   - RestTemplate, a Spring class, is introduced for sending requests.
   - To enhance readability and simplicity, the Feign client concept is introduced.

   Example RestTemplate Usage:
   ```java
   RestTemplate restTemplate = new RestTemplate();
   String questionServiceUrl = "http://localhost:8080/question/generate";
   List<Integer> questionIds = restTemplate.getForObject(questionServiceUrl, List.class);
   ```

## 10. **Introducing Feign Client:**
   - Feign provides a declarative way to make HTTP requests.
   - It simplifies interaction with other microservices.
   - A Feign client interface is created, representing the QuestionService API.

   Example QuestionService Feign Client:
   ```java
   @FeignClient(name = "question-service", url = "http://localhost:8080")
   public interface QuestionClient {
       @GetMapping("/question/generate")
       List<Integer> generateQuestions();
   }
   ```

## 11. **Service Discovery with Eureka:**
   - Eureka server and Eureka clients are introduced for service discovery.
   - Eureka server is a registry where microservices register themselves.
   - Eureka clients facilitate service discovery by querying the Eureka server.
   - Dependency on `spring-cloud-starter-netflix-eureka-client` is added to enable Eureka client functionality.

   Example Eureka Client Configuration:
   ```java
   @EnableEurekaClient
   public class QuizServiceApplication {
       public static void main(String[] args) {
           SpringApplication.run(QuizServiceApplication.class, args);
       }
   }
   ```

## 12. **Feign and Eureka Integration:**
   - Feign can be configured to use Eureka for service discovery.
   - The Feign client's URL is replaced with the service name.
   - The QuestionClient is modified to use Eureka service discovery.

   Example QuestionService Feign Client with Eureka:
   ```java
   @FeignClient(name = "question-service")
   public interface QuestionClient {
       @GetMapping("/question/generate")
       List<Integer> generateQuestions();
   }
   ```

## 13. **Adjusting Quiz Service for Feign:**
   - The QuizService now utilizes the Feign client for question retrieval.
   - The RestTemplate logic is replaced with Feign client method calls.

   Example Quiz Service Using Feign Client:
   ```java
   @Service
   public class QuizService {
       @Autowired
       private QuestionClient questionClient;

       public Quiz createQuiz(QuizDto quizDto) {
           List<Integer> questionIds = questionClient.generateQuestions();
           // Logic to create quiz using questionIds
           return new Quiz();
       }
   }
   ```

## 14. **Testing the Quiz Service:**
   - The quiz service can be tested by creating a quiz using the exposed API.
   - Ensure that both Eureka server and the QuestionService are running.

   Example Quiz Creation Request:
   ```http
   POST http://localhost:8081/quiz/create
   Body: {
       "categoryName": "Java",
       "numQuestions": 5,
       "title": "Java Basics Quiz"
   }
   ```

## 15. **Next Steps:**
   - The quiz service is now capable of creating quizzes with questions retrieved from the question service.
   - Further development involves implementing additional functionalities like retrieving scores and submitting quiz answers.
   - Continued collaboration between microservices to enrich the overall application.

In the next section, we will dive into more detailed implementation, covering aspects like retrieving questions, calculating scores, and handling quiz submissions. The integration of Eureka and Feign ensures a more robust and scalable microservices architecture.

# Chapter 16: Setting Up Eureka Server and Enabling Service Discovery

## 1. **Introduction:**
   - Eureka server is essential for enabling service discovery in a microservices architecture.
   - Services register themselves with Eureka, allowing others to discover and connect to them.
   - Netflix Eureka server is a popular choice for this purpose.

## 2. **Creating a Eureka Server:**
   - Use start.spring.io to generate a new project.
   - Select Maven, Java, and add dependencies: "Spring Web" for running the project and "Eureka Server" for creating a Eureka server.
   - Provide group ID and artifact ID.
   - Example project setup:
     ```xml
     <dependencies>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
         <dependency>
             <groupId>org.springframework.cloud</groupId>
             <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
         </dependency>
     </dependencies>
     ```

## 3. **Configuring Eureka Server:**
   - Open the `ServiceRegistryApplication` class and add the `@EnableEurekaServer` annotation.
   - Configure properties in `application.properties`:
     - `spring.application.name`: Specify the name of the Eureka server.
     - `server.port`: Set the port number for the server.
     - `eureka.instance.hostname`: Set the hostname for the Eureka server.
     - `eureka.client.register-with-eureka`: Set to `false` to prevent the server from registering itself.
     - `eureka.client.fetch-registry`: Set to `false` to avoid fetching the registry.
   - Example configuration:
     ```properties
     spring.application.name=service-registry
     server.port=8761
     eureka.instance.hostname=localhost
     eureka.client.register-with-eureka=false
     eureka.client.fetch-registry=false
     ```

## 4. **Running Eureka Server:**
   - Start the Eureka server application.
   - Access the Eureka dashboard by navigating to `http://localhost:8761` in a web browser.
   - The dashboard should initially show no registered instances.

## 5. **Enabling Eureka Client in Question Service:**
   - Open the `pom.xml` file of the Question Service.
   - Uncomment the Eureka client dependency.
     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
     </dependency>
     ```
   - Restart the Question Service application.

## 6. **Registering Question Service with Eureka:**
   - The Question Service automatically registers itself with the Eureka server.
   - Check the Eureka dashboard to confirm registration.
   - Example registration:
     - Name: `question-service`
     - Port: `8080` (default instance)
     - Multiple instances can be registered with different port numbers.

## 7. **Adding Eureka Client to Quiz Service:**
   - Similar to the Question Service, enable Eureka client in the Quiz Service.
   - Uncomment the Eureka client dependency in the `pom.xml`.
   - Restart the Quiz Service application.

## 8. **Testing Eureka Registration:**
   - Confirm that both Question Service and Quiz Service instances are registered in the Eureka dashboard.
   - Instances will be listed with their names, ports, and status.

## 9. **Next Steps:**
   - With Eureka server and client set up, services can discover and connect to each other.
   - The next chapter will explore the integration of Feign for simplifying HTTP requests between microservices.
   - Further developments will include enhancing the communication between Question Service and Quiz Service using Feign.

In the next section, we will delve into the details of configuring Feign and making it work seamlessly with Eureka for improved microservices communication.

# Chapter 17: Implementing Communication between Microservices with Feign

## 1. **Overview:**
   - Eureka server is running, registering both question service and quiz service.
   - Need to establish communication between quiz service and question service.
   - Using Feign to simplify RESTful calls between microservices.

## 2. **Creating a Feign Client Interface:**
   - Establishing communication involves creating a Feign client interface.
   - In the general package, create a new package named "Feign."
   - Inside the Feign package, create a Feign interface named "QuizInterface."
   - Add the `@FeignClient("question-service")` annotation, specifying the service name registered with Eureka.
   - Declare the necessary methods for communication.
   - Example interface structure:
     ```java
     @FeignClient("question-service")
     public interface QuizInterface {
         @GetMapping("/questions/getQuestionsForQuiz")
         ResponseEntity<List<Integer>> getQuestionsForQuiz(
             @RequestParam String category,
             @RequestParam int numberOfQuestions
         );

         // Declare other methods for communication
     }
     ```

## 3. **Enabling Feign and Eureka Client in Quiz Service:**
   - In the `pom.xml` of the quiz service, uncomment the Feign and Eureka client dependencies.
   - Enable Feign clients in the main application class using `@EnableFeignClients` annotation.
   - Example dependencies:
     ```xml
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
     </dependency>
     <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-openfeign</artifactId>
     </dependency>
     ```
   - Example main application class:
     ```java
     @SpringBootApplication
     @EnableFeignClients
     public class QuizServiceApplication {
         public static void main(String[] args) {
             SpringApplication.run(QuizServiceApplication.class, args);
         }
     }
     ```

## 4. **Using Feign Interface in Quiz Service:**
   - Autowire the `QuizInterface` in the Quiz Service class.
   - Replace the use of `RestTemplate` with Feign methods.
   - Example usage in Quiz Service class:
     ```java
     @Autowired
     private QuizInterface quizInterface;

     @PostMapping("/generateQuiz")
     public ResponseEntity<String> createQuiz(@RequestBody QuizDto quizDto) {
         // Use Feign to get questions from the question service
         ResponseEntity<List<Integer>> response = quizInterface.getQuestionsForQuiz(
             quizDto.getCategory(),
             quizDto.getNumberOfQuestions()
         );
         List<Integer> questions = response.getBody();

         // Create Quiz object and save it
         Quiz quiz = new Quiz();
         quiz.setTitle(quizDto.getTitle());
         quiz.setQuestionIds(questions);
         quizDao.save(quiz);

         return new ResponseEntity<>("Quiz created successfully", HttpStatus.CREATED);
     }
     ```

## 5. **Configuring Quiz Service:**
   - In `application.properties` of the quiz service, set the application name and port number.
   - Example configuration:
     ```properties
     spring.application.name=quiz-service
     server.port=8090
     ```

## 6. **Running Quiz Service:**
   - Ensure the application is running on a different port (e.g., 8090) to avoid conflicts.
   - Confirm successful registration in the Eureka dashboard.

## 7. **Testing Communication:**
   - Run the quiz service application.
   - Verify successful registration in Eureka dashboard.
   - Initiate a request to create a quiz and observe the Feign client making requests to the question service.
   - Confirm quiz creation in the quiz service.

## 8. **Summary:**
   - Feign simplifies communication between microservices by providing a declarative way to make HTTP requests.
   - Eureka server facilitates service discovery, enabling seamless communication between registered microservices.
   - Successful implementation of Feign allows the quiz service to request questions from the question service effortlessly.

## 9. **Next Steps:**
   - Further enhancements can include error handling, additional Feign methods, and improving overall system resilience.
   - Exploring more advanced features of Feign, such as custom error handling and fallback mechanisms, for robust microservices communication.

# Chapter 18: Implementing QuizService Features

## 1. **Overview:**
  - Reviewing the QuizController methods: createQuiz, getQuizQuestions, and calculateResult (getScore).
  - Focus on implementing `getQuizQuestions` and `calculateResult` methods.

## 2. **Implementing getQuizQuestions Method:**
  - In QuizController, work on the `getQuizQuestions` method.
  - This method aims to retrieve questions for a particular quiz ID.
  - Query the QuizService to get the question IDs associated with the quiz.
  - Utilize QuizInterface to interact with QuestionService and fetch the actual questions.
  - Return the list of QuestionWrapper for the specified quiz ID.
  - Example code:
    ```java
    @GetMapping("/get/{id}")
    public ResponseEntity<List<QuestionWrapper>> getQuizQuestions(@PathVariable int id) {
        // Get question IDs for the quiz
        List<Integer> questionIds = quizService.getQuestionIds(id);

        // Fetch questions from QuestionService
        ResponseEntity<List<QuestionWrapper>> questions = quizInterface.getQuestionsFromId(questionIds);

        // Return the questions
        return questions;
    }
    ```

## 3. **Implementing calculateResult Method (getScore):**
  - Work on the `calculateResult` method in QuizController.
  - This method involves submitting quiz responses and obtaining the score from the QuestionService.
  - Utilize QuizInterface to communicate with QuestionService and retrieve the score.
  - Return the calculated score as a ResponseEntity.
  - Example code:
    ```java
    @PostMapping("/submit/{id}")
    public ResponseEntity<Integer> calculateResult(
            @PathVariable int id,
            @RequestBody List<String> responses
    ) {
        // Use QuizInterface to get the score from QuestionService
        ResponseEntity<Integer> scoreResponse = quizInterface.getScore(responses);

        // Return the score
        return scoreResponse;
    }
    ```

## 4. **Testing the Implemented Methods:**
  - Restart the QuizService application.
  - Use Postman to test the `getQuizQuestions` and `calculateResult` methods.
  - Verify that questions are retrieved successfully, and the score is calculated accurately.

## 5. **Summary:**
  - Implemented the `getQuizQuestions` method to fetch questions for a specific quiz ID.
  - Completed the `calculateResult` method (getScore) for submitting quiz responses and obtaining the score from QuestionService.
  - Successfully tested the implemented methods to ensure correct functionality.

## 6. **Next Steps:**
  - Further refinement of error handling and validation for user inputs.
  - Consider additional features, such as tracking user progress or providing detailed feedback based on quiz performance.
  - Explore scalability options and system optimization for handling a larger number of concurrent quiz submissions.

## 7. **Instance Confusion Issue:**
  - Noted the presence of two instances of the QuestionService.
  - In the next chapter, address the instance confusion by implementing load balancing and discussing its importance in a microservices architecture.

# Chapter 19: Load Balancing in Microservices

## 1. **Introduction:**
   - Discussion on the concept of load balancing in microservices architecture.
   - Overview of horizontal scaling and the need for distributing load among multiple service instances.

## 2. **Load Balancing in Microservices:**
   - Explained the idea of having multiple instances of the same service.
   - Horizontal scaling enables creating multiple instances to handle increased load.
   - Load balancing distributes incoming requests across available service instances.
   - Ensures optimal utilization of resources and prevents a single instance from being overwhelmed.

## 3. **Load Balancing in Microservices with Spring and Feign:**
   - Introduced the usage of load balancing in microservices with Spring framework and Feign.
   - Spring, Eureka, and Feign provide tools to facilitate automatic load balancing without manual configuration.
   - Described the default inclusion of load balancers in recent versions of Spring.
   - Emphasized that when using Feign client, it automatically balances the load among available service instances.

## 4. **Demonstration with Example Code:**
   - Showcased an example where a QuizService makes requests to multiple instances of QuestionService.
   - Printed the port number of the instance being used for each request to demonstrate load balancing.
   - Used the Spring `Environment` variable to obtain the port number information.
   - Mentioned that this load balancing feature comes with the integration of Eureka and Feign.

## 5. **Code Implementation for Load Balancing:**
   - Provided example code for printing the port number in the `QuestionController` to identify the instance being accessed.
     ```java
     @Autowired
     private Environment environment;

     @GetMapping("/get/{id}")
     public ResponseEntity<Question> getQuestion(@PathVariable int id) {
         // Print the port number of the instance
         System.out.println("Instance running on port: " + environment.getProperty("local.server.port"));
         // Rest of the method logic
     }
     ```

## 6. **Testing Load Balancing:**
   - Restarted both instances of QuestionService on different ports.
   - Sent multiple requests from QuizService to QuestionService to observe the load balancing behavior.
   - Demonstrated that requests are distributed randomly among available instances.

## 7. **Conclusion:**
   - Concluded the chapter by highlighting the simplicity of achieving load balancing in microservices.
   - Emphasized that the integration of Eureka, Feign, and Spring handles load balancing seamlessly without manual configuration.
   - Encouraged exploration of additional features and optimizations for further enhancing the microservices architecture.

## 8. **Next Steps:**
   - Explore more advanced features of microservices, such as circuit breakers, fallback mechanisms, and centralized configuration.
   - Consider incorporating security measures and monitoring tools for robust microservices deployment.
   - Continue refining and expanding microservices to meet evolving application requirements.

# Chapter 20: API Gateway in Microservices

## Overview:
- Creation of two microservices and exploration of essential concepts.
- Introduction to microservices communication and the need for service discovery.
- Implementation of Eureka server and Eureka client for service discovery.
- Utilizing Feign as a shortcut for microservices interaction.

## Importance of API Gateway:
- Microservices viewed as one application from a user's perspective.
- Users prefer a unified interaction through a single URL.
- Challenges: multiple services, different names, port numbers, and authentication for each service.
- Introduction to the API gateway as an entry point and interface for users.

## API Gateway Setup:
1. **Project Creation:**
  - Use [start.spring.io](https://start.spring.io/) to create a new Maven project.
  - Group ID: com.telusko, Artifact ID: API Gateway, Java 17.
  - Dependencies: Gateway and Eureka Discovery Client.

  ```java
  // API Gateway Application
  @SpringBootApplication
  @EnableEurekaClient
  public class ApiGatewayApplication {
      public static void main(String[] args) {
          SpringApplication.run(ApiGatewayApplication.class, args);
      }
  }
  ```

2. **Configuration:**
  - Set `cloud.application.name` as "API gateway."
  - Set server port to 8765.
  - Enable the Eureka client for service registration.

  ```properties
  # application.properties
  spring.application.name=api-gateway
  server.port=8765
  eureka.client.service-url.default-zone=http://localhost:8761/eureka/
  ```

3. **Eureka Server and Running:**
  - Run Eureka server, question service, quiz service, and API gateway.
  - Confirm API gateway registration on Eureka server.

4. **API Gateway Interaction:**
  - Postman requests: Use API gateway (port 8765) instead of direct service access.
  - Discuss challenges with direct access and the need for API gateway.

5. **Discovery Configuration:**
  - Configure API gateway to discover services from Eureka.
  - Set `spring.cloud.gateway.discovery.locator.lowercase-service-id` to enable service discovery.

  ```properties
  # application.properties
  spring.cloud.gateway.discovery.locator.lowercase-service-id=true
  ```

6. **Testing:**
  - Restart API gateway and test requests.
  - Demonstrate successful interaction with the quiz service.

## Conclusion:
- API gateway simplifies microservices interaction for users.
- Discussion on challenges and the importance of unified access.
- Configuration steps for API gateway and integration with Eureka.
- Use of Postman for testing and verification of successful interaction.
- Recap of the entire microservices series and encouragement for community support.

Feel free to ask for clarifications or more details on specific points.
