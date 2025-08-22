# WEB303 Practical 1: Microservices Architecture with Go, gRPC, and Docker

## Overview

This practical demonstrates the implementation of a distributed microservices architecture using modern technologies including Go, gRPC, Protocol Buffers, and Docker. The project showcases inter-service communication patterns, containerization strategies, and service orchestration fundamentals.

### Learning Objectives Achieved

- **LO2**: Design and implement microservices using gRPC and Protocol Buffers for efficient inter-service communication
- **LO6**: Deploy microservices using Docker containerization as a foundation for Kubernetes orchestration
- **LO1**: Demonstrate understanding of microservices architecture patterns and trade-offs

## Quick Start Guide

### Prerequisites
- Go 1.24.5+
- Docker Desktop
- Protocol Buffers compiler
- grpcurl (for testing)

### Installation
```bash
# Install required tools
brew install protobuf grpcurl

# Install Go gRPC plugins
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2
```

### Running the Application
```bash
# Navigate to project directory
cd practical-one

# Build and start services
docker-compose up --build

# Test the system (in a new terminal)
grpcurl -plaintext \
    -import-path ./proto -proto greeter.proto \
    -d '{"name": "WEB303 Student"}' \
    0.0.0.0:50051 greeter.GreeterService/SayHello
```

## System Architecture

### Service Overview

| Service | Port | Visibility | Function |
|---------|------|------------|----------|
| **Greeter Service** | 50051 | Public | Handles greeting requests, orchestrates time retrieval |
| **Time Service** | 50052 | Internal | Provides current timestamp in RFC3339 format |

### Communication Flow

```
Client (grpcurl) 
    ↓ SayHello Request
Greeter Service (50051)
    ↓ GetTime Request  
Time Service (50052)
    ↑ Time Response
Greeter Service
    ↑ Personalized Greeting + Time
Client
```

### Key Architectural Decisions

1. **Service Separation**: Clear separation of concerns with dedicated time service
2. **gRPC Communication**: Type-safe, high-performance inter-service communication
3. **Container Isolation**: Each service runs in its own Docker container
4. **Network Abstraction**: Services communicate via Docker Compose networking

## Project Structure

```
practical-one/
├── proto/                          # Protocol Buffer definitions
│   ├── gen/proto/                  # Generated Go code
│   ├── greeter.proto              # Greeter service contract
│   └── time.proto                 # Time service contract
├── time-service/                   # Time microservice
│   ├── pb/                        # Local proto files
│   ├── Dockerfile                 # Container definition
│   ├── go.mod                     # Go dependencies
│   └── main.go                    # Service implementation
├── greeter-service/                # Greeter microservice
│   ├── pb/                        # Local proto files
│   ├── Dockerfile                 # Container definition
│   ├── go.mod                     # Go dependencies
│   └── main.go                    # Service implementation
├── docker-compose.yml             # Service orchestration
└── go.work                        # Go workspace configuration
```

## Implementation Details

### Protocol Buffer Definitions

**Time Service Contract:**
```protobuf
service TimeService {
  rpc GetTime(TimeRequest) returns (TimeResponse);
}
```

**Greeter Service Contract:**
```protobuf
service GreeterService {
  rpc SayHello(HelloRequest) returns (HelloResponse);
}
```

### Docker Configuration

- **Multi-stage builds** for optimized image sizes
- **Alpine Linux** base images for security and efficiency
- **Service dependencies** managed via Docker Compose
- **Internal networking** for secure service-to-service communication

## Expected Results

### Service Startup Logs
```
time-service-1     | Time service listening at [::]:50052
greeter-service-1  | Greeter service listening at [::]:50051
```

### Test Execution
```bash
$ grpcurl -plaintext \
    -import-path ./proto -proto greeter.proto \
    -d '{"name": "WEB303 Student"}' \
    0.0.0.0:50051 greeter.GreeterService/SayHello
```

### Expected Response
```json
{
  "message": "Hello WEB303 Student! The current time is 2025-08-22T10:37:10Z"
}
```

### Inter-Service Communication Logs
```
greeter-service-1  | Received SayHello request for: WEB303 Student
time-service-1     | Received request for time
```

## Demonstration Screenshot

The following screenshot shows the successful execution of the grpcurl test command and the expected JSON response:

![alt text](<Screenshot 2025-08-22 at 4.40.33 PM.png>)

## Technical Achievements

### Microservices Best Practices
- ✅ **Single Responsibility**: Each service has a focused purpose
- ✅ **Loose Coupling**: Services interact only through well-defined APIs
- ✅ **Technology Agnostic**: Services can be developed independently
- ✅ **Fault Isolation**: Container isolation prevents cascade failures

### gRPC Implementation
- ✅ **Type Safety**: Protocol Buffers ensure compile-time type checking
- ✅ **Performance**: Binary serialization with HTTP/2 transport
- ✅ **Cross-Platform**: Language-agnostic service definitions
- ✅ **Streaming Support**: Foundation for real-time communication patterns

### DevOps Integration
- ✅ **Containerization**: Consistent deployment environments
- ✅ **Orchestration**: Service lifecycle management with Docker Compose
- ✅ **Scalability Foundation**: Ready for Kubernetes migration
- ✅ **Development Workflow**: Local development with production parity

## Challenges Resolved

### Technical Issues
1. **Go Version Compatibility**: Resolved Docker build failures by updating to Go 1.24.5
2. **Package Structure**: Organized proto files to prevent namespace conflicts
3. **Service Discovery**: Configured Docker networking for reliable inter-service communication
4. **Build Optimization**: Implemented multi-stage Docker builds for smaller images

### Development Process
1. **Protocol-First Design**: Defined service contracts before implementation
2. **Incremental Testing**: Validated each service independently before integration
3. **Documentation**: Comprehensive README for reproducible setup.






