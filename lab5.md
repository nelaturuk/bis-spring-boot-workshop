### **Lab 5: Testing the Spring Boot Application**

**Objective:**  
In this lab, participants will add basic unit tests to their Spring Boot application. They will test the REST endpoints and database interactions focusing on simple, straightforward tests.

---

**Steps:**

1. **Add Test Dependencies:**
   - Ensure you have the necessary dependencies for testing in your `pom.xml` or `build.gradle`.

   For Maven:
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-test</artifactId>
       <scope>test</scope>
   </dependency>
   ```

   For Gradle:
   ```gradle
   testImplementation 'org.springframework.boot:spring-boot-starter-test'
   ```

2. **Create a Test Class for `GreetingController`:**
   - In the `src/test/java` directory, create a new package, `com.example.demo.controller`.
   - Inside this package, create a Java class named `GreetingControllerTests`.

   ```java
    package com.example.demo;

    import com.example.demo.controller.GreetingController;
    import com.example.demo.model.Greeting;
    import com.example.demo.repository.GreetingRepository;
    import org.junit.jupiter.api.BeforeEach;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
    import org.springframework.boot.test.mock.mockito.MockBean;
    import org.springframework.test.web.servlet.MockMvc;
    import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
    import org.springframework.test.web.servlet.result.MockMvcResultMatchers;

    @WebMvcTest(GreetingController.class)
    public class GreetingControllerTests {

        @Autowired
        private MockMvc mockMvc;

        @MockBean
        private GreetingRepository greetingRepository;

        @BeforeEach
        public void setup() {
            // You might need to set up your mock behavior here if using @MockBean
        }

        @Test
        public void testGreetingEndpoint() throws Exception {
            mockMvc.perform(MockMvcRequestBuilders.get("/api/greeting?name=John"))
                    .andExpect(MockMvcResultMatchers.status().isOk())
                    .andExpect(MockMvcResultMatchers.content().string("Hello, John!"));
        }
    }

   ```

   **Explanation:**
   - `@WebMvcTest` focuses only on web layer testing, providing `MockMvc` to simulate HTTP requests.
   - `MockMvc` is used to perform HTTP requests and assert responses.
   - The `setup()` method ensures the database is clean before each test.
   - `@MockBean` is used to mock the GreetingRepository so that the controller can be tested in isolation.

3. **Create a Test Class for `GreetingIntegrationTests`:**
- In the `src/test/java` directory, create a Java class named `GreetingIntegrationTests`.

```java
package com.example.demo;

import com.example.demo.model.Greeting;
import com.example.demo.repository.GreetingRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;

@SpringBootTest
public class GreetingIntegrationTests {

    @Autowired
    private WebApplicationContext webApplicationContext;

    @Autowired
    private GreetingRepository greetingRepository;

    private MockMvc mockMvc;

    @BeforeEach
    public void setup() {
        mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
        greetingRepository.deleteAll();
    }

    @Test
    public void testGreetingEndpoint() throws Exception {
        mockMvc.perform(get("/api/greeting?name=John"))
                .andExpect(status().isOk())
                .andExpect(content().string("Hello, John!"));
    }
}


```

**Explanation:**
- `@SpringBootTest` is used to create a Spring application context for the test.
- `@WebMvcTest` focuses only on web layer testing, providing `MockMvc` to simulate HTTP requests.
- `MockMvc` is used to perform HTTP requests and assert responses.
- The `setup()` method ensures the database is clean before each test.

4. **Create a Test Class for `Greeting` Entity:**
   - In the `src/test/java` directory, create a new package, `com.example.demo.model`.
   - Inside this package, create a Java class named `GreetingTests`.

   ```java
   package com.example.demo.model;

   import org.junit.jupiter.api.Test;
   import static org.junit.jupiter.api.Assertions.assertEquals;

   public class GreetingTests {

       @Test
       public void testGreetingConstructor() {
           Greeting greeting = new Greeting("Hello, Test!");
           assertEquals("Hello, Test!", greeting.getContent());
       }

       @Test
       public void testGreetingSettersAndGetters() {
           Greeting greeting = new Greeting();
           greeting.setContent("Test Content");
           assertEquals("Test Content", greeting.getContent());
       }
   }
   ```

   **Explanation:**
   - These tests ensure that the `Greeting` entity's constructor and setters/getters work correctly.

4. **Run Tests:**
   - Run the tests to ensure everything is working correctly.
     - **In IntelliJ IDEA:** Right-click on the test class -> `Run 'GreetingControllerTests'` or `Run 'GreetingIntegrationTests'` or `Run 'GreetingTests'`.
     - 
     - **In Eclipse:** Right-click on the test class -> `Run As` -> `JUnit Test`.

5. **Review Test Results:**
   - Check the results of your tests to ensure all tests pass. If any tests fail, review the code and fix any issues.

**Completion:**
- By completing this lab, you have learned how to add and run basic unit tests for your Spring Boot application, including testing REST endpoints and entity behaviors.

---

**Troubleshooting Tips:**
- **Test Failures:** Check the test output for detailed error messages. Make sure the application context and configurations are correct.
- **Dependency Issues:** Ensure you have the correct dependencies for testing in your `pom.xml` or `build.gradle`.
- **IDE Configuration:** Make sure your IDE is correctly configured to run JUnit tests.
