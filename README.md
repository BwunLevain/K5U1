# CloudNativeInventory

This project is a containerized .NET 9 application built for a modern cloud environment. The project demonstrates the implementation of CI/CD pipelines, secure secret management via Azure Key Vault, and optimized container security.

## Azure Services Used

The following Azure services constitute the infrastructure for this solution:

* **Azure Container Apps:** Used as the compute platform to run the API with automatic scaling and serverless capabilities.
* **Azure Container Registry (ACR):** Manages and stores Docker images.
* **Azure Key Vault:** Centralized and secure storage for API keys and secrets.
* **Managed Identity (User-Assigned):** Enables passwordless authentication between the Container App and the Key Vault.
* **GitHub Actions:** Drives the entire CI/CD flow for automated testing and deployment.

## Running the API Locally

To run the project locally without the risk of committing secrets, use .NET User Secrets or environment variables.

### Prerequisites

* Docker Desktop (for running via Docker Compose)
* .NET 9 SDK

### Option 1: Docker Compose

Use the provided `docker-compose.yml`. No secrets are hardcoded in the file; they are loaded via your shell's environment variables.

1. Set environment variables in your terminal:
* `export SQL_PASSWORD=YourPassword123!`
* `export VENDOR_API_KEY=LOCAL_DEV_SECRET_12345_DO_NOT_DEPLOY`


2. Run: `docker-compose up --build`

### Option 2: .NET CLI

1. Navigate to `CloudNativeInventory.Api/`.
2. Initialize and add secrets locally:
* `dotnet user-secrets set "ExternalServices:VendorApiKey" "your_local_key"`


3. Run the application: `dotnet run`

## Pipeline Structure

The pipeline is defined in `.github/workflows/pipeline.yml` and is divided into two main jobs:

### 1. Quality Control (CI)

* **Trigger:** Occurs on every `push` or `pull_request` to the `master` branch.
* **Steps:**
* Checkout code.
* Setup .NET 9 environment with caching for faster execution.
* `dotnet restore` and `dotnet build` in Release mode.
* `dotnet test` runs all unit tests. If tests fail, the pipeline is aborted.



### 2. Deployment (CD)

* **Trigger:** Occurs only upon successful CI and only for pushes directly to `master`.
* **Steps:**
* Azure login using GitHub Secrets (`AZURE_CREDENTIALS`).
* Build Docker image using `github.sha` for full traceability.
* Push the image to Azure Container Registry.
* Deploy to Azure Container Apps using the newly created image.



## Deployment and Verification

### Deployment

Deployment is fully automated via GitHub Actions when code is merged into `master`. The pipeline tags each release uniquely to enable quick rollbacks if necessary.

### Verification

Verification that the application is running correctly and has contact with its integrations (e.g., Key Vault) is done via the health check endpoint:

**Endpoint:** `/health`

* **Healthy (200 OK):** Indicates that the application has loaded a valid and secure API key from Key Vault (not the local default).
* **Unhealthy (503 Service Unavailable):** Indicates that the key is missing or that the application is still using the insecure default key `LOCAL_DEV_SECRET_12345_DO_NOT_DEPLOY`.

The verification is performed automatically in `Program.cs` through a custom `HealthCheck`.

## Architecture Decision Records (ADR)

The decisions behind the solution are documented in the `CloudNativeInventory.Api/Docs/Adr/` folder. These justify choices regarding infrastructure, security, and automation.

* [ADR 0001: Selection of Azure Compute (Container Apps)](https://www.google.com/search?q=CloudNativeInventory.Api/Docs/Adr/0001-val-av-azure-compute.md) - Rationale regarding scalability and cost.
* [ADR 0002: Secure Configuration Management]() - How we eliminate secrets in code using Key Vault and Managed Identity.
* [ADR 0003: Automation of Deployment (CI/CD)]() - Strategy to reduce human error.
* [ADR 0004: Container Hardening (Chiseled Images)]() - How we reduce the attack surface by running rootless and using minimal images.
