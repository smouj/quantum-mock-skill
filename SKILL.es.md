---
name: Quantum Mock
slug: quantum-mock
type: devops
version: 1.0.0
description: AI-powered backend service mocking for rapid development and testing
tags: [mocking, backend, api, testing, simulation, devops]
author: OpenClaw Team
category: testing
complexity: medium
---

# Quantum Mock (ES) - Skill Documentation

## Purpose

Quantum Mock generates realistic, AI-powered mock servers that simulate entire backend services. It creates intelligent responses based on API schemas, existing data patterns, or descriptive prompts.

### Real Use Cases

1. **Frontend Development Without Backend** - When frontend devs need to work on UI before APIs are ready, Quantum Mock generates realistic endpoints that behave like production APIs
2. **Integration Testing** - Create deterministic mock responses for E2E tests that simulate edge cases, errors, and timeout scenarios
3. **API Prototyping** - Rapidly prototype API behavior and iterate on contracts before implementation
4. **Third-Party API Simulation** - Mock expensive or rate-limited third-party services (Stripe, Twilio, AWS) during development
5. **Load Testing Baselines** - Generate consistent mock responses for performance testing without hitting real services

## Scope

### Core Commands

| Command | Description |
|---------|-------------|
| `quantum-mock init` | Initialize a new mock project with config |
| `quantum-mock serve` | Start the mock server |
| `quantum-mock generate` | Generate mocks from OpenAPI spec or prompt |
| `quantum-mock status` | Show running mock server status |
| `quantum-mock stop` | Stop the mock server |

### Flags and Options

```bash
# Initialize new mock project
quantum-mock init --name payment-service --port 8080 --template openapi

# Start mock server with hot reload
quantum-mock serve --config ./quantum.json --watch --port 3001

# Generate from OpenAPI 3.0 spec
quantum-mock generate --spec ./api.yaml --output ./mocks --strategy dynamic

# Generate from natural language prompt
quantum-mock generate --prompt "Twitter-like API with tweets, users, likes, retweets" --lang es

# Start with specific response delays (for timeout testing)
quantum-mock serve --latency 50-200 --error-rate 5

# Status with verbose output
quantum-mock status --verbose --json
```

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `QUANTUM_PORT` | Default server port | `8080` |
| `QUANTUM_HOST` | Server bind host | `localhost` |
| `QUANTUM_LOG_LEVEL` | Debug, info, warn, error | `info` |
| `QUANTUM_STORAGE` | Path to response storage | `./.quantum/storage` |
| `QUANTUM_AI_PROVIDER` | ai provider (openai, anthropic, local) | `openai` |
| `QUANTUM_AI_MODEL` | Model to use for generation | `gpt-4` |
| `QUANTUM_SEED` | Seed for deterministic responses | `random` |

## Work Process

### Phase 1: Project Initialization

1. Run `quantum-mock init` to scaffold project structure
2. Edit `quantum.json` to configure:
   - Base URL and port
   - API schema source (file path or URL)
   - AI provider credentials
   - Default response templates
3. Verify initialization: `quantum-mock status`

### Phase 2: Mock Generation

1. **From OpenAPI/Swagger**:
   ```bash
   quantum-mock generate \
     --spec ./openapi.yaml \
     --output ./mocks \
     --strategy dynamic \
     --覆盖-existing
   ```

2. **From Prompt**:
   ```bash
   quantum-mock generate \
     --prompt "E-commerce API with products, cart, checkout, orders, users" \
     --output ./mocks \
     --lang en
   ```

3. Review generated mocks in `./mocks/` directory

### Phase 3: Server Lifecycle

1. Start server: `quantum-mock serve --config ./quantum.json`
2. Test endpoints: `curl http://localhost:8080/api/products`
3. Modify responses live: Edit `./mocks/` files, changes auto-reload
4. Stop server: `quantum-mock stop`

### Phase 4: Advanced Configuration

1. Add custom response delays: Edit `quantum.json` latency config
2. Configure error scenarios: Add error mocks in `./mocks/errors/`
3. Set up response variations: Use `--variation` flag for A/B testing

## Golden Rules

1. **Never Use Production API Keys** - Always use test/sandbox keys for AI providers; set `QUANTUM_AI_API_KEY` from `.env` file never committed to repo

2. **Always Verify Port Availability** - Check `lsof -i :PORT` before starting to avoid conflicts

3. **Use Seed for Deterministic Tests** - Set `QUANTUM_SEED` for reproducible test responses

4. **Keep Mocks in Version Control** - Commit `./mocks/` directory to ensure team consistency

5. **Validate Generated Mocks** - Always test generated endpoints before handing off to frontend

6. **Clean Up On Exit** - Use `quantum-mock stop` to properly shutdown; avoid zombie processes

7. **Separate Environments** - Use different config files per environment: `quantum.dev.json`, `quantum.staging.json`

## Examples

### Example 1: Full Initialization and Serve

```bash
# Initialize project
$ quantum-mock init --name user-service --port 9000
✓ Created quantum.json
✓ Created .quantum/ directory
✓ Created mocks/ directory
✓ Initialization complete

# Start server
$ quantum-mock serve --config ./quantum.json
[INFO] Quantum Mock v2.4.1 starting...
[INFO] Loading config from ./quantum.json
[INFO] Mock server running at http://localhost:9000
[INFO] Endpoints available:
  GET  /api/users
  GET  /api/users/:id
  POST /api/users
  PUT  /api/users/:id
  DELETE /api/users/:id

# Test endpoint
$ curl http://localhost:9000/api/users | jq
{
  "users": [
    {
      "id": "usr_abc123",
      "name": "Alice Johnson",
      "email": "alice@example.com",
      "created_at": "2024-01-15T10:30:00Z"
    },
    {
      "id": "usr_def456",
      "name": "Bob Smith",
      "email": "bob@example.com", 
      "created_at": "2024-01-16T14:22:00Z"
    }
  ],
  "total": 2,
  "page": 1
}
```

### Example 2: Generate from OpenAPI with Error Scenarios

```bash
$ quantum-mock generate \
  --spec ./payment-api.yaml \
  --output ./mocks \
  --strategy dynamic \
  --generate-errors

✓ Parsed OpenAPI spec: 12 endpoints
✓ Generated 12 mock responses
✓ Generated 36 error scenarios
✓ Created mocks/payment-api/
✓ Created mocks/errors/
✓ quantum.json updated

$ ls -la mocks/
payment-api/
errors/
  400_bad_request.json
  401_unauthorized.json
  403_forbidden.json
  404_not_found.json
  422_validation_error.json
  429_rate_limit.json
  500_server_error.json
  503_unavailable.json
```

### Example 3: Natural Language API Generation

```bash
$ quantum-mock generate \
  --prompt "Weather API with current conditions, 7-day forecast, hourly forecast, historical data, user locations, favorites" \
  --lang en \
  --output ./weather-mocks

✓ Analyzing prompt requirements...
✓ Generating endpoints: 15
✓ Creating response schemas...
✓ Generating realistic data...

$ cat weather-mocks/endpoints.json
{
  "endpoints": [
    {
      "method": "GET",
      "path": "/v1/current",
      "description": "Get current weather conditions",
      "response": {
        "temperature": 72,
        "condition": "partly_cloudy",
        "humidity": 65,
        "wind_speed": 12,
        "units": "imperial"
      }
    },
    {
      "method": "GET", 
      "path": "/v1/forecast/daily",
      "description": "7-day weather forecast",
      "response": {
        "days": [
          {"date": "2024-01-20", "high": 75, "low": 62, "condition": "sunny"},
          {"date": "2024-01-21", "high": 71, "low": 58, "condition": "rainy"}
        ]
      }
    }
  ]
}
```

### Example 4: Testing with Simulated Latency

```bash
# Start server with 100-500ms latency and 3% error rate
$ quantum-mock serve \
  --config ./quantum.json \
  --latency 100-500 \
  --error-rate 3

[INFO] Latency simulation: 100-500ms
[INFO] Error injection: 3% chance
[INFO] Server running on http://localhost:8080

# Test with curl to observe latency
$ time curl -s http://localhost:8080/api/users > /dev/null
real    0m0.234s   # ~234ms latency

# Test error injection (repeat until 503 occurs)
$ for i in {1..50}; do curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/api/users; done
200
200
200
503    # Error injected
200
200
```

## Rollback Commands

### Complete Cleanup

```bash
# Stop any running server
quantum-mock stop

# Remove generated mocks (keep config)
rm -rf ./mocks/

# Remove quantum runtime files
rm -rf .quantum/
```

### Reset to Clean State

```bash
# Kill all quantum-mock processes
pkill -f "quantum-mock"

# Remove all generated artifacts
rm -rf .quantum/ mocks/

# Reinitialize
quantum-mock init --force
```

### Revert Config Changes

```bash
# If quantum.json was corrupted
git checkout HEAD -- quantum.json

# Or restore from backup
cp ~/.quantum/backups/quantum.json.20240115 ./quantum.json
```

### Port Conflict Resolution

```bash
# Find process using port
lsof -i :8080

# Kill specific process
kill -9 <PID>

# Or use quantum-mock to kill
quantum-mock stop --force
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Server won't start - port in use | Run `lsof -i :PORT` and kill conflicting process |
| AI generation fails - quota exceeded | Check `QUANTUM_AI_API_KEY` or switch provider with `--provider anthropic` |
| Responses are not realistic | Increase `--complexity` flag or provide more detailed prompt |
| Hot reload not working | Ensure `--watch` flag is set; check file permissions |
| Memory usage high | Reduce `--max-responses` in config; use `--strategy static` |
| CORS errors in browser | Add `--cors` flag or configure in `quantum.json` |
| Invalid OpenAPI spec | Validate with `swagger-cli validate ./api.yaml` before generation |

## Dependencies

- **Node.js**: >= 18.0.0
- **npm**: >= 9.0.0
- **AI Provider**: OpenAI API key or Anthropic API key (optional, for generation only)
- **Optional**: Docker (for containerized deployment)

## Installation

```bash
npm install -g quantum-mock

# Verify installation
quantum-mock --version
# quantum-mock v2.4.1
```

## Configuration File (quantum.json)

```json
{
  "name": "my-mock-service",
  "version": "1.0.0",
  "port": 8080,
  "host": "localhost",
  "ai": {
    "provider": "openai",
    "model": "gpt-4",
    "temperature": 0.7
  },
  "mocks": {
    "path": "./mocks",
    "strategy": "dynamic",
    "cache": true,
    "ttl": 3600
  },
  "simulation": {
    "latency": {
      "min": 50,
      "max": 200
    },
    "errorRate": 0,
    "variations": false
  },
  "cors": {
    "enabled": true,
    "origins": ["*"]
  },
  "logging": {
    "level": "info",
    "pretty": true
  }
}
```

## Verification Steps

1. **After Installation**:
   ```bash
   quantum-mock --version
   quantum-mock doctor
   ```

2. **After Mock Generation**:
   ```bash
   # Verify all endpoints exist
   quantum-mock status --endpoints

   # Test each endpoint responds
   for endpoint in /api/users /api/products /api/orders; do
     curl -sf http://localhost:8080$endpoint > /dev/null && echo "✓ $endpoint" || echo "✗ $endpoint"
   done
   ```

3. **After Server Start**:
   ```bash
   # Health check
   curl http://localhost:8080/_quantum/health
   # {"status":"ok","version":"2.4.1","uptime":42}
   ```
```