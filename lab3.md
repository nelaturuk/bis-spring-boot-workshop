### **Lab 3: Creating REST Endpoints and Connecting with Thymeleaf Front End**

**Objective:**  
In this lab, participants will create RESTful endpoints using Spring Boot and connect these endpoints to the Thymeleaf front-end pages created in the previous lab. This will allow dynamic data to be fetched from the server and displayed on the web pages.

---

**Steps:**

1. **Create a REST Controller:**
   - In the `src/main/java/com/example/demo/controller` package, create a new Java class named `GreetingController`.
   - Implement the following code to define a simple RESTful endpoint that returns a greeting message:
     ```java
     package com.example.demo.controller;

     import org.springframework.web.bind.annotation.GetMapping;
     import org.springframework.web.bind.annotation.RequestParam;
     import org.springframework.web.bind.annotation.RestController;

     @RestController
     public class GreetingController {

         @GetMapping("/api/greeting")
         public String greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
             return "Hello, " + name + "!";
         }
     }
     ```
   - This controller listens for GET requests at `/api/greeting` and returns a greeting message that can be customized with the `name` query parameter.

2. **Update Thymeleaf Template to Call the REST API:**
   - Open the `home.html` file located in `src/main/resources/templates`.
   - Modify the template to include a form that allows users to enter their name, and a button to trigger the REST API call:
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

         <script>
             function fetchGreeting() {
                 const name = document.getElementById('name').value;
                 fetch('/api/greeting?name=' + name)
                     .then(response => response.text())
                     .then(data => {
                         document.getElementById('greetingResult').textContent = data;
                     });
             }
         </script>
     </body>
     </html>
     ```
   - This code adds a simple form with an input field for the name and a button to fetch the greeting. The `fetchGreeting` JavaScript function makes an AJAX request to the `/api/greeting` endpoint and displays the result on the page.

3. **Test the Integration:**
   - Ensure your Spring Boot application is running.
   - Navigate to `http://localhost:8080` in your web browser.
   - Enter a name in the input field and click the "Get Greeting" button.
   - The page should display a personalized greeting below the form (e.g., "Hello, John!").

4. **Create Another REST Endpoint:**
   - To demonstrate more REST functionality, add another method to the `GreetingController` that returns a list of greetings:
     ```java
     @GetMapping("/api/greetings")
     public List<String> greetings() {
         return List.of("Hello, World!", "Hello, Spring Boot!", "Hello, Thymeleaf!");
     }
     ```
   - You can modify `home.html` to include a button that fetches and displays all greetings:
     ```html
     <button type="button" onclick="fetchAllGreetings()">Get All Greetings</button>

     <ul id="greetingsList"></ul>

     <script>
         function fetchAllGreetings() {
             fetch('/api/greetings')
                 .then(response => response.json())
                 .then(data => {
                     const list = document.getElementById('greetingsList');
                     list.innerHTML = '';
                     data.forEach(greeting => {
                         const listItem = document.createElement('li');
                         listItem.textContent = greeting;
                         list.appendChild(listItem);
                     });
                 });
         }
     </script>
     ```
   - This will display a list of predefined greetings on the page.

5. **Error Handling in REST Endpoints:**
   - To handle errors in your REST endpoints, you can modify the `GreetingController` to throw an exception for specific conditions:
     ```java
     @GetMapping("/api/greeting")
     public String greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
         if (name.isEmpty()) {
             throw new IllegalArgumentException("Name cannot be empty");
         }
         return "Hello, " + name + "!";
     }
     ```
   - Add an `@ExceptionHandler` method to handle the exception and return a proper response:
     ```java
     @ExceptionHandler(IllegalArgumentException.class)
     public ResponseEntity<String> handleIllegalArgumentException(IllegalArgumentException ex) {
         return ResponseEntity.badRequest().body(ex.getMessage());
     }
     ```
   - This ensures that invalid requests are handled gracefully.

**Completion:**
- By completing this lab, you have successfully created RESTful endpoints in your Spring Boot application and integrated them with the Thymeleaf front-end. This allows you to build dynamic, interactive web pages that communicate with the server.

---

**Troubleshooting Tips:**
- **CORS Issues:** If you encounter Cross-Origin Resource Sharing (CORS) issues when making the API call, you may need to add CORS configuration to your Spring Boot application.
- **REST Endpoint Not Working:** Ensure that the URL path is correct and that the server is running without errors.
- **JavaScript Errors:** Check the browser's developer console for any JavaScript errors that might prevent the API call from working.

These instructions should help participants create REST endpoints and connect them with their Thymeleaf front-end, providing a full-stack experience with Spring Boot.