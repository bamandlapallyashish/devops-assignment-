Internship Assignment
Overview
This project sets up a Dockerized application with two services (service1 and service2) behind an Nginx reverse proxy. The services are written in Go (service1) and Python Flask (service2), each exposing /ping and /hello endpoints. Nginx routes requests from /service1/* to service1:8001 and /service2/* to service2:8002, using a custom log format to include timestamps and request paths.
Project Structure
devops-assignment/
├── docker-compose.yml       # Defines services, networking, and health checks
├── nginx/
│   ├── Dockerfile          # Builds Nginx container with custom config
│   └── nginx.conf          # Nginx configuration for reverse proxy and logging
├── service_1/
│   ├── Dockerfile          # Builds Go service
│   ├── main.go             # Go source code for service1
│   └── README.md           # Service1-specific documentation
├── service_2/
│   ├── Dockerfile          # Builds Python Flask service
│   ├── app.py              # Flask source code for service2
│   ├── requirements.txt    # Python dependencies
│   └── README.md           # Service2-specific documentation
├── README.md               # This file
└── .gitignore              # Ignores build artifacts and sensitive files

Technical Constraints

Nginx in Docker: Nginx runs in a dedicated container (devops-assignment-nginx-1), not on the host, as required.
Bridge Networking: All services use a Docker bridge network (app-network) for communication, ensuring no host networking is used.

Setup Instructions

Prerequisites:

Docker and Docker Compose installed.
Git for cloning the repository.


Clone the Repository:
git clone https://github.com/your-username/devops-assignment.git
cd devops-assignment


Run the Application:
docker-compose up --build

This builds and starts:

nginx on port 8080
service1 (Go) on port 8001
service2 (Python Flask) on port 8002


Test Endpoints:
curl http://localhost:8080/service1/ping    # Returns {"service":"1","status":"ok"}
curl http://localhost:8080/service1/hello   # Returns {"message":"Hello from Service 1"}
curl http://localhost:8080/service2/ping    # Returns {"service":"2","status":"ok"}
curl http://localhost:8080/service2/hello   # Returns {"message":"Hello from Service 2"}


View Logs:
docker logs devops-assignment-nginx-1

Logs show custom format with timestamps and request paths, e.g.:
172.20.0.1 - - [24/Jun/2025:20:47:07 +0530] "GET /service1/ping HTTP/1.1" 200 36 "-" "curl/7.68.0"



Health Checks

service1: Uses curl -f http://localhost:8001/ping every 30 seconds.
service2: Uses curl -f http://localhost:8002/ping every 30 seconds.
Nginx depends on both services being healthy.

Notes

The Flask warning (WARNING: This is a development server) is expected and safe for this assignment, as services run behind Nginx.
Nginx includes a 5-second startup delay to ensure service1 and service2 are ready.

Troubleshooting

If Nginx exits, check logs: docker logs devops-assignment-nginx-1.
Ensure port 8080 is free: netstat -tuln | grep 8080.
Rebuild without cache if issues persist: docker-compose build --no-cache.
