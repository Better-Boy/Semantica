# MindsDB REST API

A RESTful API built with FastAPI for interacting with MindsDB, managed with UV package manager.

## Features

- ✅ **FastAPI Framework**: Modern, fast web framework for building APIs
- ✅ **UV Package Manager**: Fast, reliable Python package management
- ✅ **YAML Configuration**: Easy configuration management
- ✅ **MindsDB Integration**: Separate module for MindsDB interactions
- ✅ **Warmup Script**: Validate all components before deployment
- ✅ **Environment Variables**: Support for .env files
- ✅ **Comprehensive Logging**: Built-in logging system
- ✅ **Auto-generated Documentation**: Interactive API docs with Swagger UI

## Project Structure

```
rest-api-project/
├── api/                    # API routes and endpoints
│   ├── __init__.py
│   └── routes.py
├── config/                 # Configuration management
│   ├── __init__.py
│   ├── config.yaml        # Main configuration file
│   └── loader.py          # YAML config loader
├── mindsdb/               # MindsDB integration module
│   ├── __init__.py
│   └── client.py          # MindsDB client wrapper
├── main.py                # FastAPI application entry point
├── warmup.py              # Warmup/validation script
├── env.example            # Environment variables template
├── pyproject.toml         # UV project configuration
└── README.md              # This file
```

## Prerequisites

- Python 3.12+ (managed by UV)
- UV package manager ([installation instructions](https://github.com/astral-sh/uv))
- MindsDB account (Cloud or local instance)

## Installation

1. **Clone/Navigate to the project directory**:
   ```bash
   cd rest-api-project
   ```

2. **Install dependencies** (UV will automatically create a virtual environment):
   ```bash
   uv sync
   ```

3. **Configure environment variables**:
   - Copy `env.example` to `.env`:
     ```bash
     cp env.example .env
     ```
   - Edit `.env` with your MindsDB credentials:
     ```env
     MINDSDB_EMAIL=your-email@example.com
     MINDSDB_PASSWORD=your-password
     MINDSDB_URL=https://cloud.mindsdb.com
     ```

4. **Run the warmup script** to validate everything is set up correctly:
   ```bash
   uv run python warmup.py
   ```

## Configuration

### YAML Configuration (`config/config.yaml`)

```yaml
app:
  name: "REST API with MindsDB"
  version: "1.0.0"
  host: "0.0.0.0"
  port: 8000
  debug: true

mindsdb:
  url: "https://cloud.mindsdb.com"
  email: ""  # Set in .env file
  password: ""  # Set in .env file

api:
  prefix: "/api/v1"
  title: "MindsDB REST API"
  description: "REST API for interacting with MindsDB"
```

### Environment Variables

Environment variables override YAML configuration:

- `MINDSDB_EMAIL` - MindsDB account email
- `MINDSDB_PASSWORD` - MindsDB account password
- `MINDSDB_URL` - MindsDB server URL
- `APP_HOST` - API server host (default: 0.0.0.0)
- `APP_PORT` - API server port (default: 8000)

## Usage

### Start the API Server

**Option 1: Using the main script**
```bash
uv run python main.py
```

**Option 2: Using uvicorn directly**
```bash
uv run uvicorn main:app --reload
```

**Option 3: Using uvicorn with custom host/port**
```bash
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

### Access the API

Once running, you can access:

- **API Root**: http://localhost:8000/
- **Interactive API Docs (Swagger)**: http://localhost:8000/docs
- **ReDoc Documentation**: http://localhost:8000/redoc
- **OpenAPI Schema**: http://localhost:8000/openapi.json

## API Endpoints

### Health Check
```http
GET /api/v1/health
```
Check API and MindsDB connection status.

**Response:**
```json
{
  "status": "healthy",
  "connected": true,
  "message": "MindsDB client is ready"
}
```

### List Databases
```http
GET /api/v1/databases
```
List all available databases in MindsDB.

**Response:**
```json
{
  "databases": ["mindsdb", "files", "information_schema"]
}
```

### List Models
```http
GET /api/v1/models?database=mindsdb
```
List all models in a specific database.

**Response:**
```json
{
  "models": [
    {
      "name": "my_model",
      "version": "1",
      "status": "complete"
    }
  ]
}
```

### Execute Query
```http
POST /api/v1/query
Content-Type: application/json

{
  "sql": "SELECT * FROM mindsdb.models"
}
```
Execute a SQL query on MindsDB.

### Make Prediction
```http
POST /api/v1/predict
Content-Type: application/json

{
  "model_name": "my_model",
  "database": "mindsdb",
  "data": {
    "feature1": "value1",
    "feature2": "value2"
  }
}
```
Make a prediction using a MindsDB model.

## MindsDB Module

The `mindsdb/` directory contains a separate module for interacting with MindsDB:

### MindsDBClient Class

```python
from mindsdb import get_mindsdb_client

# Get client instance
client = get_mindsdb_client()

# Connect to MindsDB
client.connect()

# List databases
databases = client.list_databases()

# List models
models = client.list_models("mindsdb")

# Execute query
result = client.query("SELECT * FROM mindsdb.models")

# Make prediction
prediction = client.predict(
    model_name="my_model",
    data={"feature1": "value1"}
)

# Disconnect
client.disconnect()
```

## Warmup Script

The `warmup.py` script validates all components before running the API:

```bash
uv run python warmup.py
```

**What it checks:**
1. ✅ Directory structure
2. ✅ Dependencies installation
3. ✅ Configuration loading
4. ✅ API routes
5. ✅ MindsDB connection

**Example output:**
```
╔==========================================================╗
║               WARMUP SCRIPT                              ║
║          Testing all components...                       ║
╚==========================================================╝

============================================================
1. Checking configuration...
============================================================
✓ Configuration loaded successfully
  - App Name: REST API with MindsDB
  - App Version: 1.0.0
  - Host: 0.0.0.0
  - Port: 8000

...

============================================================
SUMMARY
============================================================
✓ PASS   - Directory Structure
✓ PASS   - Dependencies
✓ PASS   - Configuration
✓ PASS   - API Routes
✓ PASS   - MindsDB Connection
============================================================

✓ All checks passed! The application is ready to run.
```

## Development

### Adding New Endpoints

1. Add route functions in `api/routes.py`
2. Define request/response models using Pydantic
3. Use dependency injection for the MindsDB client

Example:
```python
from fastapi import APIRouter
from pydantic import BaseModel

router = APIRouter()

class MyRequest(BaseModel):
    field: str

@router.post("/my-endpoint")
async def my_endpoint(request: MyRequest):
    client = get_mindsdb_client()
    # Your logic here
    return {"result": "success"}
```

### Running Tests

```bash
# Install test dependencies
uv add --dev pytest httpx

# Run tests
uv run pytest
```

### Code Formatting

```bash
# Install formatting tools
uv add --dev black ruff

# Format code
uv run black .
uv run ruff check .
```

## Troubleshooting

### Connection Issues

If you get connection errors:

1. **Check MindsDB credentials** in `.env` file
2. **Verify MindsDB URL** (cloud vs local)
3. **Run warmup script** to diagnose: `uv run python warmup.py`

### Import Errors

If you encounter import errors:

1. **Ensure virtual environment is activated**:
   ```bash
   uv sync
   ```
2. **Check all dependencies are installed**:
   ```bash
   uv pip list
   ```

### Port Already in Use

If port 8000 is already in use:

1. **Change port in config.yaml** or
2. **Set APP_PORT environment variable** or
3. **Specify port when running**:
   ```bash
   uv run uvicorn main:app --port 8001
   ```

## License

MIT License - feel free to use this project as a template for your own APIs.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Support

For issues related to:
- **MindsDB**: Visit [MindsDB Documentation](https://docs.mindsdb.com/)
- **FastAPI**: Visit [FastAPI Documentation](https://fastapi.tiangolo.com/)
- **UV**: Visit [UV Documentation](https://github.com/astral-sh/uv)
