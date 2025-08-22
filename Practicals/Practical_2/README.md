# Go Microservices Demo

A demonstration of microservices architecture in Go, featuring service discovery with Consul and an API Gateway for request routing.

[![GitHub Repository](https://img.shields.io/badge/GitHub-Repository-blue?style=for-the-badge&logo=github)](https://github.com/KeldenPDorji/go-microservices-demo)

## Architecture Overview

This project implements a simple microservices architecture with the following components:

- **API Gateway** (Port 8080) - Routes requests to appropriate microservices
- **Users Service** (Port 8081) - Handles user-related operations
- **Products Service** (Port 8082) - Handles product-related operations
- **Consul** - Service discovery and health checking

### Infrastructure & Deployment
- **Docker Containerization**: Package each service in Docker containers for consistent deployment
- **Kubernetes Deployment**: Create K8s manifests for orchestration and scaling
- **Helm Charts**: Package the application for easy Kubernetes deployment
- **CI/CD Pipeline**: Automated testing and deployment workflows

### Security & Authentication
- **JWT Authentication**: Add token-based authentication to the API Gateway
- **Service-to-Service Security**: Implement mTLS for secure inter-service communication
- **Rate Limiting**: Add request rate limiting to prevent abuse
- **API Versioning**: Implement versioning strategy for backward compatibility

### Observability & Monitoring
- **Distributed Tracing**: Add OpenTelemetry/Jaeger for request tracing across services
- **Centralized Logging**: Implement structured logging with ELK stack or similar
- **Metrics Collection**: Add Prometheus metrics and Grafana dashboards
- **Health Monitoring**: Enhanced health checks and alerting

### Data & Persistence
- **Database Integration**: Add PostgreSQL/MongoDB for data persistence
- **Database per Service**: Implement proper data isolation patterns
- **Event Sourcing**: Add event-driven architecture for better scalability
- **Caching Layer**: Implement Redis for performance optimization

### Advanced Features
- **Circuit Breakers**: Add resilience patterns using libraries like Hystrix
- **Load Balancing**: Implement intelligent load balancing strategies
- **Configuration Management**: Add centralized configuration with Consul KV or similar
- **Message Queues**: Add async communication with RabbitMQ/Apache Kafka

### Development Experience
- **API Documentation**: Generate OpenAPI/Swagger documentation
- **Testing Suite**: Add comprehensive unit, integration, and end-to-end tests
- **Local Development**: Docker Compose setup for local development environment
- **Code Quality**: Add linting, formatting, and static analysis tools

## Architecture Overview

This project implements a simple microservices architecture with the following components:

- **API Gateway** (Port 8080) - Routes requests to appropriate microservices
- **Users Service** (Port 8081) - Handles user-related operations
- **Products Service** (Port 8082) - Handles product-related operations
- **Consul** - Service discovery and health checking

## Project Structure

```
go-microservices-demo/
├── README.md
├── api-gateway/
│   ├── go.mod
│   ├── go.sum
│   └── main.go
└── services/
    ├── products-service/
    │   ├── go.mod
    │   ├── go.sum
    │   └── main.go
    └── users-service/
        ├── go.mod
        ├── go.sum
        └── main.go
```

## Features

### API Gateway
- **Intelligent Routing**: Routes requests based on URL paths (`/api/users/*` → users-service, `/api/products/*` → products-service)
- **Service Discovery**: Uses Consul to dynamically discover healthy service instances
- **Reverse Proxy**: Forwards requests to appropriate microservices
- **Path Rewriting**: Strips API gateway prefixes before forwarding requests

### Microservices
- **Auto-Registration**: Services automatically register themselves with Consul on startup
- **Health Checks**: Each service provides a `/health` endpoint for Consul monitoring
- **Chi Router**: Uses the lightweight Chi router for HTTP routing
- **Unique Service IDs**: Services register with hostname-based unique identifiers

## Prerequisites

- Go 1.24.5 or later
- Consul (for service discovery)

## Installation & Setup

### 1. Install Consul

**macOS (using Homebrew):**
```bash
brew install consul
```

**Other platforms:** Download from [Consul's official website](https://www.consul.io/downloads)

### 2. Clone and Setup Project

```bash
git clone <your-repo-url>
cd go-microservices-demo

# Install dependencies for each service
cd api-gateway && go mod tidy && cd ..
cd services/users-service && go mod tidy && cd ../..
cd services/products-service && go mod tidy && cd ../..
```

## Running the Application

### 1. Start Consul

```bash
consul agent -dev
```

This starts Consul in development mode on `localhost:8500`.

### 2. Start the Microservices

**Terminal 1 - Users Service:**
```bash
cd services/users-service
go run main.go
```

**Terminal 2 - Products Service:**
```bash
cd services/products-service
go run main.go
```

**Terminal 3 - API Gateway:**
```bash
cd api-gateway
go run main.go
```

## API Endpoints

### Health Checks
- `GET /health` - Available on all services (ports 8081, 8082)

### Through API Gateway (Port 8080)
- `GET /api/users/{id}/products` - Get user details via users-service
- `GET /api/products/{id}` - Get product details via products-service

### Examples

```bash
# Check if services are healthy
curl http://localhost:8081/health
curl http://localhost:8082/health

# Make requests through API Gateway
curl http://localhost:8080/api/users/123/products
curl http://localhost:8080/api/products/456

# Check Consul UI
open http://localhost:8500
```

## Demo Screenshots

### Consul Health Dashboard

![alt text](<Screenshot 2025-08-21 at 2.44.23 PM.png>)

The Consul service mesh health dashboard showing:
- **Serf Health Status**: Consul agent is "alive and reachable"
- **products-service**: Running on port 8082, health check returns "Service is healthy"
- **users-service**: Running on port 8081, health check returns "Service is healthy"
- All services showing green checkmarks, indicating they're healthy and discoverable

### API Gateway in Action

![alt text](<Screenshot 2025-08-22 at 1.42.33 AM.png>)

API Gateway startup and request handling:
- **Port 8080**: API Gateway running and ready to accept requests
- **Service Discovery**: Gateway discovering 'users-service' at http://Keldens-MacBook-Air.local:8081
- **Request Forwarding**: Automatic routing of `/api/users/123` requests to the users-service
- **Load Balancing**: Gateway acting as reverse proxy, routing traffic to appropriate microservices

### Service Response

![alt text](<Screenshot 2025-08-22 at 1.44.15 AM.png>)

Testing the microservices:
- **Gateway Route**: `curl http://localhost:8080/api/users/123` - calling through API Gateway
- **Service Response**: "Details for user 123" returned from users-service
- **End-to-End**: Demonstrates both gateway routing and direct service communication working correctly

## Key Dependencies

- **[Chi Router](https://github.com/go-chi/chi)** - Lightweight HTTP router
- **[Consul API](https://github.com/hashicorp/consul/api)** - Service discovery and health checking
- **Go Standard Library** - HTTP server and reverse proxy functionality

## How It Works

1. **Service Registration**: When microservices start, they register themselves with Consul, including:
   - Service name and unique ID
   - Network address and port
   - Health check endpoint

2. **Request Flow**:
   - Client sends request to API Gateway (`/api/users/123/products`)
   - Gateway extracts service name from path (`users` → `users-service`)
   - Gateway queries Consul for healthy instances of the service
   - Gateway creates reverse proxy to forward the request
   - Gateway rewrites the path (`/api/users/123/products` → `/123/products`)
   - Service processes request and returns response

3. **Health Monitoring**: Consul continuously monitors service health via the `/health` endpoints

## Service Discovery Benefits

- **Dynamic Scaling**: Services can be added/removed without reconfiguring the gateway
- **Load Balancing**: Multiple instances of the same service can be discovered
- **Fault Tolerance**: Unhealthy services are automatically excluded from routing
- **Zero Downtime Deployments**: New service versions can be deployed while old ones are still running

## Development Notes

- Each service runs on a different port to avoid conflicts during local development
- Service registration includes hostname-based unique IDs to support multiple instances
- The API Gateway uses the first healthy instance found (production systems would implement load balancing)
- All services use structured logging for better observability





