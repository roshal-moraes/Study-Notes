# Java Web, Date/Time, JARs & Records — Interview Prep Edition

> **Goal:** Turn the original notes into a practical interview-preparation guide. The flow is deliberately different from a lecture transcript: learn the idea, see a small example, understand the trap, then test yourself.
>
> **Modernization note:** Examples below use modern Jakarta EE terminology (`jakarta.*`) where applicable. The historical `javax.*` names are still explained because interviews and legacy codebases may use them.

---

# Part 1 — Java Date and Time API

## Chapter 1 — `java.time`: The Modern Date/Time Model

### 1.1 Why Java needed a new date/time API

Before Java 8, Java developers commonly used `java.util.Date` and `java.util.Calendar`. Those APIs are mutable, easier to misuse, and awkward for many common operations.

Java 8 introduced `java.time`, designed around separate types for separate concepts. The main classes are immutable and thread-safe. citeturn618800search0

Think about a booking system:

- A birthday is a **date**.
- A shop opening time is a **time**.
- A meeting in Brussels is a **date + time + time zone**.
- A timestamp stored by a server is often an **instant**.

Using the correct type is more important than memorizing methods.

### 1.2 The core types

| Type | Represents | Example |
|---|---|---|
| `LocalDate` | Date without time zone | `2026-09-19` |
| `LocalTime` | Time without date/zone | `14:30` |
| `LocalDateTime` | Date + time, no zone | `2026-09-19T14:30` |
| `Instant` | Point on the UTC timeline | `2026-09-19T12:30:00Z` |
| `ZonedDateTime` | Date + time + named zone | `Europe/Brussels` |
| `OffsetDateTime` | Date + time + UTC offset | `+02:00` |
| `Period` | Date-based amount | `2 years, 3 months` |
| `Duration` | Time-based amount | `90 minutes` |
| `ZoneId` | Time-zone rules/identity | `Europe/Brussels` |

### 1.3 `LocalDate`

Use `LocalDate` when the concept is a calendar date rather than an instant on the global timeline.

```java
LocalDate today = LocalDate.of(2026, 9, 19);
LocalDate nextWeek = today.plusWeeks(1);

System.out.println(today);     // 2026-09-19
System.out.println(nextWeek);  // 2026-09-26
```

A major interview point: date/time objects are immutable.

```java
LocalDate date = LocalDate.of(2026, 9, 19);
date.plusDays(1);

System.out.println(date); // 2026-09-19
```

`plusDays()` returns a new object.

```java
LocalDate tomorrow = date.plusDays(1);
```

### 1.4 `LocalTime`

Use it when the date does not matter.

```java
LocalTime lunch = LocalTime.of(13, 30);
LocalTime later = lunch.plusHours(2);

System.out.println(later); // 15:30
```

### 1.5 `LocalDateTime`

Useful for a date and time where no time zone is part of the business meaning.

```java
LocalDateTime appointment =
        LocalDateTime.of(2026, 9, 19, 14, 30);
```

**Interview trap:** `LocalDateTime` does **not** mean “a timestamp in some zone.” It has no zone or offset, so it cannot tell you which instant this represents globally.

### 1.6 `Instant`

`Instant` represents a point on the global timeline.

```java
Instant now = Instant.now();
```

This is often a better type for events such as:

- order created time
- login time
- message received time
- audit timestamps

A common production model is:

```text
Store an Instant for machine events.
Convert to a ZonedDateTime when displaying it to a user.
```

### 1.7 `ZonedDateTime`

When time-zone rules matter, use a named zone.

```java
ZonedDateTime brussels =
        ZonedDateTime.now(ZoneId.of("Europe/Brussels"));

ZonedDateTime newYork =
        brussels.withZoneSameInstant(ZoneId.of("America/New_York"));
```

The important phrase is **same instant**.

#### `withZoneSameInstant()` vs `withZoneSameLocal()`

This is a classic interview question.

```java
ZonedDateTime brussels =
        ZonedDateTime.of(2026, 9, 19, 14, 0, 0, 0,
                ZoneId.of("Europe/Brussels"));

System.out.println(
    brussels.withZoneSameInstant(ZoneId.of("America/New_York"))
);
```

`withZoneSameInstant()` keeps the same moment and changes the local clock representation.

`withZoneSameLocal()` attempts to keep the local date/time fields and changes the zone, which can therefore represent a different instant.

**Memory trick:**

> Same **instant** = same moment in the world.
>
> Same **local** = same clock reading, potentially a different moment.

### 1.8 `Period` vs `Duration`

This distinction is extremely common in interviews.

`Period` is date-based:

```java
Period period = Period.of(1, 2, 10);
// 1 year, 2 months, 10 days
```

`Duration` is time-based:

```java
Duration duration = Duration.ofHours(36);
```

Conceptually:

```text
Period   -> years / months / days
Duration -> seconds / nanoseconds
```

### 1.9 Formatting and parsing

Use `DateTimeFormatter`.

```java
DateTimeFormatter formatter =
        DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm");

LocalDateTime dateTime =
        LocalDateTime.of(2026, 9, 19, 14, 30);

String text = dateTime.format(formatter);
System.out.println(text); // 19-09-2026 14:30
```

Parsing goes in the opposite direction:

```java
LocalDateTime parsed =
        LocalDateTime.parse("19-09-2026 14:30", formatter);
```

### 1.10 `ChronoUnit`

Use it when you want a simple difference between temporal values.

```java
LocalDate start = LocalDate.of(2026, 9, 1);
LocalDate end   = LocalDate.of(2026, 9, 19);

long days = ChronoUnit.DAYS.between(start, end);
System.out.println(days); // 18
```

### 1.11 A very useful testability improvement: `Clock`

Avoid hard-coding `LocalDate.now()` everywhere in business logic when tests need deterministic time.

```java
Clock clock = Clock.fixed(
        Instant.parse("2026-09-19T12:00:00Z"),
        ZoneOffset.UTC
);

LocalDate today = LocalDate.now(clock);
```

In production, inject a normal `Clock.systemUTC()` or an application-specific clock. In tests, use a fixed clock.

### Interview Questions

**Q1. Why are `java.time` classes considered safer than many legacy date/time classes?**

Because the core date/time classes are immutable and thread-safe, and the types model different concepts explicitly. citeturn618800search0

**Q2. What is the difference between `LocalDateTime` and `Instant`?**

`LocalDateTime` is a local calendar date/time without zone or offset. `Instant` represents an exact point on the UTC timeline.

**Q3. Why is `LocalDateTime` dangerous for a global meeting?**

Because the same local date/time can correspond to different instants depending on the time zone.

**Q4. When would you choose `ZonedDateTime`?**

When the named time zone and its rules matter, such as a user appointment in `Europe/Brussels`.

**Q5. Why is `DateTimeFormatter` generally preferred over sharing a legacy `SimpleDateFormat` instance between threads?**

`DateTimeFormatter` is immutable and thread-safe.

### Tricky Questions

**Q: What does this print?**

```java
LocalDate d = LocalDate.of(2026, 1, 31);
System.out.println(d.plusMonths(1));
```

**Answer:** `2026-02-28`. The API resolves an invalid resulting date to the last valid day of the month.

**Q: Does this mutate `d`?**

```java
d.plusDays(10);
```

No. You ignored the returned object.

### Key Takeaways

1. Choose the type based on the meaning of the value.
2. Use `Instant` for a machine timeline point.
3. Use `ZonedDateTime` when a named zone matters.
4. Remember `Period` vs `Duration`.
5. Date/time objects are immutable.

---

# Part 2 — Web Fundamentals

## Chapter 2 — How the Web Works

### 2.1 Client and server

A basic web request looks like:

```text
Browser / Client
      |
      | HTTP request
      v
Web server / application
      |
      | HTTP response
      v
Browser / Client
```

A browser might request:

```text
GET /books/42 HTTP/1.1
Host: example.com
Accept: application/json
```

The server might respond:

```text
HTTP/1.1 200 OK
Content-Type: application/json

{"id":42,"title":"Java"}
```

### 2.2 HTTP request anatomy

An HTTP request can contain:

```text
Request line
Headers
Blank line
Optional body
```

Example:

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

email=alice@example.com&password=secret
```

Important pieces:

- **Method** — what kind of action is requested.
- **Target/path** — which resource is addressed.
- **Headers** — metadata.
- **Body** — optional payload.

### 2.3 GET vs POST

The original notes use GET for some state-changing actions. That is an important place to improve the design.

A useful interview rule is:

```text
GET  -> retrieve a representation / read data
POST -> submit data / perform a state-changing action
```

For example, browsing books:

```http
GET /books
```

Saving a bookmark:

```http
POST /bookmarks
```

This is clearer than using a GET link to change application state.

### 2.4 Query parameters vs request body

```text
/search?query=java&page=2
```

The values after `?` are query parameters.

A POST request may carry form data or JSON in the body.

```json
{
  "title": "Effective Java"
}
```

**Interview trap:** POST data is not automatically secure merely because it is in the body. HTTPS is what protects data in transit.

### 2.5 Common HTTP methods

| Method | Typical purpose |
|---|---|
| GET | Retrieve |
| POST | Create/process/submit |
| PUT | Replace a resource representation |
| PATCH | Partial update |
| DELETE | Delete |

Interviewers may ask about **idempotency**. Very roughly, an operation is idempotent when repeating the same request has the same intended effect as doing it once. GET, PUT, and DELETE are defined as idempotent methods; POST generally is not.

### 2.6 HTTP status codes

Remember the groups first:

```text
1xx -> informational
2xx -> success
3xx -> redirection
4xx -> client-side error
5xx -> server-side error
```

Common examples:

- `200 OK`
- `201 Created`
- `204 No Content`
- `301/302` redirects
- `400 Bad Request`
- `401 Unauthorized`
- `403 Forbidden`
- `404 Not Found`
- `409 Conflict`
- `500 Internal Server Error`

### 2.7 URL anatomy

```text
https://example.com:8080/books?id=42#details
\__/   \_________/ \___/ \_______/ \_____/
scheme    host      port   query    fragment
```

Interview questions often test the difference between **query parameters** and a **fragment**. The fragment (`#details`) is primarily handled by the user agent and is not normally sent to the server in the HTTP request.

### 2.8 HTTP headers

Headers communicate metadata.

Request examples:

```http
Accept: application/json
Authorization: Bearer ...
Cookie: SESSION=abc123
Content-Type: application/json
```

Response examples:

```http
Content-Type: application/json
Cache-Control: no-cache
Set-Cookie: SESSION=abc123; HttpOnly; Secure
```

### 2.9 HTML

HTML describes document structure; CSS controls presentation; JavaScript adds client-side behavior.

Simple example:

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Books</title>
</head>
<body>
    <h1>My Books</h1>

    <form action="/login" method="post">
        <input name="email" type="email">
        <input name="password" type="password">
        <button type="submit">Login</button>
    </form>
</body>
</html>
```

### Interview Questions

**Q1. What is the difference between a URL and URI?**

A URL is a URI that identifies a resource by describing how to locate it. In interviews, the terms are often used loosely, but URI is the broader term.

**Q2. Is GET data always visible?**

Query parameters are visible in the URL and may appear in browser history, logs, and referrers. Do not put secrets in URLs.

**Q3. What is the difference between `401` and `403`?**

`401` indicates that authentication is needed or has not succeeded. `403` means the server understood the request but refuses to authorize it.

**Q4. Why shouldn't a bookmark-save action normally be GET?**

Because GET is intended for safe retrieval and may be triggered by crawlers, prefetching, browser behavior, or accidental link activation. A state-changing operation should use a method such as POST.

### Tricky Question

```text
GET /deleteUser?id=42
```

It works technically. Is it a good REST/HTTP design?

**Answer:** No. It makes a state-changing/destructive operation a GET request. Use an appropriate state-changing method and authorization checks.

---

# Part 3 — Java EE to Jakarta EE

## Chapter 3 — What Changed and Why It Matters

### 3.1 Java EE vs Jakarta EE

The original notes use **Java EE** terminology. Today, the platform is called **Jakarta EE**.

The important migration point is:

```text
Java EE 8 / javax.*
        |
        | Jakarta EE 9 — namespace transition
        v
Jakarta EE 9+ / jakarta.*
```

Jakarta EE 9 introduced the `jakarta.*` namespace to replace the `javax.*` namespace for Jakarta EE specifications. The change made Jakarta EE 9 **not source-compatible or binary-compatible** with the previous `javax.*` APIs. citeturn274534search2turn274534search4

### 3.2 Release history worth remembering

| Release | Interview-relevant point |
|---|---|
| Java EE 8 | Final Java EE-era API namespace: `javax.*` |
| Jakarta EE 8 (2019) | Transition to Eclipse Foundation, still `javax.*` |
| Jakarta EE 9 (2020) | `javax.*` → `jakarta.*` namespace change |
| Jakarta EE 10 (2022) | Major platform updates; Java SE 8 support removed |
| Jakarta EE 11 (2025) | Java SE 17 minimum; Jakarta Data; more modernization |
| Jakarta EE 12 | Under development as of September 2026 |

Jakarta EE 11 was released on **June 26, 2025** and requires Java SE 17 or later. citeturn778678search0turn339883search1

### 3.3 Servlet versions that matter

For the current stable Jakarta EE 11 generation:

- Jakarta Servlet **6.1**
- Jakarta Pages **4.0**
- Jakarta Standard Tag Library **3.0**
- Java SE **17+** minimum for Jakarta EE 11

Jakarta Servlet 6.1 is part of Jakarta EE 11. citeturn339883search8turn778678search2

### 3.4 Tomcat is not the whole Jakarta EE platform

This is a very common interview question.

Apache Tomcat is an implementation of a **subset** of Jakarta EE technologies, centered on web technologies such as Servlet, JSP/Jakarta Pages, WebSocket, and related APIs. Tomcat is not a complete Jakarta EE platform implementation. citeturn274534search1

As of the current Tomcat release line in September 2026:

```text
Tomcat 11.0.x
    -> Servlet 6.1
    -> JSP / Jakarta Pages 4.0
    -> Java 17+
```

The official Tomcat version guide currently lists Tomcat 11.0.x as the supported line implementing Servlet 6.1. citeturn274534search1

### 3.5 Modern import example

Old Java EE / pre-Jakarta code:

```java
import javax.servlet.http.HttpServlet;
```

Modern Jakarta code:

```java
import jakarta.servlet.http.HttpServlet;
```

This is not just a spelling preference. The package change affects imports, dependencies, descriptors, and binary compatibility. citeturn274534search4

### Interview Questions

**Q1. What is the biggest migration difference between Java EE 8 and Jakarta EE 9?**

The namespace change from `javax.*` to `jakarta.*`.

**Q2. Is Jakarta EE 9 binary compatible with Java EE 8?**

No. The namespace change breaks source and binary compatibility for the affected APIs. citeturn274534search4

**Q3. Can Tomcat run a full Jakarta EE application with EJB, CDI, Persistence, Messaging, and everything else by itself?**

Not as a complete Jakarta EE platform. Tomcat provides the web-container side of the stack; other Jakarta EE servers provide broader platform coverage.

---

# Part 4 — Servlet and Container Fundamentals

## Chapter 4 — From HTTP Request to Java Code

### 4.1 Why a servlet has no `main()`

A standalone Java program may start with:

```java
public static void main(String[] args) {
}
```

A servlet does not normally start that way. The **servlet container** manages it.

The container:

1. Loads servlet classes.
2. Creates servlet instances.
3. Initializes them.
4. Receives HTTP requests.
5. Calls servlet methods.
6. Manages the servlet's lifecycle.
7. Eventually destroys the servlet.

### 4.2 Minimal modern servlet

```java
package com.example.books;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebServlet("/books")
public class BooksServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/plain");
        response.getWriter().println("Books");
    }
}
```

The `@WebServlet` annotation provides mapping metadata to the container.

### 4.3 Annotation vs `web.xml`

You can configure servlet mappings with annotations:

```java
@WebServlet("/books")
```

or with deployment metadata in `WEB-INF/web.xml`.

Using annotations is usually simpler for straightforward applications, while `web.xml` remains useful when explicit deployment configuration is required or maintained by a legacy application.

### 4.4 Request lifecycle

A simplified flow:

```text
Browser
  |
  | GET /books
  v
Tomcat
  |
  | URL mapping
  v
BooksServlet
  |
  | doGet()
  v
Response
```

### 4.5 `service()` vs `doGet()` / `doPost()`

The container invokes the servlet's request-processing mechanism, and `HttpServlet` dispatches based on the HTTP method.

So conceptually:

```text
service(request, response)
        |
        +---- GET  -> doGet()
        +---- POST -> doPost()
        +---- PUT  -> doPut()
        +---- DELETE -> doDelete()
```

You usually override the method that matches your endpoint's behavior rather than overriding `service()` yourself.

### 4.6 Servlet lifecycle

The important lifecycle methods are:

```text
constructor
    ↓
init()
    ↓
service() / doGet() / doPost() ...
    ↓
destroy()
```

Initialization happens before the servlet handles requests. `destroy()` is called when the container removes the servlet from service.

### 4.7 `loadOnStartup`

Without explicit eager startup, a container may initialize a servlet when it is first needed.

With:

```java
@WebServlet(
    urlPatterns = "/books",
    loadOnStartup = 1
)
```

the container is instructed to initialize that servlet during application startup.

### 4.8 The biggest servlet interview trap: thread safety

A servlet container may process multiple requests concurrently using the same servlet instance.

Therefore, avoid request-specific mutable instance fields:

```java
public class BadServlet extends HttpServlet {
    private String currentUser; // Dangerous shared state
}
```

Prefer local variables:

```java
protected void doGet(...) {
    String currentUser = request.getParameter("user");
}
```

The servlet instance itself is managed by the container; request data belongs to the request, not the servlet object's shared state.

### 4.9 Forward vs redirect

This is one of the most important servlet questions.

**Forward:**

```java
request.getRequestDispatcher("/WEB-INF/books.jsp")
       .forward(request, response);
```

The server transfers processing internally. The same request/response objects continue. The browser URL does not change.

**Redirect:**

```java
response.sendRedirect(request.getContextPath() + "/books");
```

The server tells the client to make another request. The browser URL changes.

Memory trick:

```text
forward  = server-side handoff
redirect = client makes another request
```

### Interview Questions

**Q1. Who calls `doGet()`?**

The servlet container, through `HttpServlet`'s request dispatching.

**Q2. How many servlet instances are there?**

Do not memorize the oversimplified phrase “exactly one servlet globally.” The practical model for a normal servlet declaration is one servlet instance managed for that application mapping, while many requests may execute concurrently against it. Container configuration can affect lifecycle behavior.

**Q3. Why shouldn't you store request-specific data in servlet instance fields?**

Because concurrent requests can access the same servlet instance, causing races and data corruption.

**Q4. Difference between forward and redirect?**

Forward is an internal server-side transfer using the same request. Redirect causes a new client request.

**Q5. Why use POST instead of GET to save a bookmark?**

Because saving changes server state, and GET should be safe/read-oriented.

### Tricky Question

```java
private int counter = 0;

protected void doGet(...) {
    counter++;
}
```

Is it thread-safe?

No. Multiple requests may execute concurrently, and `counter++` is not an atomic operation for shared mutable state.

---

# Part 5 — Tomcat and Deployment

## Chapter 5 — Understanding Tomcat Without Memorizing IDE Clicks

### 5.1 What Tomcat does

Tomcat acts as the servlet container that:

- receives HTTP traffic,
- maps requests to servlets,
- manages servlet lifecycles,
- processes web applications,
- supports Jakarta Servlet and Jakarta Pages/JSP functionality.

### 5.2 Modern baseline

For a current Jakarta-based Servlet application:

```text
JDK 17+
Tomcat 11
jakarta.servlet.*
Jakarta Pages / JSP 4.0 generation
```

Tomcat 11.0.x currently implements Servlet 6.1 and supports Java 17 and later. citeturn274534search1

### 5.3 Important Tomcat directories

A simplified layout:

```text
Tomcat/
├── bin/
├── conf/
├── lib/
├── logs/
├── temp/
├── webapps/
└── work/
```

Common meanings:

- `bin` — startup/shutdown scripts and utilities
- `conf` — configuration
- `webapps` — deployed web applications
- `logs` — logs
- `work` — generated/compiled runtime artifacts such as JSP-related generated classes
- `lib` — container-level libraries

### 5.4 Port configuration

Tomcat traditionally uses port 8080 for HTTP in a default development installation.

An endpoint may therefore look like:

```text
http://localhost:8080/books
```

Changing the port is a configuration task; it is not a Java-language feature.

### 5.5 WAR files

A web application is commonly packaged as a WAR:

```text
books.war
```

A WAR is an archive containing application classes, libraries, resources, and web metadata.

A common layout is:

```text
books.war
├── WEB-INF/
│   ├── classes/
│   ├── lib/
│   └── web.xml
├── index.html
└── images/
```

### 5.6 Context path

If the WAR is deployed as:

```text
books.war
```

the application is commonly reachable under:

```text
http://localhost:8080/books/
```

A deployment named `ROOT.war` is a common way to use the root context `/` in Tomcat.

### Interview Questions

**Q1. Is Tomcat a web server, servlet container, or application server?**

It is primarily a servlet container and web/application runtime for a subset of Jakarta EE web technologies. It can serve static content and HTTP traffic too, but it is not a full Jakarta EE platform implementation. citeturn274534search1

**Q2. Why is `JAVA_HOME` relevant?**

Tomcat runs on a Java runtime and needs an appropriate Java installation/configuration. Modern Tomcat lines also have explicit supported Java versions. citeturn274534search1

**Q3. What is inside `WEB-INF`?**

Application-private configuration and classes/libraries. Resources inside `WEB-INF` are not directly served as normal public web resources.

---

# Part 6 — MVC and JSP

## Chapter 6 — Why MVC Exists

### 6.1 The problem with HTML inside a servlet

This is possible:

```java
out.println("<h1>Books</h1>");
out.println("<p>Java</p>");
```

But it becomes painful very quickly.

Mixing Java control flow and large amounts of HTML leads to:

- poor readability,
- difficult UI changes,
- difficult testing,
- poor separation of responsibilities.

### 6.2 MVC mental model

```text
        HTTP request
             |
             v
        Controller
          Servlet
             |
             v
           Model
    Service / DAO / data
             |
             v
           View
        JSP / HTML
             |
             v
        HTTP response
```

A more accurate definition than the original notes:

- **Model** — application data and business/data-access logic.
- **View** — presentation.
- **Controller** — coordinates the request and selects the next step.

A singleton manager is **not** a requirement of MVC. In modern applications, dependency injection is usually preferable to hand-written singleton patterns.

### 6.3 Example model

```java
public record Book(
        long id,
        String title,
        String author,
        double rating
) {}
```

### 6.4 Example controller

```java
@WebServlet("/books")
public class BooksServlet extends HttpServlet {

    private final BookService service = new BookService();

    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        List<Book> books = service.findAll();
        request.setAttribute("books", books);

        request.getRequestDispatcher("/WEB-INF/books.jsp")
               .forward(request, response);
    }
}
```

### 6.5 JSP as a view

Jakarta Pages (the modern name for JSP technology) is a template technology that can mix HTML/XML with expression language and tags, and the page is processed into a servlet. Jakarta Pages 4.0 is part of Jakarta EE 11. citeturn618800search2turn618800search7

A simple JSP page:

```jsp
<h1>Books</h1>

<c:forEach var="book" items="${books}">
    <p>
        ${book.title} — ${book.author}
    </p>
</c:forEach>
```

The JSP should primarily be a view, not a second place to implement business logic.

### 6.6 `RequestDispatcher`

Passing data from the controller to a view often looks like:

```java
request.setAttribute("books", books);
request.getRequestDispatcher("/WEB-INF/books.jsp")
       .forward(request, response);
```

The attribute belongs to the request and is available to the forwarded JSP.

### 6.7 JSP scriptlets: know them for interviews, avoid them in new code

Legacy JSP might contain:

```jsp
<%
    for (Book book : books) {
%>
    <p><%= book.title() %></p>
<%
    }
%>
```

This works, but the code mixes Java and markup.

A cleaner modern JSP style uses EL and tag libraries:

```jsp
<c:forEach var="book" items="${books}">
    <p>${book.title}</p>
</c:forEach>
```

### Interview Questions

**Q1. Why is the servlet called the controller?**

Because it receives the request, coordinates application logic, prepares model data, and selects/forwards to the view.

**Q2. Should DAO code be written in JSP?**

No. JSP should present data; database access belongs in the model/data-access layer.

**Q3. Why use `/WEB-INF/books.jsp`?**

The application can forward to it internally, while the browser cannot directly request that path as an ordinary public resource.

### Tricky Question

Is MVC itself a framework?

**Answer:** No. MVC is a design pattern. Frameworks and platforms may provide mechanisms that implement or encourage MVC-style architecture.

---

# Part 7 — JSTL and Expression Language

## Chapter 7 — Cleaner JSP Pages

### 7.1 What JSTL is

JSTL is the standard tag library for Jakarta Server Pages. The modern Jakarta Standard Tag Library 3.0 uses the `jakarta.tags.*` tag library URIs. citeturn982766search0turn982766search3

This is a modernization point that should replace the old URI from the source notes.

Old style:

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

Modern Jakarta Tags 3.0:

```jsp
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
```

### 7.2 `c:forEach`

```jsp
<c:forEach var="book" items="${books}">
    <p>${book.title}</p>
</c:forEach>
```

It handles iteration without Java scriptlets.

### 7.3 `c:if`

```jsp
<c:if test="${book.rating >= 4.5}">
    <strong>Highly rated</strong>
</c:if>
```

### 7.4 `c:choose`

Useful when conditions are mutually exclusive:

```jsp
<c:choose>
    <c:when test="${book.rating >= 4.5}">
        Excellent
    </c:when>
    <c:when test="${book.rating >= 4.0}">
        Good
    </c:when>
    <c:otherwise>
        Average
    </c:otherwise>
</c:choose>
```

### 7.5 Expression Language (EL)

EL lets JSP access scoped data and bean-style properties.

```jsp
${book.title}
${book.author}
${book.rating}
```

EL is not simply “call the Java getter manually.” Property access follows EL resolution rules. Conceptually, `${book.title}` can resolve through the object's property accessor.

### 7.6 Important scope names

JSP/servlet applications commonly work with:

```text
pageScope
requestScope
sessionScope
applicationScope
```

A request attribute:

```java
request.setAttribute("books", books);
```

can be accessed as:

```jsp
${books}
```

or explicitly:

```jsp
${requestScope.books}
```

### 7.7 JSTL does not replace all Java

JSTL is for view-layer tasks. It is not a replacement for:

- domain logic,
- database transactions,
- security decisions,
- complex algorithms.

Keep the view thin.

### 7.8 Current compatibility note

Jakarta Tags 3.0 is part of the Jakarta EE 11 web profile. Jakarta Pages 4.0 is also part of the Jakarta EE 11 web profile. citeturn778678search2

### Interview Questions

**Q1. What is the difference between EL and JSTL?**

EL evaluates/reads expressions and values. JSTL provides tags for common view operations such as iteration and conditions.

**Q2. What is `${books}`?**

An EL expression. EL resolves the variable from JSP/EL scopes and other resolvers.

**Q3. What happened to the old JSTL URI?**

Jakarta Tags 3.0 renamed standard tag URIs to `jakarta.tags.*`. citeturn982766search3

### Tricky Question

What is wrong with this?

```jsp
<c:forEach var="book" items="${books}">
    ...
</c:forEach>

<c:if test="${book.rating > 4}">
```

`book` is the loop variable scoped to the iteration body. Once outside the loop, you cannot assume it represents the last item. A condition that should apply to each book belongs inside the loop.

---

# Part 8 — Building a Small Bookmarking Application

## Chapter 8 — Turn the Concepts into an End-to-End Flow

The original `thrill.io` material is more useful when treated as a case study than as a list of Eclipse clicks.

### 8.1 Functional requirements

Imagine a small application:

1. User logs in.
2. User sees saved books.
3. User browses books they have not saved.
4. User saves a book.
5. User logs out.

### 8.2 Suggested architecture

```text
Browser
   |
   v
Servlet Controller
   |
   v
Service / Manager
   |
   v
DAO / Repository
   |
   v
Database
```

And for presentation:

```text
Servlet
   |
   | request attributes
   v
JSP + EL + JSTL
```

### 8.3 Database access

A DAO method might conceptually be:

```java
public List<Book> findUnbookmarkedBooks(long userId) {
    // Execute parameterized SQL.
    // Map ResultSet rows to Book objects.
    // Return the list.
}
```

Do not build SQL with string concatenation from request parameters:

```java
// Bad
String sql =
    "SELECT * FROM users WHERE email = '" + email + "'";
```

Use prepared statements or a higher-level persistence framework.

### 8.4 Browse flow

```text
GET /bookmark/browse
        ↓
BookmarkServlet.doGet()
        ↓
BookmarkService.findUnbookmarkedBooks(userId)
        ↓
DAO -> database
        ↓
request.setAttribute("books", books)
        ↓
forward("/WEB-INF/browse.jsp")
```

### 8.5 Save flow: use POST

A better design than a GET “Save” link is a form:

```jsp
<form method="post" action="${pageContext.request.contextPath}/bookmark/save">
    <input type="hidden" name="bookId" value="${book.id}">
    <button type="submit">Save</button>
</form>
```

Then:

```java
@Override
protected void doPost(HttpServletRequest request,
                      HttpServletResponse response)
        throws IOException {

    long bookId = Long.parseLong(request.getParameter("bookId"));
    long userId = getAuthenticatedUserId(request);

    service.saveBookmark(userId, bookId);

    response.sendRedirect(
        request.getContextPath() + "/bookmark/mybooks"
    );
}
```

This naturally leads to the **Post/Redirect/Get** pattern.

### 8.6 Login and session management

A successful login can associate the authenticated user with an `HttpSession`:

```java
HttpSession session = request.getSession();
session.setAttribute("userId", userId);
```

Later:

```java
Long userId =
    (Long) request.getSession().getAttribute("userId");
```

Logout should invalidate the session:

```java
request.getSession().invalidate();
```

### 8.7 Password storage: hashing, not encryption

A major correction to the original notes:

> Passwords should not be “encrypted and compared.” They should be stored using a password-hashing scheme designed for password storage.

Conceptually:

```text
password
   ↓
password-hashing algorithm + salt
   ↓
stored password verifier
```

When the user logs in, the server verifies the entered password against the stored verifier.

### 8.8 Session security

For real applications, also think about:

- HTTPS
- secure cookies
- `HttpOnly`
- appropriate `SameSite` settings
- session fixation protection
- CSRF protection for state-changing browser requests
- authorization checks on every protected operation

### 8.9 PRG: Post/Redirect/Get

After a successful POST:

```text
POST /bookmark/save
        ↓
save bookmark
        ↓
302/303 redirect
        ↓
GET /bookmark/mybooks
```

The redirect prevents the browser's refresh from directly repeating the original POST form submission in the normal flow.

### Interview Questions

**Q1. Where should database SQL live?**

In a DAO/repository/data-access layer, not in JSP.

**Q2. Where should authorization be checked?**

On the server side, for every protected operation. Never trust a hidden form field or UI control as proof of permission.

**Q3. Why use PRG after a successful form submission?**

It gives the browser a fresh GET URL after the mutation and reduces accidental duplicate submissions on refresh.

**Q4. What is the difference between authentication and authorization?**

Authentication answers “Who are you?” Authorization answers “What are you allowed to do?”

### Tricky Question

A user sends:

```text
POST /bookmark/save
bookId=42
userId=999
```

Can the server trust `userId=999` because the browser submitted it?

No. The server must derive the authenticated identity from trusted security/session context and then authorize the requested operation.

---

# Part 9 — JAR, WAR and Java Packaging

## Chapter 9 — JAR Files Without the Confusion

### 9.1 What is a JAR?

JAR means **Java Archive**. It is an archive format commonly used to package:

- `.class` files,
- resources,
- metadata,
- manifests,
- library contents.

A JAR is based on the ZIP archive format.

### 9.2 Library JAR vs executable JAR

A library JAR may simply contain reusable classes.

An executable JAR typically has a manifest identifying the application's entry point, such as:

```text
Main-Class: com.example.App
```

Then:

```bash
java -jar app.jar
```

The entry class needs an appropriate `main` method for traditional executable-JAR launching.

### 9.3 JAR vs WAR

Think:

```text
JAR -> Java archive, commonly a library/application package
WAR -> Web application archive
```

A WAR may itself contain JAR files under:

```text
WEB-INF/lib/
```

### 9.4 Useful `jar` commands

Create:

```bash
jar cf app.jar -C out .
```

List contents:

```bash
jar tf app.jar
```

Extract:

```bash
jar xf app.jar
```

Create an executable JAR with a main class:

```bash
jar cfe app.jar com.example.App -C out .
```

### 9.5 Manifest

A manifest commonly lives at:

```text
META-INF/MANIFEST.MF
```

Example:

```text
Manifest-Version: 1.0
Main-Class: com.example.App
```

The manifest ends each header with a newline, and the file uses the JAR manifest format rules.

### 9.6 Classpath

A library JAR can be added to a classpath:

```bash
java -cp app.jar:lib/mysql.jar com.example.App
```

On Windows, the classpath separator is traditionally `;` rather than `:`.

Modern build tools normally manage this for you.

### 9.7 Maven and Gradle

In professional Java development, manually downloading JAR files is uncommon.

A build tool usually:

1. declares dependencies,
2. resolves compatible versions,
3. downloads them,
4. builds the application,
5. packages the application.

That is more scalable than manually copying library files into a project.

### 9.8 JAR and JPMS modules

Modern Java also has the Java Platform Module System (JPMS).

A modular JAR may contain:

```text
module-info.class
```

This lets the application declare module dependencies and exported packages.

Interviewers may ask:

```text
Classpath vs module path?
```

The short distinction is:

- **Classpath** — traditional Java dependency lookup.
- **Module path** — module-aware runtime/compile-time resolution under JPMS.

### 9.9 JAR signing

JARs can be digitally signed. This is different from saying “a JAR is secure.” Signing is about authenticity/integrity verification; it does not automatically make the application itself safe.

### Interview Questions

**Q1. Is a JAR the same thing as a ZIP?**

JAR uses the ZIP archive format as its container format, but adds Java-oriented conventions such as the manifest.

**Q2. What does `java -jar app.jar` need?**

The JAR must be launchable as an executable JAR, typically with a manifest `Main-Class` entry and an appropriate main method.

**Q3. Can a WAR contain JARs?**

Yes. Application libraries are commonly stored under `WEB-INF/lib`.

**Q4. Where does `Main-Class` live?**

In `META-INF/MANIFEST.MF` for a traditional executable JAR.

### Tricky Question

Does this always work?

```bash
java -jar library.jar
```

No. A normal library JAR may have no `Main-Class` and may intentionally not be executable.

---

# Part 10 — Records

## Chapter 10 — Records: Less Boilerplate, Same Java Type System

### 10.1 Why records exist

Before records, a simple data carrier often required:

- fields,
- constructor,
- getters/accessors,
- `equals()`;
- `hashCode()`;
- `toString()`.

Java introduced records as a concise way to model this kind of data. Records were previewed in Java 14 and finalized in **Java 16** as JEP 395. citeturn339883search3turn339883search4

### 10.2 Basic record

```java
public record Student(
        long id,
        String name,
        LocalDate startDate
) {}
```

Create one:

```java
Student s = new Student(
        101,
        "Alice",
        LocalDate.of(2026, 9, 1)
);
```

Access the components with methods:

```java
System.out.println(s.id());
System.out.println(s.name());
System.out.println(s.startDate());
```

**Important correction:** This is wrong:

```java
s.name;     // ❌ not how record components are accessed
```

Use:

```java
s.name();   // ✅
```

### 10.3 What the compiler provides

A record has a fixed set of record components and gets a canonical constructor, accessors, `equals()`, `hashCode()`, and `toString()` based on those components. A record class is implicitly final. citeturn339883search10

Conceptually:

```java
record Point(int x, int y) {}
```

behaves like a concise data carrier with equivalent component state and generated members.

### 10.4 Records are shallowly immutable

This phrase matters in interviews.

```java
record Team(List<String> members) {}
```

The record component reference cannot be reassigned through the record state, but the referenced `List` itself may still be mutable.

```java
List<String> members = new ArrayList<>();
Team team = new Team(members);

members.add("Alice");
```

The record did not create a deep immutable snapshot.

### 10.5 Compact constructor

Use a compact constructor when you want validation or normalization without manually assigning fields.

```java
public record Student(
        String name,
        int age
) {
    public Student {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}
```

The constructor body validates the incoming parameters; the compiler handles the canonical field assignments.

### 10.6 Explicit canonical constructor

You can also write the full constructor:

```java
public record Student(String name, int age) {
    public Student(String name, int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
        this.name = name;
        this.age = age;
    }
}
```

Do not write both a full canonical constructor and a compact canonical constructor for the same record.

### 10.7 Overloaded constructors

Records may define additional constructors as long as they ultimately initialize the canonical record state.

```java
public record Student(String name, int age) {

    public Student {
        if (age < 0) {
            throw new IllegalArgumentException();
        }
    }

    public Student(String name) {
        this(name, 0);
    }
}
```

### 10.8 Methods inside records

Records are real Java classes. They can define methods:

```java
public record Rectangle(double width, double height) {
    public double area() {
        return width * height;
    }
}
```

### 10.9 Implementing interfaces

```java
public record User(long id, String name)
        implements Comparable<User> {

    @Override
    public int compareTo(User other) {
        return Long.compare(id, other.id);
    }
}
```

### 10.10 Records cannot extend another class

A record is already a special kind of class with `java.lang.Record` as its direct superclass.

You cannot write:

```java
record Student(String name) extends Person {}
```

A record can implement interfaces, but it cannot extend a different class. citeturn339883search3

### 10.11 Static members and nested types

Records can declare static members and nested types. Java 16 also relaxed the rules around static declarations in inner classes, including nested record members. citeturn339883search4

### Interview Questions

**Q1. When should you use a record?**

When the type is primarily a transparent data carrier with a fixed set of state components and value-based equality semantics.

**Q2. Are records immutable?**

They are **shallowly immutable**. Their component fields are final, but referenced mutable objects can still change.

**Q3. Can a record have methods?**

Yes.

**Q4. Can a record implement an interface?**

Yes.

**Q5. Can a record extend a class?**

No, other than its implicit superclass `java.lang.Record`.

**Q6. Are record accessor methods named `getName()`?**

No. They are named after the component, such as `name()`.

### Tricky Question

```java
record User(List<String> roles) {}
```

Is `User` deeply immutable?

No. The record's component reference is final, but the referenced list may still be modified.

---

# Part 11 — Records + Modern Pattern Matching

## Chapter 11 — Record Patterns

### 11.1 Why record patterns matter

Records became final in Java 16. Later, Java 21 finalized **record patterns**, allowing the program to test and destructure records concisely. citeturn979823search7turn979823search4

Example:

```java
record Point(double x, double y) {}

Object value = new Point(3, 4);

if (value instanceof Point(double x, double y)) {
    System.out.println(x + ", " + y);
}
```

This combines type testing and component extraction.

### 11.2 Compare with the older style

Without a record pattern:

```java
if (value instanceof Point p) {
    double x = p.x();
    double y = p.y();
    System.out.println(x + ", " + y);
}
```

With a record pattern:

```java
if (value instanceof Point(double x, double y)) {
    System.out.println(x + ", " + y);
}
```

### 11.3 Pattern matching with `switch`

Java 21 also finalized pattern matching for `switch`. citeturn979823search7

```java
static String describe(Object value) {
    return switch (value) {
        case Integer i -> "integer: " + i;
        case String s  -> "string: " + s;
        case null      -> "null";
        default        -> "other";
    };
}
```

### 11.4 Records + switch = powerful data modeling

```java
record Circle(double radius) {}
record Rectangle(double width, double height) {}

static double area(Object shape) {
    return switch (shape) {
        case Circle(double r) -> Math.PI * r * r;
        case Rectangle(double w, double h) -> w * h;
        default -> throw new IllegalArgumentException("Unknown shape");
    };
}
```

This style is especially useful for sealed hierarchies and small data-oriented domain models.

### Interview Questions

**Q1. In which release did record patterns become permanent?**

Java 21. citeturn979823search7

**Q2. What does a record pattern do?**

It tests whether a value matches a record type and extracts its record components.

**Q3. Are record patterns the same as constructors?**

No. Constructors create records; record patterns deconstruct/match existing records.

### Tricky Question

```java
Object value = null;
if (value instanceof Point(double x, double y)) {
    // ...
}
```

Does the pattern match?

No. `null` does not match a record pattern. citeturn979823search4

---

# Part 12 — Modern Java Timeline for These Notes

## Chapter 12 — What Changed and When

This section is deliberately limited to changes directly relevant to the material in this document.

| Release | Relevant change |
|---|---|
| **Java 8** | `java.time` introduced as the modern date/time API |
| **Java 14** | Records first appeared as a preview feature |
| **Java 16** | Records became permanent; pattern matching for `instanceof` also became permanent |
| **Java 21** | Record patterns and pattern matching for `switch` became permanent |
| **Jakarta EE 9 (2020)** | `javax.*` → `jakarta.*` namespace transition |
| **Jakarta EE 10 (2022)** | Major platform updates; Java SE 8 support removed |
| **Jakarta EE 11 (June 26, 2025)** | Java 17+ baseline, Jakarta Data, broader record integration, Servlet 6.1, Pages 4.0, Tags 3.0 |
| **JDK 25 (2025)** | Current LTS release as of September 2026 |
| **JDK 27 (September 15, 2026)** | Latest Java SE feature release as of September 19, 2026 |

JDK 27 was released on September 15, 2026 and is the latest Java SE release; JDK 25 is the current LTS release. citeturn339883search0turn339883search6

Jakarta EE 11 is the current released Jakarta EE platform, while Jakarta EE 12 is under development as of September 2026. citeturn778678search1

---

# Part 13 — Interview Cheat Sheet

## 13.1 Date/Time rapid fire

**LocalDate vs Instant?**

Date on a human calendar vs exact machine timeline point.

**Period vs Duration?**

Date-based amount vs time-based amount.

**Why `ZonedDateTime`?**

To model date/time together with a named time zone.

**Why are `java.time` classes easier to share between threads?**

Core types are immutable and thread-safe. citeturn618800search0

**Does `plusDays()` mutate the object?**

No.

---

## 13.2 HTTP rapid fire

**GET vs POST?**

GET is primarily safe/read-oriented; POST commonly submits or changes state.

**401 vs 403?**

Authentication problem vs authorization refusal.

**Where do query parameters appear?**

In the URL after `?`.

**Is POST automatically secure?**

No. HTTPS protects data in transit.

---

## 13.3 Servlet rapid fire

**Who creates servlet instances?**

The servlet container.

**Who invokes `doGet()`?**

The container, through `HttpServlet` dispatching.

**Why avoid servlet instance fields for request data?**

Concurrent requests can share the same servlet instance.

**forward vs redirect?**

Server-side internal transfer vs new client request.

**Why `loadOnStartup`?**

To request eager servlet initialization during application startup.

---

## 13.4 Jakarta EE rapid fire

**Java EE's modern name?**

Jakarta EE.

**When did the `javax.*` → `jakarta.*` transition happen?**

Jakarta EE 9, released December 8, 2020. citeturn274534search7

**What is the current released platform?**

Jakarta EE 11; Jakarta EE 12 is under development. citeturn778678search1

**Current Servlet generation for EE 11?**

Servlet 6.1. citeturn339883search8

**Does Tomcat equal full Jakarta EE?**

No. It implements a subset, primarily web-container technologies. citeturn274534search1

---

## 13.5 JSP/JSTL rapid fire

**What is JSP/Jakarta Pages?**

A server-side page/template technology processed into a servlet.

**Should business logic live in JSP?**

No.

**What should replace most Java scriptlets in a view?**

EL + JSTL/tag libraries and, where appropriate, other view technology.

**Modern JSTL core URI?**

`jakarta.tags.core`. citeturn982766search0

**Current Jakarta Tags version in Jakarta EE 11?**

3.0. citeturn778678search2

---

## 13.6 Records rapid fire

**When were records finalized?**

Java 16. citeturn339883search3

**Are records immutable?**

Shallowly immutable.

**Accessor for `name`?**

`name()`.

**Can records have methods?**

Yes.

**Can records implement interfaces?**

Yes.

**Can records extend a class?**

No, other than their implicit superclass `java.lang.Record`. citeturn339883search3

**When were record patterns finalized?**

Java 21. citeturn979823search7

---

# Part 14 — Tricky Interview Questions

## Question 1

```java
LocalDate date = LocalDate.of(2026, 9, 19);
date.plusDays(1);
System.out.println(date);
```

**Answer:** `2026-09-19` because the result of `plusDays()` was ignored.

---

## Question 2

```java
LocalDateTime meeting = LocalDateTime.of(2026, 9, 19, 15, 0);
```

Can this alone tell you the exact global instant of the meeting?

**Answer:** No. There is no time zone or offset.

---

## Question 3

Why can a singleton `BooksManager` still be a poor design even though the application works?

**Answer:** MVC does not require a singleton. Global mutable state increases coupling and can make testing and concurrency harder. In modern Jakarta EE, container-managed dependency injection is usually a better architectural direction.

---

## Question 4

A servlet has:

```java
private String userName;
```

and does:

```java
userName = request.getParameter("userName");
```

What is the danger?

**Answer:** The field is shared by concurrent requests. One request can overwrite the value while another request is still using it.

---

## Question 5

Why does this not prove security?

```html
<input type="hidden" name="userId" value="42">
```

**Answer:** The client controls form fields. The server must derive identity from trusted authentication/session context and enforce authorization.

---

## Question 6

Why is this suspicious?

```java
response.sendRedirect("/books.jsp");
```

**Answer:** Redirect creates a new browser request. An internal JSP view under `WEB-INF` is often better served using `RequestDispatcher.forward()` so the JSP remains directly inaccessible.

---

## Question 7

Why is this wrong in modern Jakarta code?

```java
import javax.servlet.http.HttpServlet;
```

**Answer:** For Jakarta EE 9+ servlet APIs, the namespace is `jakarta.servlet.*`. The namespace migration was a breaking compatibility change. citeturn274534search4

---

## Question 8

Why is this JSP variable risky?

```jsp
<%= request.getParameter("name") %>
```

**Answer:** It directly writes user-controlled data into the response. In real applications, output encoding and contextual escaping are important to avoid injection problems. Prefer proper view technologies and escaping facilities.

---

## Question 9

What is wrong with this record usage?

```java
record User(List<String> roles) {}

User u = new User(new ArrayList<>());
```

Calling `u.roles().add("ADMIN")` may still succeed if the supplied list is mutable. The record is not deeply immutable.

---

## Question 10

Why is this not an executable JAR automatically?

```bash
java -jar library.jar
```

Because being a JAR does not mean being a runnable application. A launchable JAR normally needs a `Main-Class` manifest entry and a suitable main method.

---

# Part 15 — Mini Coding Exercises

## Exercise 1 — Date range

Write a method that returns the number of whole days between two `LocalDate` values.

**Expected idea:**

```java
long daysBetween(LocalDate start, LocalDate end) {
    return ChronoUnit.DAYS.between(start, end);
}
```

---

## Exercise 2 — Convert time zones

Given:

```java
ZonedDateTime source =
    ZonedDateTime.now(ZoneId.of("Europe/Brussels"));
```

Convert the same instant to Tokyo.

**Expected idea:**

```java
source.withZoneSameInstant(ZoneId.of("Asia/Tokyo"));
```

---

## Exercise 3 — Simple servlet endpoint

Create:

```text
GET /hello
```

and return:

```text
Hello from Jakarta Servlet
```

Use `@WebServlet`, `doGet()`, and `response.getWriter()`.

---

## Exercise 4 — MVC forwarding

Write a servlet that:

1. creates a list of books,
2. stores it as a request attribute,
3. forwards to `/WEB-INF/books.jsp`.

---

## Exercise 5 — JSTL

Given `books`, write JSP code that:

- loops through the list,
- prints title and author,
- prints “Top Pick” when rating is at least 4.5.

Expected pattern:

```jsp
<c:forEach var="book" items="${books}">
    <p>${book.title} — ${book.author}</p>

    <c:if test="${book.rating >= 4.5}">
        <strong>Top Pick</strong>
    </c:if>
</c:forEach>
```

---

## Exercise 6 — Record validation

Create:

```java
record Course(String name, int durationWeeks) {}
```

Then add a compact constructor that rejects non-positive durations.

---

## Exercise 7 — Record pattern

Given:

```java
record Point(int x, int y) {}
```

Use a record pattern with `instanceof` to print the coordinates only when the object is a `Point`.

---

# Final Mental Model

When answering interview questions, try to connect the layers instead of memorizing isolated definitions.

```text
Date/Time
   ↓
Model correct business values

HTTP
   ↓
Understand request/response semantics

Servlet container
   ↓
Map HTTP requests into Java code

Controller / Service / DAO
   ↓
Separate responsibilities

JSP / JSTL / EL
   ↓
Render the result

Session / Security
   ↓
Identify and authorize the user

JAR / WAR
   ↓
Package and deploy the application

Records
   ↓
Represent data cleanly

Pattern matching
   ↓
Process structured data concisely
```

The strongest interview answers usually explain **why** a design exists, not just **what** a class or annotation does.

For example:

> “I use `LocalDate` because the business value is a calendar date, not a global instant.”

is stronger than:

> “`LocalDate` stores a date.”

Similarly:

> “I use POST for creating a bookmark because it changes server state; after success I redirect so the browser lands on a GET URL.”

is stronger than:

> “POST calls `doPost()`.”

---

# Verified Modernization References

- Oracle Java `java.time` API: https://docs.oracle.com/javase/8/docs/api/java/time/package-summary.html
- Jakarta EE 9 namespace migration: https://jakarta.ee/specifications/platform/9/jakarta-platform-spec-9
- Jakarta EE releases: https://jakarta.ee/release/
- Jakarta Servlet 6.1: https://jakarta.ee/specifications/servlet/6.1/
- Jakarta Pages: https://jakarta.ee/specifications/pages/
- Jakarta Tags 3.0 core URI: https://jakarta.ee/specifications/tags/3.0/tagdocs/c/tld-summary
- Apache Tomcat supported versions: https://tomcat.apache.org/whichversion
- Java record classes: https://docs.oracle.com/en/java/javase/16/language/records.html
- Java 21 record patterns: https://docs.oracle.com/en/java/javase/21/language/record-patterns.html
- Oracle JDK downloads/current releases: https://www.oracle.com/java/technologies/downloads/

