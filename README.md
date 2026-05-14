# CloudNativeInventory

Detta projekt är en containeriserad .NET 9-applikation byggd för en modern molnmiljö. Projektet demonstrerar implementering av CI/CD-pipelines, säker hantering av hemligheter via Azure Key Vault och optimerad containersäkerhet.

## Azure-tjänster som används

Följande Azure-tjänster utgör infrastrukturen för lösningen:

* **Azure Container Apps:** Används som compute-plattform för att köra API:et med automatisk skalning och serverless-kapacitet.
* **Azure Container Registry (ACR):** Hanterar och lagrar Docker-images.
* **Azure Key Vault:** Centraliserad och säker lagring av API-nycklar och hemligheter.
* **Managed Identity (User-Assigned):** Möjliggör lösenordslös autentisering mellan Container App och Key Vault.
* **GitHub Actions:** Driver hela CI/CD-flödet för automatisering av tester och driftsättning.

## Köra API:t lokalt

För att köra projektet lokalt utan att riskera att checka in hemligheter används .NET User Secrets eller miljövariabler.

### Förutsättningar

* Docker Desktop (för körning via Docker Compose)
* .NET 9 SDK

### Alternativ 1: Docker Compose

Använd medföljande `docker-compose.yml`. Inga hemligheter är hårdkodade i filen; de läses in via ditt skals miljövariabler.

1. Sätt miljövariabler i din terminal:
* `export SQL_PASSWORD=DittLösenord123!`
* `export VENDOR_API_KEY=LOCAL_DEV_SECRET_12345_DO_NOT_DEPLOY`


2. Kör: `docker-compose up --build`

### Alternativ 2: .NET CLI

1. Navigera till `CloudNativeInventory.Api/`.
2. Initiera och lägg till hemligheter lokalt:
* `dotnet user-secrets set "ExternalServices:VendorApiKey" "din_lokala_nyckel"`


3. Kör applikationen: `dotnet run`

## Pipeline-struktur

Pipelinen finns definierad i `.github/workflows/pipeline.yml` och är uppdelad i två huvudjobb:

### 1. Kvalitetskontroll (CI)

* **Trigger:** Sker vid varje `push` eller `pull_request` mot `master`-branschen.
* **Steg:**
* Checkout av koden.
* Setup av .NET 9-miljö med caching för snabbare körningar.
* `dotnet restore` och `dotnet build` i Release-läge.
* `dotnet test` kör alla enhetstester. Om testerna misslyckas avbryts pipelinen.



### 2. Driftsättning (CD)

* **Trigger:** Sker endast vid lyckad CI och enbart vid push direkt till `master`.
* **Steg:**
* Inloggning mot Azure med GitHub Secrets (`AZURE_CREDENTIALS`).
* Bygge av Docker-image med `github.sha` för full spårbarhet.
* Push av imagen till Azure Container Registry.
* Deployment till Azure Container Apps med den nyskapade imagen.



## Deploy och Verifiering

### Deploy

Driftsättning sker helt automatiserat via GitHub Actions när kod mergas in i `master`. Pipelinen taggar varje release unikt för att möjliggöra snabb rollback om det behövs.

### Verifiering

Verifiering av att applikationen körs korrekt och har kontakt med sina integrationer (t.ex. Key Vault) görs via health-check-endpointen:

**Endpoint:** `/health`

* **Healthy (200 OK):** Indikerar att applikationen har laddat en giltig och säker API-nyckel från Key Vault (inte lokal default).
* **Unhealthy (503 Service Unavailable):** Indikerar att nyckeln saknas eller att applikationen fortfarande använder den osäkra standardnyckeln `LOCAL_DEV_SECRET_12345_DO_NOT_DEPLOY`.

Verifieringen utförs automatiskt i `Program.cs` genom en anpassad `HealthCheck`.

## Arkitekturbeslut (ADR)

Besluten bakom lösningen finns dokumenterade i mappen `CloudNativeInventory.Api/Docs/Adr/`. Här motiveras val kring infrastruktur, säkerhet och automatisering.

* [ADR 0001: Val av Azure Compute (Container Apps)](https://www.google.com/search?q=CloudNativeInventory.Api/Docs/Adr/0001-val-av-azure-compute.md) - Motivering kring skalbarhet och kostnad.
* [ADR 0002: Säker konfigurationshantering](https://www.google.com/search?q=CloudNativeInventory.Api/Docs/Adr/0002-saker-konfigurationshantering.md) - Hur vi eliminerar hemligheter i kod med Key Vault och Managed Identity.
* [ADR 0003: Automatisering av deploy (CI/CD)]() - Strategi för att minska mänskliga fel.
* [ADR 0004: Container-härdning (Chiseled Images)]() - Hur vi minskar attackytan genom att köra rootless och minimala images.