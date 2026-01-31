# AMD64 Development Environment Guide

A Docker-based development environment for running Node.js projects with AMD64 architecture compatibility on any system.

## Quick Start

### 1. Copy Required Files
Copy these files to your project root:
- `Dockerfile.dev` - Ubuntu-based development container with Node.js
- `docker-compose.dev-template.yml` - Docker Compose configuration (rename to `docker-compose.yml` or `docker-compose.dev.yml`)

### 2. Configure for Your Project

#### Update docker-compose.yml:
```yaml
services:
  project-dev:  # Rename to your project name
    container_name: your-project-name  # Update container name
    ports:
      - "3000:3000"  # Update port mapping as needed
    environment:
      - NODE_VERSION=${NODE_VERSION:-18}  # Set your Node.js version
```

#### Set Node.js Version:
```bash
# Option 1: Environment variable
export NODE_VERSION=20
docker compose up --build

# Option 2: Inline
NODE_VERSION=20 docker compose up --build

# Option 3: .env file
echo "NODE_VERSION=20" > .env
docker compose up --build
```

### 3. Run Your Development Environment

```bash
# Build and start the container
docker compose up --build

# Run in detached mode
docker compose up -d --build

# Force rebuild when NODE_VERSION changes
docker compose build --no-cache && docker compose up

# Access the container shell
docker compose exec project-dev bash
```

## Features

- **Ubuntu 24.04** base image with sudo-enabled developer user
- **NVM** for Node.js version management
- **Automatic rebuilds** when NODE_VERSION changes
- **Hot reload** support with proper file watching
- **AMD64 platform** compatibility for M1/M2 Macs
- **Volume mounting** preserves node_modules between rebuilds

## Customization

### Common Adjustments

#### Different Port:
```yaml
ports:
  - "8080:8080"  # host:container
```

#### Additional Environment Variables:
```yaml
environment:
  - API_URL=http://localhost:4000
  - DATABASE_URL=postgresql://...
```

#### Multiple Services:
```yaml
services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    # ...
  
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    # ...
```

#### Custom Commands:
```yaml
command: npm run dev  # Override default command
```

## Troubleshooting

### Permission Issues
The container runs as `developer` user with sudo access. Use `sudo` for system-level operations.

### Node Version Not Changing
Force rebuild: `docker compose build --no-cache`

### File Watching Not Working
Ensure `CHOKIDAR_USEPOLLING=true` is set (included by default).

### Port Already in Use
Change the host port in docker-compose.yml or stop the conflicting service.

## Project Structure Example

```
your-project/
├── Dockerfile.dev
├── docker-compose.yml
├── package.json
├── package-lock.json
└── src/
    └── ...
```

## Commands Reference

```bash
# Start development environment
docker compose up

# Rebuild with new Node version
NODE_VERSION=18 docker compose up --build

# Stop containers
docker compose down

# Remove containers and volumes
docker compose down -v

# View logs
docker compose logs -f

# Execute commands in container
docker compose exec project-dev npm install
docker compose exec project-dev npm run test
```