# Using Your Custom WAHA Docker Image

This guide explains how to build and use your custom WAHA Docker image in different environments.

## Building the Image

1. Ensure you have the latest code and dependencies:

   ```bash
   yarn install
   yarn build
   ```

2. Build the Docker image:

   ```bash
   docker-compose build
   ```

   This creates a local image named `waha_waha`.

## Running with Docker Compose

### Basic Usage

- Start the container:

  ```bash
  docker-compose up -d
  ```

- Access the app at `http://localhost:3001` (or your configured port).

### Using Different Environments

#### 1. Environment Variables via .env File

Create or modify `.env` file in the project root:

```env
# Database
WHATSAPP_SESSIONS_MONGO_URL=mongodb://username:password@host:port/database

# Engine
WAHA_DEFAULT_ENGINE=WEBJS

# Other configs
WAHA_API_KEY=your-api-key
```

Then run:

```bash
docker-compose up -d
```

#### 2. Override with Command-Line Env Vars

```bash
WAHA_EDITION=PLUS WAHA_DEFAULT_ENGINE=GOWS docker-compose up -d
```

#### 3. Multiple .env Files for Different Environments

- Create `.env.dev`, `.env.prod`, etc.
- Run with specific env file:

  ```bash
  docker-compose --env-file .env.dev up -d
  ```

## Tagging and Pushing to Registry

1. Tag the image:

   ```bash
   docker tag waha_waha your-registry/waha-custom:v1.0
   ```

2. Push to registry:

   ```bash
   docker push your-registry/waha-custom:v1.0
   ```

3. Update `docker-compose.yaml`:

   ```yaml
   services:
     waha:
       image: your-registry/waha-custom:v1.0
   ```

## Custom Configurations

### Ports

Change the port in `docker-compose.yaml`:

```yaml
ports:
  - '127.0.0.1:3002:3000/tcp'  # Change 3001 to desired port
```

### Volumes

Modify volumes for custom paths:

```yaml
volumes:
  - './my-sessions:/app/.sessions'
  - './my-media:/app/.media'
```

### Networks

For custom networks, add to `docker-compose.yaml`:

```yaml
networks:
  my-network:
    driver: bridge
services:
  waha:
    networks:
      - my-network
```

## Troubleshooting

- **Port conflicts**: Change the host port if 3001 is in use.
- **Database issues**: Ensure MongoDB/PostgreSQL is running and accessible.
- **Logs**: Check logs with `docker-compose logs waha`.
- **Rebuild**: After code changes, run `docker-compose build --no-cache`.

## Environment-Specific Examples

### Development

```bash
# .env.dev
WAHA_DEFAULT_ENGINE=WEBJS
WAHA_LOG_LEVEL=debug
```

### Production

```bash
# .env.prod
WAHA_DEFAULT_ENGINE=GOWS
WAHA_API_KEY=secure-key
WHATSAPP_SESSIONS_MONGO_URL=mongodb://prod-db:27017/waha
```

Run with: `docker-compose --env-file .env.prod up -d`

For more options, refer to the main README.md and WAHA documentation.
