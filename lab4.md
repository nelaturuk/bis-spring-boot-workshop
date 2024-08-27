### **Lab 4: Integrating a Database with Spring Boot and Connecting with REST Endpoints and Front-End**

**Objective:**  
In this lab, participants will integrate a database into their Spring Boot application using Spring Data JPA. They will create a repository for storing data, update their REST endpoints to interact with the database, and connect the database operations with the Thymeleaf front-end.

---

**Steps:**

1. **Add Database Dependency:**
   - Open `pom.xml` (or `build.gradle` if using Gradle).
   - Add the following dependencies under the `<dependencies>` section for Maven:
     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-data-jpa</artifactId>
     </dependency>
     <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>javax.persistence-api</artifactId>
            <version>2.2</version>
     </dependency>
     <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>2.2.220</version> <!-- Use the latest stable version -->
            <scope>runtime</scope>
        </dependency>
     ```
   - These dependencies include Spring Data JPA for database interaction and H2 as an in-memory database for simplicity.

2. **Configure H2 Database:**
   - Open `src/main/resources/application.properties`.
   - Add the following configuration to set up the H2 database:
     ```properties
     spring.datasource.url=jdbc:h2:mem:testdb
     spring.datasource.driverClassName=org.h2.Driver
     spring.datasource.username=sa
     spring.datasource.password=password
     spring.h2.console.enabled=true
     spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
     spring.jpa.hibernate.ddl-auto=update
     ```
   - This configuration sets up an in-memory H2 database and enables the H2 console for viewing the database content.

3. **Create a JPA Entity:**
   - In the `src/main/java/com/example/demo` package, create a new package named `model`.
   - Inside the `model` package, create a Java class named `Greeting`.
   - Annotate the class to define it as a JPA entity:
     ```java
     package com.example.demo.model;

     import jakarta.persistence.Entity;
     import jakarta.persistence.GeneratedValue;
     import jakarta.persistence.GenerationType;
     import jakarta.persistence.Id;

     @Entity
     public class Greeting {
         @Id
         @GeneratedValue(strategy = GenerationType.AUTO)
         private Long id;
         private String content;

         public Greeting() {}

         public Greeting(String content) {
             this.content = content;
         }

         public Long getId() {
             return id;
         }

         public void setId(Long id) {
             this.id = id;
         }

         public String getContent() {
             return content;
         }

         public void setContent(String content) {
             this.content = content;
         }
     }
     ```
   - This class represents the `Greeting` entity that will be stored in the database.

4. **Create a JPA Repository:**
   - In the `src/main/java/com/example/demo` package, create a new package named `repository`.
   - Inside the `repository` package, create a new Java interface named `GreetingRepository`:
     ```java
     package com.example.demo.repository;

     import com.example.demo.model.Greeting;
     import org.springframework.data.jpa.repository.JpaRepository;

     public interface GreetingRepository extends JpaRepository<Greeting, Long> {
     }
     ```
   - This interface provides CRUD operations for the `Greeting` entity.

5. **Update REST Controller to Use Database:**
   - In the `GreetingController`, inject the `GreetingRepository` and update the endpoints to use the database:
     ```java
     package com.example.demo.controller;

     import com.example.demo.model.Greeting;
     import com.example.demo.repository.GreetingRepository;
     import org.springframework.beans.factory.annotation.Autowired;
     import org.springframework.web.bind.annotation.GetMapping;
     import org.springframework.web.bind.annotation.PostMapping;
     import org.springframework.web.bind.annotation.RequestParam;
     import org.springframework.web.bind.annotation.RestController;

     import java.util.List;

     @RestController
     public class GreetingController {

         @Autowired
         private GreetingRepository greetingRepository;

         @GetMapping("/api/greeting")
         public String greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
             Greeting greeting = new Greeting("Hello, " + name + "!");
             greetingRepository.save(greeting);
             return greeting.getContent();
         }

         @GetMapping("/api/greetings")
         public List<Greeting> greetings() {
             return greetingRepository.findAll();
         }

         @PostMapping("/api/greeting")
         public Greeting createGreeting(@RequestParam String content) {
             Greeting greeting = new Greeting(content);
             return greetingRepository.save(greeting);
         }
     }
     ```
   - The `greeting` endpoint now stores each greeting in the database, and the `greetings` endpoint retrieves all greetings from the database.

6. **Update Thymeleaf Template to Display Data from Database:**
   - Open the `home.html` file located in `src/main/resources/templates`.
   - Modify the template to display the greetings stored in the database:
     ```html
     <!DOCTYPE html>
     <html xmlns:th="http://www.thymeleaf.org">
     <head>
         <title>Spring Boot Thymeleaf Example</title>
     </head>
     <body>
         <h1 th:text="${message}"></h1>
         
         <form id="greetingForm">
             <label for="name">Enter your name:</label>
             <input type="text" id="name" name="name">
             <button type="button" onclick="fetchGreeting()">Get Greeting</button>
         </form>

         <p id="greetingResult"></p>

         <h2>Stored Greetings:</h2>
         <ul id="greetingsList"></ul>

         <script>
             function fetchGreeting() {
                 const name = document.getElementById('name').value;
                 fetch('/api/greeting?name=' + name)
                     .then(response => response.text())
                     .then(data => {
                         document.getElementById('greetingResult').textContent = data;
                         fetchAllGreetings(); // Update the list after adding a new greeting
                     });
             }

             function fetchAllGreetings() {
                 fetch('/api/greetings')
                     .then(response => response.json())
                     .then(data => {
                         const list = document.getElementById('greetingsList');
                         list.innerHTML = '';
                         data.forEach(greeting => {
                             const listItem = document.createElement('li');
                             listItem.textContent = greeting.content;
                             list.appendChild(listItem);
                         });
                     });
             }

             // Load the greetings when the page loads
             window.onload = fetchAllGreetings;
         </script>
     </body>
     </html>
     ```
   - This code will display all greetings stored in the database in a list on the page.

7. **Test the Integration:**
   - Ensure your Spring Boot application is running.
   - Navigate to `http://localhost:8080`.
   - Enter a name and click "Get Greeting". The greeting will be stored in the database and displayed in the list below the form.
   - Reload the page to verify that the stored greetings persist across sessions.

8. **View the Database Using the H2 Console:**
   - Access the H2 console by navigating to `http://localhost:8080/h2-console` in your browser.
   - Use the default settings (JDBC URL: `jdbc:h2:mem:testdb`, User Name: `sa`, Password: `password`) to log in.
   - You can view the contents of the `Greeting` table by executing a simple SQL query like `SELECT * FROM GREETING`.

**Completion:**
- By completing this lab, you have successfully integrated a database into your Spring Boot application. You’ve learned how to create a JPA entity, repository, and use these to store and retrieve data via REST endpoints, and display the data on your Thymeleaf front-end.

---

**Troubleshooting Tips:**
- **Database Not Persisting Data:** Ensure that `spring.jpa.hibernate.ddl-auto=update` is set in `application.properties` to auto-create and update tables based on your entity classes.
- **H2 Console Issues:** Double-check the URL and login credentials for the H2 console if you're unable to access it.
- **REST Endpoint Issues:** Ensure that your endpoints are correctly mapped and that the repository is being injected properly.

