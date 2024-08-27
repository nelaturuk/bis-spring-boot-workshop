### **Lab 6: Adding Actuator for Displaying Metrics**

**Objective:**  
In this lab, participants will add Spring Boot Actuator to their application to expose and display application metrics. They will learn how to configure Actuator endpoints and view metrics data.

---

**Steps:**

1. **Add Actuator Dependency:**
   - Add the Spring Boot Actuator dependency to your `pom.xml` or `build.gradle`.

   For Maven:
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   <dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-core</artifactId>
   </dependency>

   ```

   For Gradle:
   ```gradle
   implementation 'org.springframework.boot:spring-boot-starter-actuator'
   ```

2. **Configure Actuator Endpoints:**
   - Open `application.properties` or `application.yml` and configure which Actuator endpoints to expose.

   **For `application.properties`:**
   ```properties
   management.endpoints.web.exposure.include=health,metrics
   management.endpoint.health.show-details=always
   management.endpoints.web.base-path=/actuator
   ```


   **Explanation:**
   - `management.endpoints.web.exposure.include` specifies which endpoints to expose (e.g., `health`, `metrics`).
   - `management.endpoint.health.show-details=always` shows detailed health information.
   - `management.endpoints.web.base-path` sets the base path for Actuator endpoints.

3. **Restart Your Application:**
   - Restart your Spring Boot application to apply the changes.

4. **Access Actuator Endpoints:**
   - Open a web browser or use a tool like `curl` or Postman to access the Actuator endpoints.

   **Health Endpoint:**
   ```bash
   curl http://localhost:8080/actuator/health
   ```
   - This endpoint provides the health status of the application.

   **Metrics Endpoint:**
   ```bash
   curl http://localhost:8080/actuator/metrics
   ```
   - This endpoint provides various metrics about the application.

5. **Explore Metrics:**
   - Access specific metrics by appending the metric name to the `/actuator/metrics` endpoint. For example, to view the JVM memory metrics:
   
   ```bash
   curl http://localhost:8080/actuator/metrics/jvm.memory
   ```

6. **Add Custom Metrics:**
   - In your project, navigate to a suitable package for configuration classes. This is often a package like `com.example.demo.config` or any other package dedicated to configuration.
   - To add custom metrics, create a `MeterRegistry` bean in one of your configuration classes.

   **Example:**
   ```java
   import io.micrometer.core.instrument.MeterRegistry;
   import org.springframework.boot.actuate.autoconfigure.metrics.MeterRegistryCustomizer;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;

   @Configuration
   public class MetricsConfig {

       @Bean
       public MeterRegistryCustomizer<MeterRegistry> metricsCommonTags() {
           return registry -> registry.config().commonTags("application", "demo-application");
       }
   }
   ```

   **Explanation:**
   - This configuration adds a common tag to all metrics.

7. **Test Custom Metrics:**
   - After adding custom metrics, restart your application and access the `/actuator/metrics` endpoint again to verify that your custom metrics are available.
   - Examples, check for the metric `http://localhost:8080/actuator/metrics/http.server.requests`

**Completion:**
- By completing this lab, you have integrated Spring Boot Actuator into your application, configured it to expose metrics, and learned how to access and customize metrics.

---

**Troubleshooting Tips:**
- **Endpoints Not Found:** Ensure that the base path and endpoint configuration are correct in `application.properties` or `application.yml`.
- **No Metrics Data:** Make sure your application is running and generating metrics. Custom metrics should be configured correctly.
- **Access Issues:** Ensure that your application is accessible at the configured port and that there are no firewall or network issues.

