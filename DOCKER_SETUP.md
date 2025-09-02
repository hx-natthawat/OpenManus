# OpenManus Docker Setup Guide

This guide helps you set up OpenManus using Docker Compose for easy deployment and development.

## Quick Start

1. **Clone and navigate to the project**:

   ```bash
   cd /path/to/OpenManus
   ```

2. **Set up configuration**:

   ```bash
   cp config.toml.example config/config.toml
   # Edit config/config.toml with your API keys and settings
   ```

3. **Start the main application**:

   ```bash
   docker-compose up -d openmanus
   ```

## Configuration

### Required Configuration

Edit your `config/config.toml` file with the following required settings:

```toml
# Primary LLM Configuration (Choose one)
[llm]
api_key = "your_api_key_here"

# For Anthropic Claude (default)
model = "claude-3-7-sonnet-latest"
base_url = "https://api.anthropic.com/v1/"

# For OpenAI (uncomment to use)
# model = "gpt-4o"
# base_url = "https://api.openai.com/v1/"
```

## Service Profiles

The docker-compose setup includes optional services using profiles:

### Core Service (Always Available)

- **openmanus**: Main application with MCP server

### Optional Services

- **ollama**: Local LLM service
- **cache**: Redis for caching
- **database**: PostgreSQL for data persistence

## Usage Examples

### 1. Basic Setup (Recommended)

```bash
# Start only the main application
docker-compose up -d openmanus
```

### 2. With Local LLM (Ollama)

```bash
# Start with Ollama for local inference
docker-compose --profile ollama up -d

# Pull a model (run once)
docker-compose exec ollama ollama pull llama3.2
```

### 3. Full Stack with Database and Cache

```bash
# Start all services
docker-compose --profile database --profile cache up -d
```

### 4. Development Mode

```bash
# Start with logs visible
docker-compose up openmanus

# Or start in background and follow logs
docker-compose up -d openmanus
docker-compose logs -f openmanus
```

## Interacting with OpenManus

### MCP Server Mode

The default setup runs OpenManus as an MCP server:

```bash
# The server is accessible on port 8000 (configurable via MCP_PORT)
# Connect your MCP client to: localhost:8000
```

### Interactive Mode

```bash
# Run the main agent interactively
docker-compose exec openmanus python main.py --prompt "Your task here"

# Or enter interactive shell
docker-compose exec openmanus bash
```

## Directory Structure

The setup creates the following volume mounts:

- `./config` → `/app/OpenManus/config` (Configuration files)
- `./data` → `/app/OpenManus/data` (Application data)
- `./logs` → `/app/OpenManus/logs` (Log files)

## Troubleshooting

### Browser Issues

If you encounter browser-related errors:

```bash
# Increase shared memory
docker-compose down
# Edit docker-compose.yml and increase shm_size if needed
docker-compose up -d
```

### Permission Issues

```bash
# Fix file permissions
sudo chown -R $USER:$USER ./config ./data ./logs
```

### Configuration Issues

```bash
# Verify your configuration file exists and is readable
docker-compose exec openmanus ls -la config/
docker-compose exec openmanus cat config/config.toml
```

### View Logs

```bash
# View application logs
docker-compose logs openmanus

# Follow logs in real-time
docker-compose logs -f openmanus
```

## Stopping Services

```bash
# Stop all services
docker-compose down

# Stop and remove volumes (careful: this deletes data!)
docker-compose down -v

# Stop specific service
docker-compose stop openmanus
```

## Advanced Configuration

### Custom Configuration Files

Place your custom configuration files in the `./config` directory:

- `config.toml` - Main configuration (copy from `config.toml.example`)
- `mcp.json` - MCP server configuration

### Configuration Reference

See `config.toml.example` for all available configuration options and their descriptions. The TOML format provides better structure and validation than environment variables.

### Scaling

```bash
# Run multiple instances (if needed)
docker-compose up -d --scale openmanus=3
```

## Security Notes

- The setup includes necessary capabilities for browser automation
- Docker socket is mounted for sandbox functionality
- Consider running in a restricted environment for production use
- Always use strong passwords for database services

## Support

For issues and questions:

1. Check the logs: `docker-compose logs openmanus`
2. Verify your configuration in `config/config.toml`
3. Ensure all required API keys are set in the TOML file
4. Check the main project documentation
