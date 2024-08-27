**Lab 2: Setting Up a Simple MVC Front End Using Thymeleaf**

**Objective:**  
In this lab, participants will learn how to create a simple Model-View-Controller (MVC) front end using Spring Boot and Thymeleaf. Thymeleaf is a Java template engine that integrates well with Spring, allowing you to create dynamic web pages.

---

**Steps:**

1. **Add Thymeleaf Dependency:**
   - Open `pom.xml` (or `build.gradle` if you're using Gradle).
   - Add the following dependency under the `<dependencies>` section for Maven:
     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-thymeleaf</artifactId>
     </dependency>
     ```
   - If using Gradle, add the following:
     ```gradle
     implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
     ```
   - Save the file and let the IDE refresh the dependencies.

2. **Create a Controller:**
   - In the `src/main/java/com/example/demo` directory, right-click and select new `package`. 
   - Create a new package named `controller`.
   - Inside the `controller` package, create a new Java class called `HomeController`.
   - Define a simple controller method to handle requests to the home page:
     ```java
     package com.example.demo.controller;

     import org.springframework.stereotype.Controller;
     import org.springframework.ui.Model;
     import org.springframework.web.bind.annotation.GetMapping;

     @Controller
     public class HomeController {

         @GetMapping("/")
         public String home(Model model) {
             model.addAttribute("message", "Welcome to Spring Boot with Thymeleaf!");
             return "home";
         }
     }
     ```
   - This controller listens for GET requests at the root URL (`/`) and passes a message to the view named `home`.

3. **Create Thymeleaf Template:**
   - In `src/main/resources/templates`, create a new HTML file named `home.html`.
   - Add the following basic Thymeleaf template:
     ```html
     <!DOCTYPE html>
     <html xmlns:th="http://www.thymeleaf.org">
     <head>
         <title>Spring Boot Thymeleaf Example</title>
     </head>
     <body>
         <h1 th:text="${message}"></h1>
     </body>
     </html>
     ```
   - This template uses the `th:text` attribute to display the `message` attribute passed from the controller.

4. **Run the Application:**
   - Ensure your Spring Boot application is running.
   - Navigate to `http://localhost:8080` in your web browser.
   - You should see the message "Welcome to Spring Boot with Thymeleaf!" displayed on the page.

5. **Customize the View:**
   - Experiment with adding more elements to the `home.html` file, such as an additional message or an image:
     ```html
     <p th:text="'Today is: ' + ${#dates.format(new java.util.Date(), 'dd-MM-yyyy')}"></p>
     <img src="https://example.com/sample-image.jpg" alt="Sample Image">
     ```
   - Use Thymeleaf's built-in expressions to format dates, handle conditions, or iterate over collections.

6. **Handling Multiple Views:**
   - Add another method to the `HomeController` to demonstrate navigation to another page:
     ```java
     @GetMapping("/about")
     public String about(Model model) {
         model.addAttribute("message", "This is the about page.");
         return "about";
     }
     ```
   - Create a corresponding `about.html` template in the `templates` directory:
     ```html
     <!DOCTYPE html>
     <html xmlns:th="http://www.thymeleaf.org">
     <head>
         <title>About Page</title>
     </head>
     <body>
         <h1 th:text="${message}"></h1>
         <a href="/" th:text="'Back to Home'"></a>
     </body>
     </html>
     ```
   - Visit `http://localhost:8080/about` to see the new page and navigate back to the home page using the link.

**Completion:**
- By completing this lab, you have successfully created a simple MVC front end using Thymeleaf. You’ve learned how to set up a Thymeleaf template, pass data from a controller, and render dynamic content on the web page.

---

**Troubleshooting Tips:**
- **Template Not Found:** Ensure that the `home.html` file is located in the `src/main/resources/templates` directory and named correctly.
- **Thymeleaf Syntax Errors:** Double-check the Thymeleaf syntax, especially `th:text` and other expressions.
- **Port Conflicts:** If your application doesn't start, make sure the port specified in `application.properties` is not in use by another application.

