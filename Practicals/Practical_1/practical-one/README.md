# Practical One: Microservices Communication Demo

## Quick Start

1. **Build and Run:**
   ```bash
   docker-compose up --build
   ```

2. **Test the Service:**
   ```bash
   grpcurl -plaintext \
       -import-path ./proto -proto greeter.proto \
       -d '{"name": "WEB303 Student"}' \
       0.0.0.0:50051 greeter.GreeterService/SayHello
   ```

3. **Expected Response:**
   ```json
   {
     "message": "Hello WEB303 Student! The current time is 2025-08-22T10:37:10Z"
   }
   ```

## How It Works

1. **Client Request**: grpcurl sends a SayHello request to greeter-service:50051
2. **Service-to-Service Call**: greeter-service calls time-service:50052 for current time
3. **Response Composition**: greeter-service combines greeting with time and responds
4. **Final Response**: Client receives personalized greeting with timestamp

## Services

- **greeter-service**: Port 50051 (public) - Handles greeting requests
- **time-service**: Port 50052 (internal) - Provides current time

Both services communicate over Docker's internal network using gRPC.
