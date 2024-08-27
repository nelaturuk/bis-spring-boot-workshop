### **Lab 7: Packing and Deploying Your Spring Boot Application Using Docker**

**Objective:**  
In this lab, you will learn how to containerize your Spring Boot application using Docker. You will create a Docker image of your application and deploy it using Docker.

---

### **1. **Install Docker**

1. **Download and Install Docker:**
   - **For Windows and macOS:** Download Docker Desktop from [Docker's official website](https://www.docker.com/products/docker-desktop) and follow the installation instructions.
   - **For Linux:** Follow the [Docker installation guide for Linux](https://docs.docker.com/engine/install/) for your distribution.

2. **Verify Docker Installation:**
   - Open a terminal and run the following commands to check that Docker is installed and running:
     ```bash
     docker --version
     docker-compose --version
     ```

### **2. Create a Dockerfile**

1. **Navigate to Your Project Directory:**
   - Open a terminal and go to the root directory of your Spring Boot project.

2. **Create a Dockerfile:**
   - Create a new file named `Dockerfile` (no file extension) in the root directory of your project.

3. **Add the Following Content to the Dockerfile:**

   ```dockerfile
    # Use OpenJDK 21 as the base image
    FROM openjdk:21-jdk-slim AS base

    # Install Maven
    RUN apt-get update && \
        apt-get install -y maven && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    # Set the working directory
    WORKDIR /app

    # Copy the Maven settings file (optional)
    # COPY settings.xml /usr/share/maven/conf/

    # Use the base image for building the application
    FROM base AS build

    # Copy the pom.xml and download the dependencies
    COPY pom.xml .
    RUN mvn dependency:go-offline

    # Copy the source code
    COPY src ./src

    # Build the application
    RUN mvn package

    # Use OpenJDK 21 for the runtime
    FROM openjdk:21-jdk-slim

    # Set the working directory
    WORKDIR /app

    # Copy the JAR file from the build stage
    COPY --from=build /app/target/demo-0.0.1-SNAPSHOT.jar app.jar

    # Expose the port on which the application will run
    EXPOSE 8080

    # Run the application
    ENTRYPOINT ["java", "-jar", "app.jar"]

   ```

   **Explanation:**
   - **`WORKDIR /app`**: Sets the working directory in the container.
   - **`COPY pom.xml .`**: Copies the `pom.xml` file to the container.
   - **`RUN mvn dependency:go-offline`**: Downloads dependencies.
   - **`COPY src ./src`**: Copies the source code to the container.
   - **`RUN mvn package`**: Builds the application and creates a JAR file.
   - **`FROM openjdk:21-jdk-slim`**: Uses a slim OpenJDK image for runtime.
   - **`COPY --from=build /app/target/demo-0.0.1-SNAPSHOT.jar app.jar`**: Copies the built JAR file.
   - **`EXPOSE 8080`**: Exposes port 8080.
   - **`ENTRYPOINT ["java", "-jar", "app.jar"]`**: Defines the command to run the application.

### **3. Build the Docker Image**

1. **Run the Docker Build Command:**
   - In the terminal, execute the following command to build the Docker image:

     ```bash
     docker build -t demo-application .
     ```

   - **`-t demo-application`**: Tags the image with the name `demo-application`.

2. **Verify the Docker Image:**
   - Check that the Docker image was created successfully:

     ```bash
     docker images
     ```

   - You should see `demo-application` in the list of images.

### **4. Run the Docker Container**

1. **Start the Docker Container:**
   - Run the following command to start a container from the image:

     ```bash
     docker run -p 8080:8080 demo-application
     ```

   - **`-p 8080:8080`**: Maps port 8080 on your host to port 8080 in the container.

2. **Verify the Application is Running:**
   - Open a web browser and go to `http://localhost:8080`.
   - Verify that your Spring Boot application is accessible.

### **5. Manage Docker Containers**

1. **List Running Containers:**
   - To see the list of running containers:

     ```bash
     docker ps
     ```

2. **Stop a Running Container:**
   - To stop a container, use the container ID from `docker ps`:

     ```bash
     docker stop <container_id>
     ```

3. **Remove a Container:**
   - To remove a stopped container:

     ```bash
     docker rm <container_id>
     ```

### **6. (Optional) Push Docker Image to a Registry**

1. **Tag the Docker Image:**
   - Tag the image for a Docker registry (e.g., Docker Hub):

     ```bash
     docker tag demo-application <your_dockerhub_username>/demo-application:latest
     ```

2. **Push the Image:**
   - Push the tagged image to Docker Hub:

     ```bash
     docker push <your_dockerhub_username>/demo-application:latest
     ```

### **Completion**

By following these instructions, you have containerized your Spring Boot application and deployed it using Docker. You have also learned how to manage Docker images and containers.