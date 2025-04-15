# Docker Setup for PROMA Project Management App

This document provides instructions for running the PROMA Project Management application using Docker and Docker Compose.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Getting Started

### Running the Application

1. Clone the repository and navigate to the project root:

```bash
cd proma
```

2. Start all services using Docker Compose:

```bash
docker-compose up
```

This will build and start three containers:
- Client (Next.js frontend) - accessible at http://localhost:3000
- Server (Node.js backend) - accessible at http://localhost:8000
- PostgreSQL database - accessible at localhost:5432

3. To run in detached mode (background):

```bash
docker-compose up -d
```

4. To view logs:

```bash
# All logs
docker-compose logs

# Follow logs
docker-compose logs -f

# Service-specific logs
docker-compose logs client
docker-compose logs server
docker-compose logs postgres
```

### Database Seeding

The server container automatically runs migrations when it starts. To seed the database with sample data:

```bash
docker-compose exec server npm run seed
```

### Stopping the Application

To stop all containers:

```bash
docker-compose down
```

To stop and remove all containers, networks, and volumes:

```bash
docker-compose down -v
```

## Configuration

### Environment Variables

The Docker Compose setup uses environment variables defined in the docker-compose.yml file. If you need to customize these, you can:

1. Edit the docker-compose.yml file directly
2. Create a .env file in the project root for Docker Compose to use

### Database Connection

When running in Docker, the database connection URL is:

```
postgresql://postgres:postgres@postgres:5432/proma?schema=public
```

## Development Workflow

For development, you might prefer running some services in Docker and others locally:

### Running Only the Database in Docker

```bash
docker-compose up postgres
```

Then update your local .env files to connect to the Docker database:

For server/.env:
```
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/proma?schema=public
```

### Rebuilding Services

If you make changes to Dockerfiles or dependencies, rebuild the services:

```bash
docker-compose build
# Or for a specific service
docker-compose build client
```

## Troubleshooting

### Database Connection Issues

If the server can't connect to the database:

1. Check that the postgres container is running:
   ```bash
   docker-compose ps
   ```

2. Make sure the DATABASE_URL in server configuration points to the docker service name:
   ```
   postgresql://postgres:postgres@postgres:5432/proma?schema=public
   ```

3. Try restarting the server after the database is fully initialized:
   ```bash
   docker-compose restart server
   ```

### Client Cannot Connect to Server

If the client can't connect to the server API:

1. Verify the NEXT_PUBLIC_API_BASE_URL in the client service environment

2. Check that both services are on the same Docker network:
   ```bash
   docker network inspect proma-network
   ```

3. Ensure the server is listening on 0.0.0.0 (not just localhost)