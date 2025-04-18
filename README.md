# Repository Cloner Microservice

This is a simple Flask-based microservice that clones a Git repository when triggered via an HTTP API call. It is useful for automating the process of cloning repositories in your CI/CD pipeline, or integrating with Tekton pipelines or any other automation tool.

## Features

- **Cloning Git Repositories**: The microservice accepts a POST request with the repository URL and branch name and clones the repository to the container's file system.
- **Health Check**: Includes a `/health` endpoint to verify if the service is running and responsive.
- **Logging**: The service logs every action taken (such as repository cloning) for debugging and monitoring purposes.

## Requirements

Before running the service, make sure you have the following:

- **Python 3.x** installed.
- **Git** installed in the container or host system.
- **Flask** installed (`pip install flask`).
- **Podman** or **Docker** to build and run the container (if running in a containerized environment).

### Prerequisites

1. **Install Flask**:
   Ensure that Flask is installed in your Python environment. You can install Flask using pip:

   ```bash
   pip install flask
   ```

2. **Install Git**:
   The container or machine needs to have Git installed to clone repositories. Make sure to install it if it's not already available:

   ```bash
   apt-get install git  # On Ubuntu/Debian
   ```

3. **Containerization (optional)**:
   If you want to run this as a containerized microservice, use **Podman** or **Docker** to build and run the service.

## How It Works

This microservice provides two endpoints:

### 1. `/build` (POST)

This endpoint accepts a `POST` request with a JSON payload containing the repository URL and the branch name (optional, defaults to `main`).

#### Example Request:
- install this servcie on publci ip server 
```bash
curl -X POST http://sonarx86-machine1.fyre.ibm.com:5000/build \
  -H 'Content-Type: application/json' \
  -d '{"repo": "https://github.com/vijay2181/frontend-app.git", "branch": "main"}'
```

#### Expected Response:
If the repository is cloned successfully:
```json
{
  "status": "success",
  "output": "Successfully cloned https://github.com/vijay2181/frontend-app.git (main branch)"
}
```

If there's an error (e.g., repository not found):
```json
{
  "status": "error",
  "message": "Cloning failed: [error message]"
}
```

### 2. `/health` (GET)

This endpoint is used for health checks. It responds with a simple JSON indicating the service is up and running.

#### Example Request:
```bash
curl http://sonarx86-machine1.fyre.ibm.com:5000/health
```

#### Expected Response:
```json
{
  "status": "healthy"
}
```

## Running the Service

### Option 1: Run Locally

1. **Clone this repository** (or create the file structure for `build_service.py`).
2. Install dependencies:

   ```bash
   pip install flask
   ```

3. Start the Flask application:

   ```bash
   python build_service.py
   ```

   This will run the service locally on port `5000` by default.

4. You can now send a `POST` request to `http://localhost:5000/build` with a JSON payload to clone a repository.

---

### Option 2: Run in a Container (Using Podman or Docker)

If you prefer to run the microservice inside a container, follow these steps:

1. **Create a Dockerfile** (or use the `Dockerfile` from this repository):

   ```Dockerfile
   # Use an official Python runtime as a parent image
   FROM python:3.9-slim

   # Set the working directory in the container
   WORKDIR /app

   # Copy the current directory contents into the container at /app
   COPY . /app

   # Install any needed packages specified in requirements.txt
   RUN pip install -r requirements.txt

   # Expose port 5000
   EXPOSE 5000

   # Run build_service.py when the container launches
   CMD ["python", "build_service.py"]
   ```

2. **Build the image**:

   ```bash
   podman build -t x86-builder-service .
   ```

3. **Run the container**:

   ```bash
   podman run -d -p 5000:5000 x86-builder-service
   ```

4. The service will now be available at `http://<host_ip>:5000`.

---

## Logging

The service logs key actions (such as the cloning process) to the console. The logs help to track the success or failure of cloning operations. 

Logs can be viewed directly in the container logs or from the console output.

---

## Troubleshooting

- **"git: not found" Error**: Ensure that Git is installed in the container or system where the service is running.
  - For containerized environments, add `RUN apt-get install -y git` to your `Dockerfile` to ensure Git is available.
  
- **Cloning errors**: Check the provided repository URL and make sure it's accessible from the machine or container where the service is running.
  
- **Port Issues**: If the service is running in a container, make sure the port `5000` is exposed and mapped correctly. Use `-p 5000:5000` to expose the port.

---

## Conclusion

This microservice is a simple way to automate the process of cloning Git repositories using HTTP requests. It can be used in CI/CD pipelines or as part of any automation system. With basic logging and health check capabilities, it’s easy to integrate and monitor in larger systems.
