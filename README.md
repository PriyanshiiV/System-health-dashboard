# System Health Dashboard API

A small Flask API that provides health, version, and environment information. It was built as a Git and DevOps mini project to demonstrate testing, Docker containerisation, and a Jenkins CI pipeline.

## What this project does

The API has three endpoints:

| Endpoint | Purpose | Example response |
| --- | --- | --- |
| `/health` | Confirms that the application is running | `{"status":"UP"}` |
| `/version` | Shows the application version | `{"version":"1.0.0"}` |
| `/environment` | Shows the value of the `APP_ENV` environment variable | `{"environment":"development"}` |

## Project structure

```text
Mini_Project/
|-- app.py                 # Flask application and API endpoints
|-- requirements.txt       # Python dependencies
|-- tests/
|   `-- test_app.py        # Automated endpoint tests
|-- Dockerfile             # Application container image definition
|-- Dockerfile.jenkins     # Optional custom Jenkins image definition
|-- Jenkinsfile            # Jenkins pipeline stages
|-- .gitignore             # Files Git should not commit
`-- .dockerignore          # Files Docker should not copy into the image
```

## Prerequisites

- Python 3.11 or later
- Docker Desktop (only required for the Docker section)
- Jenkins with Docker access (only required for the Jenkins section)

## Run locally (Windows PowerShell)

1. Open PowerShell in the project folder:

   ```powershell
   cd "D:\Mthree Training\Mini_Project"
   ```

2. Create a virtual environment the first time you set up the project:

   ```powershell
   python -m venv .venv
   ```

3. Activate it:

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

   If PowerShell blocks this command, run the following once for the current terminal and try again:

   ```powershell
   Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
   ```

4. Install dependencies:

   ```powershell
   python -m pip install -r requirements.txt
   ```

5. Start the API:

   ```powershell
   python app.py
   ```

The application runs at `http://localhost:5000`. Stop it with `Ctrl + C`.

## Verify the API

With the app running, open these URLs in a browser:

- `http://localhost:5000/health`
- `http://localhost:5000/version`
- `http://localhost:5000/environment`

The environment endpoint returns `development` unless `APP_ENV` is set.

To run the app with a different environment in PowerShell:

```powershell
$env:APP_ENV = "testing"
python app.py
```

Then `/environment` returns:

```json
{"environment":"testing"}
```

## Run automated tests

From the project folder, with the virtual environment active:

```powershell
python -m pytest -v
```

The suite checks all three endpoints. A non-zero exit code means at least one test failed; Jenkins uses this to stop the pipeline before a faulty image is built.

## Build and run with Docker

1. Build a versioned image:

   ```powershell
   docker build -t system-health-dashboard:1.0.0 .
   ```

2. Run it and supply the environment as a runtime variable:

   ```powershell
   docker run --rm -p 5000:5000 -e APP_ENV=production system-health-dashboard:1.0.0
   ```

3. Verify `/health`, `/version`, and `/environment` at `http://localhost:5000`.

Use `Ctrl + C` to stop the container. The `--rm` option removes the stopped container automatically.

## Jenkins pipeline

The `Jenkinsfile` defines these stages:

```text
Checkout -> Install -> Test -> Build -> Tag -> Health check
```

- **Install** installs Python dependencies.
- **Test** runs `pytest -v`; a failure stops the pipeline.
- **Build** creates the Docker image.
- **Tag** adds Jenkins' unique `BUILD_NUMBER`, for example `system-health-dashboard:12`.
- **Health check** starts a temporary container, calls `/health`, then removes that container.

Before running this pipeline, ensure the Jenkins agent can run `python3`, `pip3`, `pytest`, `docker`, and `curl`.

## Git workflow used

The project uses:

```text
main -> develop -> feature/api-endpoints and feature/tests
```

Feature work was completed in focused branches and merged into `develop`. A README merge conflict was resolved and recorded in the commit history. The intended final workflow is a reviewed pull request from `develop` to `main`.

## Possible improvements

- Add a production WSGI server such as Gunicorn.
- Add API versioning and structured logging.
- Add Jenkins checkout configuration and publish Docker images to a registry.
- Add pipeline screenshots and pull-request review evidence to the repository documentation.
