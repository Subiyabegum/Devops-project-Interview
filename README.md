# DevOps Take-Home Exercise - Microservices Bootstrap

This repository contains two simple microservices designed as a starting point for DevOps engineering exercises. The services demonstrate basic inter-service communication and require containerization and deployment configuration.

## Architecture Overview

```
┌─────────────────────┐         HTTP GET           ┌─────────────────────┐
│                     │  ────────────────────────> │                     │
│  Node.js Service    │   /sum?a=X&b=Y (repeated)  │  Python Service     │
│  (Express)          │  <──────────────────────── │  (FastAPI)          │
│  Port: 3000         │      JSON response         │  Port: 8000         │
└─────────────────────┘                            └─────────────────────┘
```

### Services

#### 1. Python FastAPI Service (`python/`)
- **Framework:** FastAPI with Uvicorn
- **Port:** 8000 (configurable via `.env`)
- **Endpoints:**
  - `GET /health` - Returns service health status
  - `GET /sum?a=<number>&b=<number>` - Returns the sum of two numbers

#### 2. Node.js Express Service (`node/`)
- **Framework:** Express.js
- **Port:** 3000 (configurable via `.env`)
- **Endpoints:**
  - `GET /health` - Returns service health status
  - `GET /multiply?a=<number>&b=<number>` - Multiplies two numbers by repeatedly calling Python's `/sum` endpoint

### How They Connect

The Node service implements multiplication using a **silly algorithm**: it calls the Python service's `/sum` endpoint multiple times to perform repeated addition. For example, to calculate `5 × 3`, it calls `/sum?a=0&b=5` three times, accumulating the result.

This intentionally inefficient design demonstrates:
- Inter-service HTTP communication
- Service dependency management
- Configuration via environment variables
- The importance of proper service orchestration

## Quick Start

### Running Locally

1. **Start the Python service:**
   ```bash
   cd python
   ./start.sh
   ```
   The service will run on http://localhost:8000

2. **Start the Node service** (in a new terminal):
   ```bash
   cd node
   ./start.sh
   ```
   The service will run on http://localhost:3000

3. **Test the services:**
   ```bash
   # Test Python sum service
   curl "http://localhost:8000/sum?a=5&b=3"
   
   # Test Node multiply service (calls Python service)
   curl "http://localhost:3000/multiply?a=5&b=3"
   ```

## Configuration

Both services use `.env` files for configuration:

- **Python service:** `PORT` (default: 8000)
- **Node service:** `PORT` (default: 3000), `PYTHON_SERVICE_URL` (default: http://localhost:8000)

The `.env.example` files in each service directory serve as templates. The startup scripts automatically create `.env` files if they don't exist.

## Project Structure

```
.
├── README.md                 # This file
├── python/                   # Python FastAPI service
│   ├── app/
│   │   └── main.py          # FastAPI application
│   ├── requirements.txt     # Python dependencies
│   ├── .env.example         # Environment template
│   ├── .gitignore          # Git ignore rules
│   ├── start.sh            # Startup script
│   └── README.md           # Service-specific docs
└── node/                    # Node.js Express service
    ├── src/
    │   └── index.js        # Express application
    ├── package.json        # Node dependencies
    ├── .env.example        # Environment template
    ├── .gitignore         # Git ignore rules
    ├── start.sh           # Startup script
    └── README.md          # Service-specific docs
```

---

## Your Evaluation Task

Your task is to **fork this repository** and implement the following requirements:

### Required Tasks

1. **Containerization**
   - Create a `Dockerfile` for the Python service
   - Create a `Dockerfile` for the Node.js service
   - Ensure both images build successfully and services run in containers

2. **Orchestration**
   - Create a `docker-compose.yml` file that runs both services together
   - Properly configure service networking so the Node service can communicate with the Python service
   - Ensure environment variables are correctly passed to containers
   - Services should be accessible from the host machine

3. **CI/CD Pipeline**
   - Set up GitHub Actions workflows for both services
   - Implement automated testing (linting, basic health checks)
   - Build and push Docker images on successful builds
   - (Bonus) Implement automatic versioning/tagging

4. **Documentation**
   - Update this README with:
     - Instructions on how to build and run with Docker
     - Instructions on how to use docker-compose
     - Any environment variables that need to be configured
     - How to verify the services are working correctly
   - Document your CI/CD pipeline

### Evaluation Criteria

Your submission will be evaluated on:
- **Correctness:** Do the services run properly in containers?
- **Best Practices:** Are you following Docker and DevOps best practices?
- **Environment Management:** Are secrets and configuration properly handled?
- **Documentation:** Is your work well-documented and easy to follow?
- **CI/CD Implementation:** Is the pipeline efficient and robust?

### Submission

When you're done:
1. Ensure all changes are committed to your forked repository
2. Share the link to your fork
3. Include any additional notes or assumptions in your updated README

Good luck! 🚀

## Notes

- The `.env` files are excluded from version control (see `.gitignore`)
- Shell scripts are set to executable by default
- Services must start in order: Python first, then Node (due to dependency)
- All code is intentionally simple to focus on DevOps practices rather than application complexity
