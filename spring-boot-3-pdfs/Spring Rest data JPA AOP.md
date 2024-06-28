# **Section 1: Spring Rest**
# Chapter 1: Working with REST API using Spring Boot

## Overview of the Project
- The project involves building a full-stack application where users can view and add jobs. It includes both frontend and backend components.
- The application has controllers for handling requests, different layers for processing, and a mock database (job repo) for data storage.

## Full Stack Application Architecture
- The project is considered a full-stack application, encompassing both frontend and backend in a single project.
- Frontend includes JSP pages for rendering views, allowing users to interact with the application.
- Backend includes controllers for accepting requests, processing layers, and a mock database.

## Transition to REST API Concept
- The current application serves both browsers and mobile applications, but there's a need to optimize for mobile apps.
- Proposes the idea of having a common server that doesn't return HTML pages but only data.
- Suggests building a separate frontend (UI) using technologies like React or Angular.
- The backend server will exclusively handle JSON or XML data, making communication more efficient.

## Introduction to REST API
- Describes the need for a server that returns only data, not pages, and introduces the concept of a REST API.
- Emphasizes the importance of proper data formatting for communication.
- Mentions the use of JSON and XML as common formats for data exchange between client and server.

### Example Code for JSON Data Format
```json
{
  "employee": {
    "name": "John Doe",
    "id": 123,
    "address": "123 Main St",
    "currentEmployer": "Microsoft"
  }
}
```

## Need for REST API
- Explores the scenario of having two separate servers for browsers and mobile applications.
- Suggests the idea of a single common server that returns only data in JSON or XML format.
- Highlights the importance of creating a frontend (UI) separately.

## Frontend Technology
- Acknowledges the existence of a React frontend for demonstration purposes.
- Stresses that this is not a React course, but explains the project structure.
- Encourages users to run and connect with the frontend, emphasizing the importance of frontend testing.

## REST API Client: Postman
- Introduces Postman as a popular REST client for testing API endpoints.
- Emphasizes the need to understand backend functionality, even if not interested in frontend development.

# Chapter 2: Understanding REST
## Overview of REST
- REST stands for Representational State Transfer.
- Discusses the primary operations in a web application: create, read, update, delete (CRUD).
- Entities in the application are treated as resources, representing different aspects such as jobs, employees, employers, etc.

## State in REST
- Defines the concept of state in REST, emphasizing that data on the server is considered a resource with a changing state.
- Describes the state as the current value of a resource at a given moment.
- Explains the importance of sending the state to the client in a presentable and transferable format.

## Stateless Nature of REST
- Emphasizes that REST is stateless, requiring clients to provide all details in every request.
- States that each request to the server should include information about who the client is and why the data is requested.

## Noun-Based Approach in REST API
- Introduces the noun-based approach in REST, where URLs represent resources (nouns) rather than actions (verbs).
- Contrasts with traditional action-based URLs used in non-RESTful approaches.

### Example URLs in REST
- `localhost:8080/jobs` for details about jobs.
- `localhost:8080/job` for adding a job.
- `localhost:8080/job` for retrieving a specific job.

## HTTP Methods in REST
- Highlights the use of HTTP methods (GET, POST, PUT, DELETE) in REST to differentiate between actions on resources.
- Discusses how the same URL can perform different actions based on the HTTP method used.

### Example HTTP Methods in REST
- `GET localhost:8080/jobs` for retrieving job details.
- `POST localhost:8080/job` for adding a new job.
- `PUT localhost:8080/job` for updating an existing job.
- `DELETE localhost:8080/job` for deleting a job.

## Data Formats in REST
- Introduces JSON and XML as two famous formats for representing data in REST.
- States that JSON is preferred for its readability and efficiency in data representation.

### Example JSON Data
```json
{
  "job": {
    "title": "Software Engineer",
    "description": "Developing software applications",
    "location": "XYZ City"
  }
}
```

### Example XML Data
```xml
<job>
  <title>Software Engineer</title>
  <description>Developing software applications</description>
  <location>XYZ City</location>
</job>
```

## Conclusion
- Provides an overview of REST fundamentals, including state, statelessness, noun-based approach, HTTP methods, and data formats (JSON, XML).
- Sets the stage for further exploration of HTTP methods in the next video.

# Chapter 3: Working with HTTP Methods in Spring Boot

## HTTP and Methods
- **HTTP:** Stands for Hypertext Transfer Protocol.
- **Methods:** Operations to work with HTTP.
  - **GET:** Used to fetch data from the server.
  - **POST:** Used to create a resource or send data to the server.
  - **PUT:** Used for updating data.
  - **DELETE:** Used to delete data.

## HTTP Methods in Spring Boot
- **Spring Boot Annotations:**
  - `@GetMapping`: Maps HTTP GET requests.
  - `@PostMapping`: Maps HTTP POST requests.
  - `@PutMapping`: Maps HTTP PUT requests.
  - `@DeleteMapping`: Maps HTTP DELETE requests.

## URL and Methods Correlation
- **URL Structure:**
  - For creating: `/job` (POST method).
  - For reading: `/job` (GET method).
  - Methods distinguish the operation.

## Front-end Testing
- **Browser vs. Postman:**
  - Browsers send GET requests by default.
  - For POST requests, create a form.
  - Postman: A dedicated client for testing HTTP methods.

## Setting Up Front-end
1. Open the provided React project in VS Code.
2. Open terminal and install dependencies using `npm install`.
3. Run JSON server using `json-server --watch db.json --port 8000`.
4. Start React application using `npm start`.

## Understanding React
- **Component-based Design:**
  - React uses components for building UI.
  - Components are reusable.
  - Example: `App` component rendering `AllPosts` component.

## Fetching Data in React
- **Axios:** Used for making HTTP requests.
- **Fetching Data:**
  - `axios.get('http://localhost:8000/posts')`.
  - Mapping data using `map` in React components.

## Project Execution
1. Install dependencies with `npm install`.
2. Start JSON server with `json-server --watch db.json --port 8000`.
3. Start React application with `npm start`.
4. View all job posts fetched from the fake backend.

## React Component Hierarchy
1. `index.html`: Main HTML file.
2. `index.js`: Manages React rendering in the root (`<div id="root">`) of `index.html`.
3. `App.js`: Top-level React component, rendering child components.
4. `AllPosts.js`: Component fetching and displaying job posts.

## Challenges and Choices
- **Port Number Challenge:**
  - Matching the port number of React and Spring Boot.
  - Update the port number when the Spring Boot backend is ready.

## Conclusion
- Basic understanding of HTTP methods.
- Integration of React and Spring Boot for front-end and back-end.
- Future focus: Backend implementation in Spring Boot.
- Challenges: Port matching between React and Spring Boot.
- React's component-based design for building UI.
- Fetching data from a fake backend using Axios in React.

# Chapter 4: Building the Backend with Spring Boot

## Project Setup
1. **Creating a New Project:**
   - Utilizing Spring Initializr.
   - Selecting Maven, Spring Boot version, and dependencies.
   - Dependencies: Spring Web and Lombok.

2. **Copying Existing Code:**
   - Copying the model, repository, and service classes from the previous project.

## Controller Creation
1. **Creating a New Controller:**
   - Adding a new class named `JobRestController`.
   - Copying and pasting the model, repository, and service classes.

2. **Mapping for View All Jobs:**
   - Defining a method `getAllJobs` in the controller.
   - Using `@GetMapping` with URL "/jobPosts".
   - Using `@Autowired` to inject the `JobService`.
   - Returning a list of `JobPost` objects.

3. **RestController Annotation:**
   - Instead of `Controller`, using `@RestController`.
   - Avoiding the need for `@ResponseBody` with `@RestController`.

## Running the Backend
1. **Starting the Application:**
   - Running the Spring Boot application.
   - Ensuring the application runs on a different port.

2. **Verification in the Browser:**
   - Accessing the URL `localhost:8080/jobPosts`.
   - Understanding the internal error due to the ViewResolver.
   - Resolving the issue by adding `@ResponseBody` or using `@RestController`.
   - Verifying the JSON data in the browser.

3. **Verification in Postman:**
   - Changing the URL to `localhost:8080/jobPosts`.
   - Sending a GET request.
   - Viewing the JSON response.

## Conclusion
- **Project Structure:**
  - Model, repository, and service classes reused.
  - Creating a dedicated controller for RESTful operations.

- **Controller Configuration:**
  - Use of `@RestController` for REST-specific controllers.
  - Handling JSON data instead of returning view names.

- **Verification:**
  - Successful retrieval of data through browser and Postman.
  - Understanding the importance of matching request methods.

  Certainly! Below is the sample code for the `JobRestController` in Spring Boot:

  ```java
  package com.telusko.springbootrest.controller;

  import com.telusko.springbootrest.model.JobPost;
  import com.telusko.springbootrest.service.JobService;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;

  import java.util.List;

  @RestController
  @RequestMapping("/jobPosts")
  public class JobRestController {

      @Autowired
      private JobService jobService;

      @GetMapping
      public List<JobPost> getAllJobs() {
          return jobService.getAllJobs();
      }
  }
  ```

  Explanation of the code:
  - `@RestController`: Annotation indicating that this class is a REST controller, and it will automatically convert the returned data to JSON.
  - `@RequestMapping("/jobPosts")`: Maps all the methods in this class to the `/jobPosts` URL.
  - `@Autowired`: Injects the `JobService` bean into the controller.
  - `@GetMapping`: Handles HTTP GET requests. In this case, it's used to retrieve all job posts.
  - `public List<JobPost> getAllJobs()`: Method that returns a list of `JobPost` objects. This is the endpoint for fetching all job posts.

  This code assumes you have the `JobService` in place, which contains the business logic for retrieving job posts. Additionally, make sure to have the necessary dependencies, such as Spring Web and Lombok, in your `pom.xml` file.

  # Chapter 5: Connecting React Frontend with Spring Boot Backend

## Introduction
- Objective: Connect the React frontend application with the Spring Boot backend.
- Current State: React app using mock database (db.json) for data.

## Changing Backend URL in React App
1. **URL Change in React Application:**
   - React app currently uses data from the mock database (db.json).
   - Change the URL in the React app to connect with the Spring Boot backend.

```javascript
// Change this URL to connect with the Spring Boot backend
const API_URL = 'http://localhost:8080/jobPosts';
```

2. **Issue with Security:**
   - Attempting to run the React app with the updated URL.
   - Encountering a "Network Error."

## Cross-Origin Resource Sharing (CORS)
1. **Understanding Cross-Origin Issue:**
   - The issue is related to Cross-Origin.
   - Cross-Origin Resource Sharing (CORS) is a security feature.
   - Due to different origins (frontend and backend running on different ports), requests are blocked.

2. **Solution - @CrossOrigin Annotation:**
   - Adding `@CrossOrigin` annotation to the Spring Boot controller.
   - Specifying allowed origins using the `origins` attribute.

```java
@CrossOrigin(origins = "http://localhost:3000")
@RestController
@RequestMapping("/jobPosts")
public class JobRestController {
    // Controller code remains the same
}
```

## Verifying Connection
1. **Refreshing React App:**
   - Restarting the React app after making changes.
   - Checking if the CORS issue is resolved.

2. **Successful Connection:**
   - Refreshing the React app.
   - Confirming that the React application can now retrieve data from the Spring Boot backend.

## Conclusion
- **Cross-Origin Issue Resolution:**
  - Adding `@CrossOrigin` annotation to the Spring Boot controller.
  - Specifying the allowed origin to resolve CORS issues.

- **Successful Connection:**
  - Verifying that the React frontend is now successfully connected to the Spring Boot backend.

# Chapter 6: Fetching Single Job Post Using Path Variable

## Introduction
- **Objective:** Fetch a single job post based on the post ID.
- **Current State:** Successfully fetching all job posts, but now focusing on retrieving a specific job post by ID.

## Requesting Single Job Post
1. **Current Implementation:**
   - The existing controller fetches all job posts when the endpoint `/jobPosts` is called.
   - Viewing all posts is successful.

2. **Scenario Change - Fetching One Post:**
   - Now, the need is to fetch a specific job post by its ID.
   - In the URL, specifying `/jobPost/{postId}` to uniquely identify the post.

```java
// Updated URL for fetching a specific job post
@GetMapping("/jobPost/{postId}")
public JobPost getJob(@PathVariable int postId) {
    return service.getJob(postId);
}
```

3. **Initial Implementation Issue:**
   - Attempting to fetch a specific job post using the updated URL.
   - Encountering a "404 Not Found" error.

## Implementing Path Variable
1. **Path Variable Explanation:**
   - Using `@PathVariable` annotation to extract values from the URI.
   - Defining `{postId}` as a path variable in the URL.
   - Specifying the variable name in the `@PathVariable` annotation.

```java
@GetMapping("/jobPost/{postId}")
public JobPost getJob(@PathVariable("postId") int postId) {
    return service.getJob(postId);
}
```

2. **Dynamic Value Acceptance:**
   - Enabling dynamic acceptance of the post ID from the URL.
   - Previously hardcoded, now accepting the ID dynamically.

```java
// Previous Code
service.getJob(3);

// Updated Code
service.getJob(postId);
```

3. **Testing Dynamic Values:**
   - Relaunching the application after changes.
   - Testing the implementation using Postman with various post IDs.

```java
// Sample Postman Requests
- /jobPost/3
- /jobPost/2
- /jobPost/10
- /jobPost/5
```

## Conclusion
- **Path Variable Implementation:**
  - Successfully implemented fetching a single job post using the path variable.
  - Utilized `@PathVariable` annotation to dynamically accept values from the URI.

- **Next Steps:**
  - Further discussions on sending data to the server, as currently, the focus has been on fetching data.


  # Chapter 7: Sending Data to Server Using Post Mapping

## Introduction
- **Objective:** Learn how to send data from the client to the server.
- **Current State:** Successfully fetching and viewing job posts. Now, focus on sending data to add a new job post.

## Creating a Post Mapping for Adding Job
1. **Creation of Post Controller Method:**
   - To add a new job post, a new method, `addJob`, is created in the controller.
   - The method accepts a `JobPost` object using the `@RequestBody` annotation.

```java
// Post Mapping for Adding a New Job
@PostMapping("/jobPost")
public void addJob(@RequestBody JobPost jobPost) {
    service.addJob(jobPost);
}
```

2. **Understanding @RequestBody Annotation:**
   - `@RequestBody` is used to bind the HTTP request body to the method parameter.
   - It maps the content of the incoming HTTP request body to the `JobPost` object.

```java
public void addJob(@RequestBody JobPost jobPost) {
    // Method implementation to add the job post to the list
    service.addJob(jobPost);
}
```

3. **Testing the Post Mapping:**
   - Relaunching the application after making changes.
   - Using Postman to send a POST request to add a new job post.

```java
// Postman Request for Adding a New Job Post
- Method: POST
- URL: http://localhost:8080/jobPost
- Body: JSON format with new job post details
```

## Alternative Return Approach
1. **Returning the Created Job Post:**
   - Instead of returning void, considering returning the created job post.
   - Additional check by fetching the job post using its ID from the service.

```java
@PostMapping("/jobPost")
public JobPost addJob(@RequestBody JobPost jobPost) {
    service.addJob(jobPost);
    return service.getJob(jobPost.getPostId());
}
```

2. **Testing the Updated Return Approach:**
   - Verifying the updated implementation by sending a POST request and checking the returned data.

```java
// Postman Request for Adding a New Job Post
- Method: POST
- URL: http://localhost:8080/jobPost
- Body: JSON format with new job post details
```

## Conclusion
- **Post Mapping Implementation:**
  - Successfully implemented a Post Mapping to add a new job post.
  - Utilized `@RequestBody` annotation to accept JSON data from the client.
  - Demonstrated an alternative approach by returning the created job post after addition.

- **Next Steps:**
  - Further exploration on handling different data formats, such as XML.
  - Continuing discussions on connecting with a database.

# Chapter 8: Implementing PUT and DELETE Requests

## Introduction
- **Objective:** Explore and implement PUT and DELETE requests in the Spring Boot application.
- **Current State:** Successfully handled GET and POST requests; now focusing on updating (PUT) and deleting (DELETE) job posts.

## Updating Job Post (PUT Request)
1. **Scenario:**
 - Desired Update: Change the job profile from "Frontend Developer" to "React Developer."
 - Also, update the required experience from 3 to 2.

2. **Making a PUT Request:**
 - Using Postman to make a PUT request for updating the job post.
 - Copying the data for the job post to be updated.

```java
// Postman Request for Updating Job Post
- Method: PUT
- URL: http://localhost:8080/jobPost
- Body: JSON format with updated job post details
```

3. **Updating the Controller:**
 - Modifying the controller method to handle PUT requests.
 - Utilizing `@PutMapping` and specifying the URL path as "/jobPost."

```java
@PutMapping("/jobPost")
public JobPost updateJob(@RequestBody JobPost jobPost) {
  service.updateJob(jobPost);
  return service.getJob(jobPost.getPostId());
}
```

4. **Updating the Service and Repository:**
 - Creating the `updateJob` method in the service and repository layers.
 - Iterating through the job posts to find and update the specified job post.

```java
// Service Layer
public void updateJob(JobPost jobPost) {
  repo.updateJob(jobPost);
}

// Repository Layer
public void updateJob(JobPost jobPost) {
  for (JobPost jp : jobs) {
      if (jp.getPostId() == jobPost.getPostId()) {
          jp.setPostProfile(jobPost.getPostProfile());
          jp.setDescription(jobPost.getDescription());
          jp.setExperience(jobPost.getExperience());
          jp.setTechStack(jobPost.getTechStack());
          break;
      }
  }
}
```

5. **Testing the PUT Request:**
 - Executing the PUT request in Postman to update the specified job post.
 - Verifying the changes by fetching all job posts.

## Deleting Job Post (DELETE Request)
1. **Scenario:**
 - Desired Deletion: Delete the job post with the post ID equal to 3.

2. **Making a DELETE Request:**
 - Using Postman to make a DELETE request for removing a specific job post.

```java
// Postman Request for Deleting Job Post
- Method: DELETE
- URL: http://localhost:8080/jobPost/3
```

3. **Handling the DELETE Request in the Controller:**
 - Modifying the controller method to handle DELETE requests.
 - Utilizing `@DeleteMapping` and specifying the URL path as "/jobPost/{postId}" to accept the post ID as a path variable.

```java
@DeleteMapping("/jobPost/{postId}")
public String deleteJob(@PathVariable int postId) {
  service.deleteJob(postId);
  return "Job Post Deleted";
}
```

4. **Updating the Service and Repository for Deletion:**
 - Creating the `deleteJob` method in the service and repository layers.
 - Iterating through the job posts to find and remove the specified job post.

```java
// Service Layer
public void deleteJob(int postId) {
  repo.deleteJob(postId);
}

// Repository Layer
public void deleteJob(int postId) {
  jobs.removeIf(jobPost -> jobPost.getPostId() == postId);
}
```

5. **Testing the DELETE Request:**
 - Executing the DELETE request in Postman to remove the specified job post.
 - Verifying the deletion by fetching all job posts.

## Conclusion
- **PUT and DELETE Implementation:**
- Successfully implemented PUT and DELETE requests.
- Updated job posts using PUT request.
- Deleted a specific job post using DELETE request.

- **Next Steps:**
- Ongoing improvements and enhancements based on project requirements.
- Exploration of additional functionalities and considerations for robust RESTful APIs.

# Chapter 9: Content Negotiation in Spring Boot

## Introduction
- **Objective:** Explore and implement content negotiation in a Spring Boot application.
- **Current State:** Handling JSON data by default; aim to enable support for XML format.

## Understanding Content Negotiation
- **Scenario:** When interacting with an API, the default response is in JSON format. However, some scenarios may require data in XML format.
- **Objective:** Achieve content negotiation to allow both JSON and XML responses based on client preferences.

## Enabling XML Support with Jackson
1. **Default JSON Support:**
   - Current situation: Using Jackson library for default JSON serialization.
   - The library automatically converts Java objects to JSON.

2. **Adding Jackson XML Support:**
   - XML support requires adding the Jackson DataFormat XML library.
   - Find the library on the MVN Repository and add it to the project's `pom.xml`.

```xml
<!-- Jackson XML Support -->
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.15.3</version> <!-- Match the version with Jackson Core -->
</dependency>
```

3. **Reloading Maven and Verifying XML Support:**
   - Reload the Maven project.
   - Confirm the presence of the Jackson DataFormat XML library in the external libraries.
   - Restart the Spring Boot application.

4. **Testing XML Response:**
   - In Postman, set the `Accept` header to `application/xml`.
   - Observe the successful retrieval of data in XML format.

## Content Negotiation for Producing and Consuming
1. **Negotiating Content:**
   - Define what format the server can produce (returns) and consume (accepts).

2. **Specifying Produced Content (Produces):**
   - Using the `@GetMapping` or `@PostMapping` annotation, specify the media type the method produces.

```java
@GetMapping(value = "/jobPost", produces = "application/json")
public List<JobPost> getAllJobPosts() {
    return service.getAllJobPosts();
}
```

3. **Specifying Consumed Content (Consumes):**
   - Using the `@PostMapping` annotation, specify the media type the method can consume.

```java
@PostMapping(value = "/jobPost", consumes = "application/xml")
public JobPost createJobPost(@RequestBody JobPost jobPost) {
    return service.addJob(jobPost);
}
```

4. **Testing Content Negotiation:**
   - Attempt to send requests with specified media types for both producing and consuming.
   - Observe the server's response based on the specified content types.

## Conclusion
- **Content Negotiation Implementation:**
  - Enabled support for XML format by adding Jackson XML library.
  - Demonstrated content negotiation for both producing and consuming JSON and XML formats.
  - Highlighted the importance of specifying media types for effective content negotiation.

- **Next Steps:**
  - Ongoing consideration of project requirements and potential refinements.
  - Further exploration of advanced Spring Boot features and functionalities.

# **Section 2:  Spring Data JPA**
# Chapter 1: Introduction to Spring Data JPA

## Overview
- **Objective:** Understand the role of Spring Data JPA in building applications with database integration.
- **Current State:** Completed the construction of a job portal application without database integration.

## Controller and Service Layers
- **Controller Layer:**
  - Responsible for handling client requests (GET, POST, PUT, DELETE).
  - Accepts requests and returns responses.

- **Service Layer:**
  - Performs the actual operations and logic.
  - Manages the business logic and communicates with the data layer.

## Introduction to Repository Layer
- **Database Interaction:**
  - Previous implementation lacked connectivity with the database.
  - Need to fetch data from the database using the repository layer.

- **Repository Layer Purpose:**
  - Connects the service layer to the database.
  - Responsible for executing database operations.

## Approaches for Database Interaction
1. **Traditional Approaches:**
   - Use normal JDBC or Spring JDBC.
   - Implement Spring ORM and manage code manually.

2. **Simplified Approach - Spring Data JPA:**
   - Utilizes the Spring Data JPA module.
   - Simplifies database operations and integrates with JPA (Java Persistence API).

## Choosing Spring Data JPA
- **Advantages of Spring Data JPA:**
  - Easiest module in the Spring framework.
  - Streamlines database operations.
  - Avoids manual code writing for ORM.

## Example Project - Student Management
- **Project Selection:**
  - Use a previous project related to Spring JDBC - Student Management.
  - Already contains relevant code for JDBC operations.

## Understanding Object-Relational Mapping (ORM)
- **Definition:**
  - ORM stands for Object Relational Mapping.
  - Connects object-oriented programming concepts with relational databases.

- **Java World Object Creation:**
  - Java is object-oriented, and everything is represented as objects.

- **Class Blueprint:**
  - Class serves as a blueprint for creating objects.
  - Contains variables (properties) and methods.

## ORM and Database World Connection
- **Relational Databases:**
  - Store data in tables.
  - Relate tables using primary and foreign keys.

- **ORM Connection:**
  - Connects Java classes to database tables.
  - Maps class properties to table columns.

## Hibernate - ORM Tool
- **ORM Tool Options:**
  - Multiple ORM tools available (Hibernate, TopLink, etc.).
  - Hibernate is widely used.

- **Hibernate Implementation:**
  - Hibernate simplifies the process of connecting objects to the database.

## Introduction to JPA
- **Java Persistence API (JPA):**
  - Common specification for ORM tools.
  - Provides a standard way to connect Java objects to databases.

- **Hibernate and JPA:**
  - Hibernate implements the JPA specification.
  - JPA offers a generic interface, making it easy to switch between ORM tools.

## Spring ORM and JPA
- **Spring ORM:**
  - Connects with the Spring Framework.
  - Provides integration with JPA.

- **JPA and Hibernate:**
  - Both Hibernate and Spring ORM implement the JPA specification.

## Simplifying Database Operations with Spring Data JPA
- **Introduction to Spring Data JPA:**
  - Part of the Spring ecosystem.
  - Simplifies the implementation of the repository layer.
  - Reduces the need for extensive code for database interactions.

## Conclusion
- **Key Takeaways:**
  - Introduction to the importance of database connectivity.
  - Overview of traditional and simplified approaches.
  - Selection of Spring Data JPA for its simplicity and integration capabilities.
  - Understanding the concepts of ORM, JPA, and their connection with Hibernate.

- **Next Steps:**
  - Dive into practical implementation using Spring Data JPA in the selected Student Management project.
  - Explore the functionalities and benefits provided by Spring Data JPA.

### Chapter 2: Spring Data JPA Project

#### Introduction:
- In this chapter, we delve into a Spring Data JPA project, comparing it with a previous project that used JDBC in the repository layer.
- The goal is to demonstrate how Spring Data JPA simplifies database interactions and eliminates the need for explicit SQL queries.

#### Project Setup:
1. **Create a new project:**
   - Using Spring Initializr to create a Maven project named "spring-data-jpa-example" with group ID "com.telusko" and Java version 21.
   - Dependencies: Spring Data JPA and PostgreSQL.

2. **Model Class:**
   - Copy the existing Student class from the Spring JDBC project to the new project's `model` package.

   ```java
   package com.telusko.model;

   public class Student {
       // ... (Roll number, name, marks)
   }
   ```

3. **Configure Database:**
   - Add the PostgreSQL configuration to `application.properties`.

   ```properties
   spring.datasource.url=jdbc:postgresql://localhost:5432/telusko
   spring.datasource.username=postgres
   spring.datasource.password=0000
   ```

   Ensure there's no existing 'student' table in the database.

#### Creating JPA Repository:
4. **Repository Interface:**
   - Create a new interface, `StudentRepo`, extending `JpaRepository` for the `Student` class.

   ```java
   package com.telusko.repo;

   import org.springframework.data.jpa.repository.JpaRepository;

   public interface StudentRepo extends JpaRepository<Student, Integer> {
   }
   ```

   Add `@Repository` annotation to mark it as a repository.

#### Main Application:
5. **Main Application Class:**
   - In the main application class, retrieve a `StudentRepo` bean from the application context.

   ```java
   StudentRepo repo = context.getBean(StudentRepo.class);
   ```

6. **Data Saving:**
   - Create `Student` objects and save them using the repository.

   ```java
   Student s1 = new Student();
   // Set student properties
   repo.save(s1);
   ```

   Ensure that the `spring.jpa.hibernate.ddl-auto` property is set to `update` in `application.properties` to allow Hibernate to manage table creation.

7. **Data Retrieval:**
   - Demonstrate retrieving all students and fetching a specific student by ID.

   ```java
   List<Student> allStudents = repo.findAll();
   Optional<Student> studentOptional = repo.findById(101);
   Student student = studentOptional.orElse(new Student());
   ```

   Note the use of `Optional` to handle cases where the data may not exist.

#### Conclusion:
- Spring Data JPA facilitates database interactions by providing repository interfaces with common CRUD operations.
- Hibernate, behind the scenes, generates SQL queries based on method names and the structure of the entity classes.
- Optional is used to handle cases where data may or may not be present.
- The process includes setting up the project, creating an entity class, configuring the database, and using a JPA repository for data access.

# Chapter 3: Working with Spring Data JPA - Update and Delete

## Overview
In this chapter, we will explore how to perform update and delete operations using Spring Data JPA. We'll delve into updating existing records and deleting records from the database. Additionally, we'll discuss the methods provided by JPA repositories and how to handle these operations effectively.

### Update Operation
Updating a record involves modifying certain fields of an existing entity and saving the changes to the database. In Spring Data JPA, the `save` method can be used for both saving new records and updating existing ones.

#### Example Code:
```java
// Updating an existing student record
Student s2 = context.getBean(Student.class);
s2.setRollNumber(102);
s2.setName("Kiran");
s2.setMarks(65);

repo.save(s2);
```

- Explanation:
  - Obtain an instance of the student you want to update.
  - Set the desired values for the fields you want to modify.
  - Use the `save` method to update the record in the database.

### Delete Operation
Deleting a record involves removing an entity from the database. In Spring Data JPA, the `delete` method is used for deleting records.

#### Example Code:
```java
// Deleting a student record
repo.delete(s2);
```

- Explanation:
  - Use the `delete` method and pass the entity you want to delete.

### Custom Query Methods
Spring Data JPA provides automatic query generation based on method names. However, if you need to create a custom query, you can use the `@Query` annotation with JPQL (Java Persistence Query Language).

#### Example Code:
```java
// Custom query to find students by name
@Query("SELECT s FROM Student s WHERE s.name = ?1")
List<Student> findByName(String name);
```

- Explanation:
  - Use the `@Query` annotation and specify the JPQL query.
  - The `?1` is a placeholder for the method parameter, in this case, the student's name.

### DSL (Domain Specific Language)
Spring Data JPA supports a DSL that allows the generation of queries based on method names. The method naming conventions and parameter names play a crucial role in this automatic query generation.

#### Example Code:
```java
// Using DSL to find students by marks greater than 72
List<Student> findByMarksGreaterThan(int marks);
```

- Explanation:
  - The method name adheres to the DSL naming convention.
  - JPA generates a query to find students with marks greater than the specified value.

### Summary
In this chapter, we covered the update and delete operations in Spring Data JPA. We explored the use of the `save` method for updating records and the `delete` method for removing records. Additionally, we discussed custom queries using the `@Query` annotation and the DSL provided by Spring Data JPA for automatic query generation. These operations provide essential functionalities for managing data in a Spring Data JPA application.

# Chapter 4: Integrating Spring Data JPA with Job Application

## Overview
In this chapter, we will explore the process of integrating Spring Data JPA into a job application. We will transform an existing job management system, which initially uses an in-memory list, into a fully-fledged JPA-based application. This involves adding JPA dependencies, creating a JPA repository, updating service methods, and configuring the application properties.

### Dependency Addition
To start using Spring Data JPA, we need to add the relevant dependencies to the project's `pom.xml` file.

#### Example Code:
```xml
<!-- Add Spring Data JPA and PostgreSQL dependencies -->
<dependencies>
    <!-- ... other dependencies ... -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

- Explanation:
  - Include the `spring-boot-starter-data-jpa` dependency for Spring Data JPA.
  - Add the PostgreSQL dependency since it is the chosen database for this example.

### Creating JPA Repository
In Spring Data JPA, repositories are interfaces that extend `JpaRepository`. They provide out-of-the-box methods for common database operations.

#### Example Code:
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface JobRepo extends JpaRepository<JobPost, Integer> {
    // Additional custom query methods can be added here if needed
}
```

- Explanation:
  - Create an interface `JobRepo` that extends `JpaRepository`.
  - Specify the entity type (`JobPost`) and the type of the primary key (`Integer`).

### Updating Service Methods
Adjust the service methods to use the new JPA repository methods.

#### Example Code:
```java
@Service
public class JobService {
    @Autowired
    private JobRepo repo;

    // ... other methods ...

    public JobPost getJobById(int postId) {
        return repo.findById(postId).orElse(new JobPost());
    }

    public Iterable<JobPost> getAllJobs() {
        return repo.findAll();
    }

    public String addJob(JobPost job) {
        repo.save(job);
        return "Job added successfully!";
    }

    public String updateJob(JobPost job) {
        repo.save(job);
        return "Job updated successfully!";
    }

    public String deleteJob(int postId) {
        repo.deleteById(postId);
        return "Job deleted successfully!";
    }

    public String load() {
        // Load data into the database
        List<JobPost> jobs = Arrays.asList(
            new JobPost("Software Engineer", "Coding and development", 2, Arrays.asList("Java", "Spring Boot")),
            // ... other job posts ...
        );
        repo.saveAll(jobs);
        return "Data loaded successfully!";
    }
}
```

- Explanation:
  - Utilize JPA repository methods (`findById`, `findAll`, `save`, `deleteById`) in service methods.
  - The `load` method populates the database with initial data.

### Entity and Configuration
Make sure the entity class is properly annotated and configure application properties for JPA.

#### Example Code:
```java
@Entity
public class JobPost {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int postId;
    private String title;
    private String description;
    private int experience;
    @ElementCollection
    private List<String> technologies;

    // ... constructors, getters, setters ...
}
```

```properties
# src/main/resources/application.properties

# Database configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/your_database_name
spring.datasource.username=your_database_username
spring.datasource.password=your_database_password
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```

- Explanation:
  - Annotate the `JobPost` class with `@Entity`, specifying the primary key (`@Id`) and its generation strategy (`@GeneratedValue`).
  - Configure database properties in `application.properties`, including the database URL, username, password, and JPA settings.

### Summary
This chapter covered the integration of Spring Data JPA into a job application. The process included adding JPA dependencies, creating a JPA repository, updating service methods, and configuring the application properties. The example demonstrated the transition from an in-memory list to a JPA-backed system for managing job posts, providing persistence and enhanced data retrieval capabilities.

# Chapter 5: Enhancing JPA Features and Adding Custom Query Methods

## Overview
In this chapter, we focus on refining the JPA features in our project and introducing custom query methods for more advanced operations. We start by ensuring the `JobPost` class is properly annotated as an entity with a primary key. We then configure properties for PostgreSQL and proceed to execute the application to verify its functionality. Additionally, we explore how to perform searches based on specific keywords using custom query methods in the repository.

### Entity and Configuration
Let's make sure the `JobPost` class is correctly annotated as an entity with a primary key (`Id`). We also need to configure properties for PostgreSQL in the `application.properties` file.

#### Example Code:
```java
@Entity
public class JobPost {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int postId;
    private String title;
    private String description;
    private int experience;
    @ElementCollection
    private List<String> techStack;

    // ... constructors, getters, setters ...
}
```

```properties
# src/main/resources/application.properties

# PostgreSQL Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/your_database_name
spring.datasource.username=your_database_username
spring.datasource.password=your_database_password
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```

- Explanation:
  - Annotate `JobPost` class with `@Entity`, specifying the primary key (`@Id`) and its generation strategy (`@GeneratedValue`).
  - Configure PostgreSQL database properties in `application.properties`.

### Custom Query Methods
Now, let's add a custom query method in the repository to search for job posts based on specific keywords in the post profile and description.

#### Example Code:
```java
// JobRepo.java
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface JobRepo extends JpaRepository<JobPost, Integer> {
    // ... existing methods ...

    // Custom query method for searching by keyword in post profile and description
    List<JobPost> findByPostProfileContainingOrPostDescriptionContaining(String postProfile, String postDescription);
}
```

- Explanation:
  - Add a custom query method in the `JobRepo` interface.
  - Utilize the naming convention to automatically generate a query for searching based on `postProfile` and `postDescription` containing a specific keyword.

### Testing the Search Functionality
After making these changes, execute the application and test the search functionality using Postman.

#### Example Request in Postman:
- **GET** request to `jobpost/keyword/Java` to search for posts containing the keyword "Java."

- **GET** request to `jobpost/keyword/API` to search for posts containing the keyword "API."

- **GET** request to `jobpost/keyword/Python` to search for posts containing the keyword "Python."

- Explanation:
  - Execute various requests to test the search functionality.
  - Observe the results and ensure that the application can successfully retrieve posts based on specified keywords.

### Summary
This chapter covered the refinement of JPA features and the addition of custom query methods for advanced search operations. By configuring entity annotations, database properties, and custom queries, the project is enhanced to provide a robust and efficient way of managing job posts. The example demonstrated successful execution of search operations using keywords and showcased the flexibility of Spring Data JPA in handling custom queries.

#**Section 3: Spring Data Rest**
# Chapter 1: Introduction to Spring Data Rest

## Overview
This chapter explores the concept of Spring Data Rest, a powerful feature of the Spring framework that simplifies the development of RESTful web services by automating the creation of REST endpoints. We'll discuss the traditional architecture of web applications, including controllers, services, and repositories. The chapter explains the motivation behind Spring Data Rest, its advantages, and demonstrates how it eliminates the need for explicit controllers, making development more efficient.

### Traditional Web Application Architecture
In traditional web applications, a layered architecture is often followed. The request flows from the client to the controller, which interacts with the service layer for business logic. The service layer, in turn, communicates with the repository layer for database operations.

#### Example Code:
```java
@Controller
public class MyController {
    // Controller handling requests
    // ...
}

@Service
public class MyService {
    // Service layer for business logic
    // ...
}

@Repository
public interface MyRepository extends JpaRepository<MyEntity, Long> {
    // JPA repository for database operations
    // ...
}
```

### Spring Data JPA and Repository Layer
Spring Data JPA simplifies database operations by generating queries based on repository method names. Developers annotate the repository interface with `@Repository` and extend `JpaRepository`. The framework takes care of most query generation.

#### Example Code:
```java
// Repository interface using Spring Data JPA
@Repository
public interface MyRepository extends JpaRepository<MyEntity, Long> {
    // Spring Data JPA queries are inferred from method names
    List<MyEntity> findByFirstName(String firstName);
}
```

### Introduction to Spring Data Rest
Spring Data Rest extends the simplicity of Spring Data JPA by eliminating the need for explicit controller classes. With Spring Data Rest, developers can expose RESTful endpoints directly from the repository layer without creating custom controllers. This approach aims to reduce boilerplate code and improve development efficiency.

### Creating a Spring Data Rest Project
To demonstrate Spring Data Rest, we'll create a new project using the Spring Initializer. The essential dependencies include Spring Data JPA, Spring Data Rest, and the PostgreSQL driver.

#### Example Code (pom.xml):
```xml
<dependencies>
    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Spring Data Rest -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-rest</artifactId>
    </dependency>

    <!-- PostgreSQL Driver -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- Lombok (optional for code simplification) -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

### Setting Up Repository and Model
Reuse the existing repository and model classes from a previous project in the new Spring Data Rest project. The repository interfaces extend `JpaRepository`, and the `@Repository` annotation is retained.

#### Example Code:
```java
// Model class
@Entity
public class JobPost {
    // Entity attributes
    // ...
}

// Repository interface
@Repository
public interface JobRepo extends JpaRepository<JobPost, Integer> {
    // JPA repository methods
    // ...
}
```

### Configuring Application Properties
Configure database connection properties in the `application.properties` file. The configuration includes details such as the database URL, username, password, and other settings.

#### Example Code (application.properties):
```properties
# PostgreSQL Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/your_database_name
spring.datasource.username=your_database_username
spring.datasource.password=your_database_password
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```

### Running the Spring Data Rest Project
Execute the new project, and test the generated RESTful endpoints using Postman. Note the absence of custom controllers, highlighting how Spring Data Rest automatically exposes endpoints based on repository methods.

### Summary
This chapter introduces the fundamentals of Spring Data Rest and its role in simplifying the creation of RESTful web services. By eliminating the need for explicit controllers and leveraging existing repository layers, Spring Data Rest streamlines the development process. The chapter also demonstrates the setup of a Spring Data Rest project using the Spring Initializer and showcases the simplicity of exposing RESTful endpoints with minimal code.

# **Section 4: Spring AOP**

**Chapter 1: Understanding Aspect Oriented Programming (AOP)**

**Introduction to AOP:**
- AOP (Aspect Oriented Programming) is introduced as a complement to OOP (Object Oriented Programming).
- It doesn't replace OOP but offers solutions to certain issues.

**Cross-cutting Concerns:**
- A core problem addressed by AOP is that of cross-cutting concerns.
- Cross-cutting concerns refer to functionalities or aspects that affect multiple parts of an application but aren't central to its core logic.
- Examples include logging, security, validation, exception handling, and performance monitoring.
- These concerns typically spread across various modules and methods, making code maintenance and readability challenging.

**Challenges in Traditional Approach:**
- In traditional programming, developers embed cross-cutting concerns within the main business logic.
- This leads to bloated methods with lines of code unrelated to the core functionality.
- Maintenance and readability suffer as the main business logic gets obscured by auxiliary code.
- Example: Within a REST controller, logging, security checks, input validation, and exception handling may clutter the methods responsible for business logic.

**Example Code:**
```java
@RestController
public class ExampleController {

    @Autowired
    private ExampleService exampleService;

    @PostMapping("/save")
    public ResponseEntity<String> saveData(@RequestBody Data data) {
        // Business logic may reside here
        exampleService.saveData(data);
        // Logging, security checks, validation, and exception handling clutter the method
        // Traditional approach embeds these concerns within business logic
        // Code becomes difficult to maintain and understand
    }
}
```

**Need for Separation of Concerns:**
- To address these issues, the concept of separating concerns emerges.
- Rather than embedding cross-cutting concerns within business logic, they should reside in separate classes and methods.
- This separation enhances code modularity, maintainability, and readability.
- However, calling these separate methods from every relevant point introduces complexity and redundancy.

**Automating Cross-cutting Concerns with AOP:**
- AOP automates the invocation of cross-cutting concerns.
- It allows developers to define aspects (cross-cutting concerns) and specify where they should be applied.
- AOP ensures that these concerns are invoked automatically without explicit calls from the main business logic.
- Example: Logging, security checks, and validation methods can be invoked automatically before or after the execution of business logic methods.

**Conclusion:**
- AOP serves as a powerful tool to address the challenges posed by cross-cutting concerns in traditional programming.
- It promotes cleaner, more maintainable code by automating the invocation of auxiliary functionalities.
- Understanding AOP involves grasping key concepts and terms, which will be explored further in subsequent discussions.

**Chapter 2: Implementing Aspect Oriented Programming (AOP)**

**Introduction to AOP Implementation:**
- AOP aims to address cross-cutting concerns such as logging, performance monitoring, exceptions, and validations.
- AOP allows developers to modularize these concerns and apply them across multiple classes and methods.
- In this chapter, we focus on implementing AOP for logging and understanding its core concepts.

**Creating a Logging Aspect:**
- A logging aspect is created to demonstrate AOP functionality.
- Aspects encapsulate cross-cutting concerns and are separate from the main application logic.

**Example Code:**
```java
package com.example.aop;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

@Component
public class LoggingAspect {

    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);

    public void logMethodCall() {
        logger.info("Method called.");
    }
}
```

- The `LoggingAspect` class contains a method `logMethodCall()` responsible for logging method invocations.
- It utilizes the SLF4J logging framework for logging.

**Configuring Aspect Placement:**
- Aspects are placed within dedicated packages (e.g., AOP package) separate from the main application logic.
- Configuration ensures better organization and maintenance of cross-cutting concerns.

**Implementing Logging Functionality:**
- The `logMethodCall()` method logs method invocations to the console using the `Logger` object.
- Invocation of this method needs to be automated for every method call within the application.

**Understanding AOP Concepts:**
- Core AOP concepts are essential for understanding its implementation.
- These concepts include aspect, join point, advice, pointcut, introduction, target object, AOP proxy, and weaving.
- Different types of advice include before, after returning, after throwing, after (finally), and around.

**Explanation of AOP Concepts:**
1. **Aspect**: A modularization of concern that cuts across multiple classes.
2. **Join Point**: A point during the execution of a program, such as method execution or exception handling.
3. **Advice**: Specifies what should happen at a join point, such as logging or security checks.
4. **Pointcut**: Specifies when or where advice should be applied, defining the join points where advice should be executed.
5. **Introduction**: Not emphasized in the discussion.
6. **Target Object**: Represents the main character or hero of the application, where cross-cutting concerns are applied.
7. **AOP Proxy**: Represents a proxy object that intercepts method calls to the target object, allowing the execution of additional behavior.
8. **Weaving**: The process of linking aspects with other application types.
9. **Types of Advice**:
   - **Before Advice**: Executes before the join point and is often used for preparation or validation tasks.
   - **After Returning Advice**: Executes after a join point completes normally, allowing access to the return value of the method.
   - **After Throwing Advice**: Executes if a method exits by throwing an exception, enabling exception handling and cleanup tasks.
   - **After (Finally) Advice**: Executes after a join point, regardless of its normal or exceptional completion, commonly used for cleanup tasks.
   - **Around Advice**: Most powerful type of advice that wraps a join point, providing complete control over the method invocation, including the ability to bypass it.

**Implementing AOP in Runtime:**
- Spring AOP primarily operates at runtime, enhancing application behavior dynamically.
- Aspects are applied to target objects through proxies, allowing seamless integration of cross-cutting concerns.

**Conclusion:**
- AOP provides a powerful mechanism for addressing cross-cutting concerns in applications.
- Understanding AOP concepts and their application is crucial for effective implementation.
- The next step involves delving into annotations and configuring AOP aspects for specific use cases.

**Chapter 3: Implementing AOP Concepts - Making it Work**

**Objective:**
- Implement logging for every method call in the `JobService` using Aspect Oriented Programming (AOP).
- Understand the practical application of AOP concepts introduced in the previous chapter.

**Implementing Logging Functionality:**
- The goal is to log every method call in the `JobService`.
- A method named `logMethodCall` in the `LoggingAspect` will be executed before any method call.

**Example Code (Partial):**
```java
// LoggingAspect.java

package com.example.aop;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class LoggingAspect {

    private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);

    @Before("execution(* com.telusko.springbootrest.service.JobService.*(..))")
    public void logMethodCall() {
        logger.info("Method called.");
    }
}
```

**Explanation:**
1. **Defining Aspect:**
   - `@Aspect` annotation marks the class as an aspect.
   - `@Component` makes it a Spring component.

2. **Specifying Advice:**
   - The `@Before` annotation denotes that the advice method (`logMethodCall`) should be executed before the specified join point.
   - In this case, the join point is defined using the "execution" expression.

3. **Understanding Advice Types:**
   - **Before Advice:**
      - Executed before the target method.
      - Helpful for preparation or validation tasks.
      - Used in this example to log method calls.

4. **Execution Expression:**
   - `"execution(* com.telusko.springbootrest.service.JobService.*(..))"`
   - Breakdown of the expression:
      - `execution(*`: Denotes the execution of a method.
      - `com.telusko.springbootrest.service.JobService.*`: Fully qualified class name with a wildcard for any method in the class.
      - `(..))`: Represents any arguments (zero or more).

**Running and Testing:**
- The application is run, and Postman is used to send requests to the `JobService` methods.
- Log messages ("Method called") should be observed in the console for each method call.

**Expression Breakdown:**
- Understanding the breakdown of the execution expression:
   - `execution(* com.telusko.springbootrest.service.JobService.*(..))`:
      - `execution(*`: Any return type.
      - `com.telusko.springbootrest.service.JobService.*`: Any method in the specified class.
      - `(..))`: Any number of arguments.

**Observations and Troubleshooting:**
- If log messages are observed for every action, including Spring's internal actions, narrow down the pointcut expression to specific methods or classes.
- Resolve port conflicts if the application fails to start.

**Conclusion:**
- AOP successfully implemented to log method calls without altering the business logic in the `JobService`.
- The power of AOP demonstrated in separating cross-cutting concerns from the main application logic.
- Next, explore the use of "after" advice types and specify advice for specific methods.

# Chapter 4: Logging Aspect - Detailed Notes

## Logging in JobService

In this chapter, we focus on implementing logging within the `JobService` class. The primary objective is to log method calls, but with the flexibility to target specific methods rather than logging all methods.

### 1. Basic Logging Setup

The initial approach involves logging every method call inside the `JobService`. This can be achieved using an aspect that intercepts method executions.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.JobService.*(..))")
    public void logMethodCall() {
        System.out.println("Method called inside JobService.");
    }
}
```

Explanation:
- `@Aspect` declares the class as an aspect.
- `@Before` specifies that the advice (logMethodCall) should run before the execution of any method in the `JobService` class.
- `execution(* com.example.JobService.*(..))` is a pointcut expression that targets all methods in the `JobService` class.

### 2. Targeting a Specific Method

If we want to log only a specific method, like `updateJob`, we can refine our pointcut expression.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.JobService.updateJob(..))")
    public void logUpdateJobCall() {
        System.out.println("Method called: updateJob");
    }
}
```

Explanation:
- The pointcut expression is now specific to the `updateJob` method.
- This ensures that the advice (`logUpdateJobCall`) only executes before the `updateJob` method.

### 3. Using JoinPoint to Extract Method Information

To provide more meaningful information about the method being called, we can utilize the `JoinPoint` parameter in the advice method.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.JobService.updateJob(..))")
    public void logUpdateJobCall(JoinPoint jp) {
        System.out.println("Method called: " + jp.getSignature().getName());
    }
}
```

Explanation:
- `JoinPoint jp` is a parameter in the advice method, providing information about the intercepted method.
- `jp.getSignature().getName()` retrieves the name of the method being called.

### 4. Handling Multiple Methods

To log calls for multiple methods, a pipe symbol (`|`) can be used in the pointcut expression.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.JobService.updateJob(..)) || execution(* com.example.JobService.getJob(..))")
    public void logUpdateAndGetJobCalls(JoinPoint jp) {
        System.out.println("Method called: " + jp.getSignature().getName());
    }
}
```

Explanation:
- The pointcut expression now targets both the `updateJob` and `getJob` methods.
- The advice (`logUpdateAndGetJobCalls`) will execute before the execution of either of these methods.

### 5. Printing Method Parameters (Optional)

If desired, method parameters can be printed as well.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.JobService.getJob(..))")
    public void logGetJobCall(JoinPoint jp) {
        System.out.println("Method called: " + jp.getSignature().getName());

        // Optional: Print method parameters
        Object[] args = jp.getArgs();
        for (Object arg : args) {
            System.out.println("Parameter: " + arg);
        }
    }
}
```

Explanation:
- `jp.getArgs()` retrieves an array of method arguments.
- Looping through `args` allows for printing each parameter value.

By implementing these logging aspects, we gain the ability to selectively log method calls and extract relevant information about those calls using `JoinPoint`. This enhances the logging process for specific scenarios within the `JobService` class.

# Chapter 5: Advanced Logging Aspects - Detailed Notes

In Chapter 5, the focus is on exploring advanced logging aspects, specifically employing different types of advice such as After, AfterThrowing, and AfterReturning. These aspects allow for more granular control over logging, handling execution after the method call, during exceptions, and after successful execution.

### 1. After Advice

The After advice is demonstrated as an alternative to Before advice. It executes after the target method, regardless of whether an exception occurred or not.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @After("execution(* com.example.JobService.getJob(..)) || execution(* com.example.JobService.updateJob(..))")
    public void logMethodExecuted(JoinPoint jp) {
        System.out.println("Method Executed: " + jp.getSignature().getName());
    }
}
```

Explanation:
- The `@After` annotation signifies that the advice (`logMethodExecuted`) will run after the specified methods (`getJob` and `updateJob`).

### 2. AfterThrowing Advice

This advice executes only when an exception occurs within the targeted method.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @AfterThrowing(pointcut = "execution(* com.example.JobService.getJob(..))", throwing = "ex")
    public void logMethodCrash(Exception ex) {
        System.out.println("Method has some issues: " + ex.getMessage());
    }
}
```

Explanation:
- The `@AfterThrowing` annotation specifies that the advice (`logMethodCrash`) will execute only when an exception occurs during the execution of the `getJob` method.

### 3. AfterReturning Advice

This advice is executed after the successful execution of the targeted method.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @AfterReturning("execution(* com.example.JobService.getJob(..))")
    public void logMethodExecutedSuccess(JoinPoint jp) {
        System.out.println("Method executed successfully: " + jp.getSignature().getName());
    }
}
```

Explanation:
- The `@AfterReturning` annotation ensures that the advice (`logMethodExecutedSuccess`) runs only after the successful execution of the `getJob` method.

### 4. Handling Exceptions and Success

Combining AfterThrowing and AfterReturning allows handling both exceptions and successful executions.

#### Example Code:
```java
@Aspect
@Component
public class LoggingAspect {

    @AfterThrowing(pointcut = "execution(* com.example.JobService.getJob(..))", throwing = "ex")
    public void logMethodCrash(Exception ex) {
        System.out.println("Method has some issues: " + ex.getMessage());
    }

    @AfterReturning("execution(* com.example.JobService.getJob(..))")
    public void logMethodExecutedSuccess(JoinPoint jp) {
        System.out.println("Method executed successfully: " + jp.getSignature().getName());
    }
}
```

Explanation:
- This example combines both `@AfterThrowing` and `@AfterReturning` to handle exceptions and successful executions separately for the `getJob` method.

### 5. Observations and Considerations

- **Logging during Exceptions:** By intentionally causing an exception within the `getJob` method, the `@AfterThrowing` advice is triggered, providing information about the exception.

- **Successful Execution Logging:** When the exception is removed, and the `getJob` method executes successfully, the `@AfterReturning` advice logs the success message.

- **Dynamic Logging Control:** These aspects offer dynamic control over logging, enabling developers to monitor specific methods based on success or failure.

- **Weaving and Proxy:** The interactions between aspects, proxy objects, and the weaving process occur at runtime. The aspect provides a seamless way to observe and log method executions without modifying the actual business logic.

# Chapter 6: Around Advice and Performance Monitoring - Detailed Notes

In Chapter 6, the focus is on the Around advice, a powerful AOP feature that allows developers to control method execution entirely, executing code both before and after the target method. The example provided demonstrates using Around advice to monitor the performance of a specific method (`getJob`) by calculating the time taken for its execution.

### 1. Introduction to Logging Aspect Recap

Before diving into Around advice, a quick recap of the logging aspect is provided. The logging aspect, implemented in a separate class, uses various advice types such as Before, After, AfterThrowing, and AfterReturning to log method calls, exceptions, and successful executions. This sets the stage for introducing the Around advice.

### 2. Use Case for Around Advice: Performance Monitoring

Around advice is introduced as a solution for scenarios where developers need to control both the start and end of method execution. The use case presented is performance monitoring, specifically measuring the time taken for the execution of the `getJob` method.

### 3. Implementing Performance Monitoring with Around Advice

#### Example Code:
```java
@Aspect
@Component
public class PerformanceMonitorAspect {

    private static final Logger LOGGER = LoggerFactory.getLogger(PerformanceMonitorAspect.class);

    @Around("execution(* com.example.JobService.getJob(..))")
    public Object monitorTime(ProceedingJoinPoint jp) throws Throwable {
        long start = System.currentTimeMillis();

        // Call the actual method
        Object obj = jp.proceed();

        long end = System.currentTimeMillis();

        // Log the time taken
        LOGGER.info("Time taken by {}: {} milliseconds", jp.getSignature().getName(), end - start);

        return obj; // Return the result of the method
    }
}
```

Explanation:
- The `@Around` annotation signifies the use of Around advice.
- The pointcut expression specifies that this advice applies to the `getJob` method in the `JobService` class.
- `ProceedingJoinPoint jp` is used to capture the join point and allows calling the actual method using `jp.proceed()`.
- The start and end times are recorded to calculate the time taken.
- The result of the method is returned using `return obj;`.

### 4. Understanding Around Advice Execution

#### Execution Flow:
1. **Before Method Execution:**
   - The advice method (`monitorTime`) is executed before the target method (`getJob`).
   - Start time is recorded.

2. **Actual Method Execution:**
   - The target method (`getJob`) is invoked using `jp.proceed()`.

3. **After Method Execution:**
   - The advice method continues execution after the target method completes.
   - End time is recorded.
   - The time taken for the method execution is calculated and logged.

### 5. Pitfalls and Solutions

#### Issue:
- Initially, the example didn't work as expected, resulting in zero time taken.

#### Solution:
- To address this, the `obj` returned by `jp.proceed()` was not being returned from the advice method.

#### Issue:
- The example encountered an error due to potential exceptions within the target method.

#### Solution:
- The `monitorTime` method is updated to declare `throws Throwable` to handle exceptions properly.

### 6. Testing Performance Monitoring

- The example is tested by sending requests using Postman.
- The time taken for the `getJob` method is logged and displayed in milliseconds.
- Subsequent requests demonstrate the impact of caching on execution time.

### 7. Enriching Logging Output

- The example is extended to include the method name in the log output for a more detailed view of performance.
- The `jp.getSignature().getName()` method is used to retrieve the method name.

# Chapter 6 (Continued): Around Advice for Input Validation

In the continuation of Chapter 6, the focus shifts to utilizing Around advice for input validation. The goal is to demonstrate how to intercept method calls and validate input parameters before they reach the actual service method. The example uses the `getJob` method from the `JobService` class as a target for validation.

### 1. Introduction to Input Validation Use Case

The chapter begins by introducing the concept of input validation and its significance. The use case involves validating the `postId` parameter before it reaches the `getJob` method. The example demonstrates handling scenarios where users might mistakenly input negative values.

### 2. Creating a ValidationAspect

A new aspect, named `ValidationAspect`, is created to encapsulate the input validation logic. The `@Component` and `@Aspect` annotations are used to define it as a Spring component and aspect, respectively.

#### Example Code:
```java
@Aspect
@Component
public class ValidationAspect {

    private static final Logger LOGGER = LoggerFactory.getLogger(ValidationAspect.class);

    @Around("execution(* com.example.JobService.getJob(int)) && args(postId)")
    public Object validateAndUpdate(ProceedingJoinPoint jp, int postId) throws Throwable {
        if (postId < 0) {
            // Update the postId to its positive counterpart
            postId = -postId;

            // Log the validation and update
            LOGGER.info("PostId is negative, updating it. New value: {}", postId);
        }

        // Call the actual method with the updated argument
        return jp.proceed(new Object[]{postId});
    }
}
```

Explanation:
- The `@Around` annotation signifies the use of Around advice.
- The pointcut expression specifies that this advice applies to the `getJob` method in the `JobService` class, expecting an integer parameter named `postId`.
- The `validateAndUpdate` method performs the input validation logic, updating the `postId` if it is negative.
- The actual method (`getJob`) is called using `jp.proceed(new Object[]{postId})`.

### 3. Understanding Input Validation Execution Flow

#### Execution Flow:
1. **Before Method Execution:**
   - The advice method (`validateAndUpdate`) is executed before the target method (`getJob`).
   - The `postId` parameter is checked for negativity.

2. **Validation and Update:**
   - If `postId` is negative, it is updated to its positive counterpart.
   - A log statement is printed indicating the validation and update.

3. **Actual Method Execution:**
   - The target method (`getJob`) is invoked with the updated `postId` parameter.

### 4. Testing Input Validation

The example is tested using Postman, sending requests with different `postId` values:
- For `postId = 4`, the request works successfully.
- For `postId = -4`, the aspect intercepts the call, updates the value, and logs the validation.

### 5. Exploring Further Use Cases

The chapter concludes by hinting at the versatility of AOP, mentioning that not only can input be validated, but output can also be manipulated using similar techniques. The flexibility of AOP allows developers to apply aspects for various purposes.

### 6. Conclusion

- Around advice provides a powerful mechanism for intercepting method calls, allowing developers to validate input parameters before reaching the actual method.
- The example demonstrates the use of Around advice to perform input validation and updates.

# **Section 4: Spring Security**
# Chapter 1: Implementing Spring Security

## Introduction
- **Presenter**: Navin Reddy
- **Objective**: Implement Spring Security to secure a Spring-based web application.
- Spring Security enhances security for various types of applications, including enterprise, desktop, and web applications.

## Understanding Spring Security
- Spring Security is used to secure Spring-based applications.
- It provides features for authentication, authorization, and protection against common security vulnerabilities.
- In this example, the focus is on securing a web application using Spring Security.

## Setting Up the Project
- **Project Creation**:
  - Spring Boot is chosen for its ease of configuration.
  - The project is set up using Spring Starter Project in Spring Boot.
  - The project name is specified as "secure app" under the package `com.telusko.secureapp`.

```java
@SpringBootApplication
public class SecureAppApplication {

	public static void main(String[] args) {
		SpringApplication.run(SecureAppApplication.class, args);
	}
}
```

## Creating Controllers and Views
- A basic controller (`HomeController`) is created to handle homepage requests.
- The homepage is rendered using a JSP page named `home.jsp`.
- Spring Boot is configured to work with JSP by adding the Tomcat Jasper dependency.

```java
@Controller
public class HomeController {

    @RequestMapping("/")
    public String home() {
        return "home";
    }
}
```

## Adding Spring Security
- Spring Security is integrated into the project by adding the appropriate dependency in the `pom.xml` file.
- Upon adding Spring Security, the application automatically generates a login page for authentication.
- By default, the username is "user", and the password is autogenerated.
- Access to the homepage is restricted to authenticated users.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## Exploring Default Security Configuration
- The default username is "user", and the password is autogenerated.
- Upon login, users are authenticated and granted access to the homepage.
- Attempts to access other pages without authentication result in access denied.

## Next Steps
- **Customization of User Credentials**:
  - Change the default username and password.
- **Data Fetching from Database**:
  - Integrate Spring Security with a database for user authentication.
- These topics will be covered in upcoming videos.

## Conclusion
- Spring Security provides out-of-the-box security features for Spring applications.
- Integration with Spring Boot simplifies the setup process.
- The next steps involve customizing security configurations and integrating with databases for user authentication.

By following these steps, users can create secure Spring applications with minimal effort.

# Chapter 2: Customizing User Authentication in Spring Security

## Introduction
- In the previous video, Spring Security was implemented, and the default login credentials were used.
- This chapter focuses on customizing user authentication by configuring Spring Security with a specific username and password.

## Default Login Page
- Initially, the application displayed a login page with the username "user" and an autogenerated password.
- Users were authenticated using these default credentials.

## Customizing User Authentication
- The goal is to define a custom username and password.
- Configuration is achieved using the `AppSecurityConfig` class, which extends `WebSecurityConfigurerAdapter`.

```java
@Configuration
@EnableWebSecurity
public class AppSecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    @Override
    protected UserDetailsService userDetailsService() {
        List<UserDetails> users = new ArrayList<>();
        users.add(User.withDefaultPasswordEncoder().username("navin").password("1234").roles("USER").build());
        return new InMemoryUserDetailsManager(users);
    }
}
```

### Explanation:
- `@Configuration`: Indicates that this class provides configuration.
- `@EnableWebSecurity`: Enables web security for the application.
- `WebSecurityConfigurerAdapter`: A base class for configuring web security.

### Custom User Configuration
- The `userDetailsService()` method is overridden to provide custom user details.
- `InMemoryUserDetailsManager`: Manages user details in memory.
- A list of `UserDetails` objects is created with custom usernames, passwords, and roles.

### Deprecated Method
- The `withDefaultPasswordEncoder()` method is deprecated but used for simplicity.
- In future implementations, other password encoders should be considered.

## Testing Custom Authentication
- The application is relaunched with the custom configuration.
- Users now need to log in with the username "navin" and password "1234".

### Code Execution:
```java
@Bean
@Override
protected UserDetailsService userDetailsService() {
    List<UserDetails> users = new ArrayList<>();
    users.add(User.withDefaultPasswordEncoder().username("navin").password("1234").roles("USER").build());
    return new InMemoryUserDetailsManager(users);
}
```

## Future Steps
- **Transition to Database Authentication**:
  - The current implementation uses in-memory authentication.
  - Future videos will demonstrate fetching user credentials from a database.

## Conclusion
- Customizing user authentication in Spring Security involves extending `WebSecurityConfigurerAdapter` and providing a custom `UserDetailsService`.
- The application now authenticates users with the specified username and password.
- Upcoming videos will explore more advanced authentication mechanisms, including database integration.

# Chapter 3: Integrating Database Authentication in Spring Security

## Introduction
- Recap of previous chapters: Initially used Spring Security with default credentials, then customized it with in-memory authentication.
- The goal now is to extract user data, specifically from a database.

## Database Authentication Setup
- The desire is to handle user authentication from the database.
- Initial step: Disable the existing in-memory user configuration.

### Code Execution:
```java
// Inside AppSecurityConfig class
@Override
protected UserDetailsService userDetailsService() {
    // Disable existing in-memory configuration
    return super.userDetailsService();
}
```

## Steps for Database Authentication
1. **Create an Authentication Provider Method:**
   - Create a method that acts on the `AuthenticationProvider` object.
   - The method should return an object of type `AuthenticationProvider`.
   - Import the necessary packages.

### Code Execution:
```java
@Bean
public AuthenticationProvider authenticationProvider() {
    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(userDetailsService());
    return provider;
}
```

2. **Configure Database Properties:**
   - Add database properties for connecting to the database.
   - Include dependencies for Spring Data JPA and the appropriate database connector (e.g., MySQL).

### Code Execution:
```xml
<!-- pom.xml -->
<dependencies>
    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <!-- MySQL Connector -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>
```

3. **Create Entity Class for User Data:**
   - Create a class representing the user data in the database.
   - Include fields for username, password, etc.

### Code Execution:
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String password;
    // ... other fields, getters, setters
}
```

4. **Create a User Repository Interface:**
   - Create a repository interface for managing user data using Spring Data JPA.
   - Include a method to find a user by username.

### Code Execution:
```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}
```

5. **Create UserDetailsService Implementation:**
   - Implement the `UserDetailsService` interface to fetch user details from the database.

### Code Execution:
```java
@Service
public class UserDataService implements UserDetailsService {
    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Optional<User> userOptional = userRepository.findByUsername(username);
        User user = userOptional.orElseThrow(() -> new UsernameNotFoundException("User not found"));

        return new UserPrincipal(user);
    }
}
```

6. **Create UserPrincipal Class:**
   - Create a class implementing the `UserDetails` interface.
   - This class represents the authenticated user with roles and permissions.

### Code Execution:
```java
public class UserPrincipal implements UserDetails {
    private final User user;

    public UserPrincipal(User user) {
        this.user = user;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return Collections.singleton(new SimpleGrantedAuthority("USER"));
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUsername();
    }

    // ... other UserDetails methods
}
```

7. **Configure Password Encoder:**
   - Configure the password encoder for the `DaoAuthenticationProvider`.

### Code Execution:
```java
@Bean
public PasswordEncoder passwordEncoder() {
    return NoOpPasswordEncoder.getInstance(); // For demo purposes, use a more secure encoder in production
}

// Inside AppSecurityConfig class
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.authenticationProvider(authenticationProvider());
}
```

## Conclusion
- The process involves configuring database properties, creating an entity class, a repository interface, a `UserDetailsService` implementation, and a `UserPrincipal` class.
- The application now authenticates users based on data retrieved from the database.
- Further enhancements, such as using a secure password encoder, are recommended for production use.

# Chapter 4: Implementing Password Hashing with Spring Security

## Introduction
- Recap: Implemented Spring Security with database authentication.
- Highlight the issue of storing passwords in plain text and the need for encryption.

## Password Hashing Techniques
- **Ciphertext**: Not secure as it can be decoded.
- **Hashing**: Provides better security, but which hashing algorithm to choose?
- **MD5 and SHA-1**: Not recommended due to vulnerabilities.
- **bcrypt**: Recommended for password hashing, as it supports multiple rounds of hashing.

### Example of Using bcrypt
- Demonstration using an online bcrypt generator.
- Rounds of hashing determine the strength of the encryption.

## Integrating bcrypt with Spring Security
- Default behavior in Spring Security is to use plaintext.
- Integrate bcrypt by using `BCryptPasswordEncoder` provided by Spring.

### Code Execution:
```java
// Inside AppSecurityConfig class
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}

// Update the authenticationProvider() method
@Bean
public AuthenticationProvider authenticationProvider() {
    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(userDetailsService());
    provider.setPasswordEncoder(passwordEncoder()); // Set password encoder
    return provider;
}
```

- Explain the significance of the `BCryptPasswordEncoder` in securing passwords.

### Example of Using BCryptPasswordEncoder in Code:
```java
public class PasswordEncoderExample {
    public static void main(String[] args) {
        BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();

        String plainPassword = "1234";
        String hashedPassword = encoder.encode(plainPassword);

        System.out.println("Plain Password: " + plainPassword);
        System.out.println("Hashed Password: " + hashedPassword);

        // Verify password
        boolean matches = encoder.matches(plainPassword, hashedPassword);
        System.out.println("Password Matches: " + matches);
    }
}
```

## Testing Password Hashing in Spring Boot Application
- Modify the existing code to use bcrypt.
- Showcase the login functionality with hashed passwords.

### Code Execution:
```java
// Inside AppSecurityConfig class
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.authenticationProvider(authenticationProvider());
}
```

- Run the application and test the login with hashed passwords.
- Emphasize the importance of storing hashed passwords in the database.

## Conclusion
- Upgraded security by implementing bcrypt for password hashing.
- Discussed the integration of `BCryptPasswordEncoder` with Spring Security.
- Demonstrated the usage of bcrypt in a Spring Boot application.
- Next chapter: Customizing the login page in Spring Security.

# Chapter 5: Customizing Login and Logout Pages in Spring Security

## Introduction
- Recap: Securing the application with Spring Security.
- Discuss the default login page provided by Spring and the need for customization.

## Creating Custom Login and Logout Pages
- Created simple login and logout pages.
- Emphasized the simplicity of the pages and the possibility of customization.

### Example Login Page:
```html
<!-- login.jsp -->
<form action="login" method="post">
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required/><br/>

    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required/><br/>

    <button type="submit">Submit</button>
</form>
```

### Example Logout Page:
```html
<!-- logout.jsp -->
<h2>Logout Successful</h2>
<p>Thank you for using our application.</p>
<a href="/">Go to Homepage</a>
```

## Customizing Login and Logout Configuration
- Demonstrated the need to inform Spring Framework about the custom pages.
- Override `configure` method in `WebSecurityConfigurerAdapter` class.

### Code Execution:
```java
// Inside AppSecurityConfig class
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .csrf().disable() // Disable CSRF
        .formLogin()
            .loginPage("/login") // Specify custom login page
            .permitAll()
        .and()
        .logout()
            .invalidateHttpSession(true)
            .clearAuthentication(true)
            .logoutSuccessUrl("/logout-success") // Specify custom logout success page
            .permitAll();
}
```

## Handling Custom Login and Logout in Controller
- Added methods to the controller to handle login and logout requests.

### Example Controller Methods:
```java
// Inside HomeController class
@GetMapping("/login")
public String loginPage() {
    return "login"; // Returns the login page
}

@GetMapping("/logout-success")
public String logoutPage() {
    return "logout"; // Returns the logout page
}
```

## Testing Custom Login and Logout
- Ran the application and demonstrated the custom login and logout functionality.
- Highlighted the changes made to the login form to handle Spring Security exceptions.

### Example Error Handling in Login Page:
```html
<!-- login.jsp -->
<!-- Display error message if authentication fails -->
<c:if test="${param.error != null}">
    <p style="color: red;">Bad credentials. Please try again.</p>
</c:if>
```

## Conclusion
- Successfully customized login and logout pages.
- Discussed the simplicity and flexibility of creating custom pages.
- Highlighted the importance of informing Spring about custom pages in the security configuration.
- Demonstrated error handling in the custom login page.
- Next steps: Further customization of the login form and handling user roles.

# Chapter 6: Integrating OAuth2 for Google Login in Spring Security

## Introduction
- Recap of the need for user authentication and protection of user data.
- Introduction to OAuth2 as an authorization framework.
- Decision to implement Google login using OAuth2 for user authentication.

## Adding OAuth2 Dependency
- Explanation of the need for OAuth2 library integration.
- Demonstration of adding OAuth2 dependency to the project.

### Example Dependency Addition:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
```

## Configuring OAuth2 Properties
- Introduction to OAuth2 client configuration for Google.
- Explanation of necessary configurations in the `application.properties` file.

### Example OAuth2 Configuration in `application.properties`:
```properties
# OAuth2 Configuration for Google Login
spring.security.oauth2.client.registration.google.client-id=your-client-id
spring.security.oauth2.client.registration.google.client-secret=your-client-secret
spring.security.oauth2.client.registration.google.scope=openid,profile,email
```

## Configuring Spring Security for OAuth2
- Explanation of configuring Spring Security to use OAuth2 for Google login.
- Code demonstration of configuring OAuth2 in the `SecurityConfig` class.

### Example Code in `SecurityConfig` Class:
```java
// Inside SecurityConfig class
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/login", "/error").permitAll()
                .anyRequest().authenticated()
            .and()
            .oauth2Login()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .invalidateHttpSession(true)
                .clearAuthentication(true)
                .logoutSuccessUrl("/logout-success")
                .permitAll();
    }
}
```

## Creating Google Developer Account and Obtaining Client ID and Secret
- Guidance on creating a Google Cloud account for OAuth2 configuration.
- Step-by-step demonstration of creating OAuth client ID and client secret.

## Testing Google Login with OAuth2
- Demonstration of the Google login process using OAuth2.
- Explanation of the redirection to Google's login page.
- Verifying the user information after successful login.

### Example Testing URLs:
- Google Login: `http://localhost:8080/login`
- User Information: `http://localhost:8080/user`

## Conclusion
- Successful integration of OAuth2 for Google login in Spring Security.
- Importance of securing user data and providing authentication options.
- Recap of the configuration steps for OAuth2 in Spring Boot.
- Next steps: Further customization, handling additional OAuth providers, and securing user data.
