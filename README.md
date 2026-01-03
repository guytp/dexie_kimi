# Dexie Platform

The definitive Pokémon card pricing platform for the UK market. Dexie provides real-time, accurate market pricing through intelligent eBay data aggregation and AI analysis, helping collectors, sellers, and buyers make informed decisions.

## What is Dexie?

Dexie is a first-of-its-kind mobile application designed specifically for the UK Pokémon card market. The platform addresses a critical gap in the market by providing:

- **Real-time Pricing**: Instant access to current market prices for any Pokémon card
- **Historical Trends**: Min/max/average pricing over time periods
- **Multi-variant Support**: Holo, reverse holo, normal, cosmofoil, stamped versions
- **Graded Card Pricing**: Prices for cards graded by major labs (1-10 scale)
- **AI-powered Analysis**: Maitento AI pipeline helps decode eBay auction context
- **Mobile Scanning**: Scan cards via camera or search by card number/set/name

### MVP Focus

The initial release focuses on a simple but powerful workflow: scan a card → get instant market pricing with the ability to filter by grading company, grade level, and view recent sales data.

## Tech Stack

| Component | Technology | Version | Purpose |
|-----------|------------|---------|---------|
| **Backend APIs** | .NET | 8.0 | Microservices architecture |
| **Database** | MongoDB | Latest | Flexible document storage for card data |
| **Message Queue** | RabbitMQ | Latest | Event-driven communication between services |
| **Internal Web UI** | React | Latest | Operations dashboard and admin tools |
| **Mobile Apps** | MAUI (.NET) | 8.0 | Cross-platform Android/iOS applications |
| **Container Orchestration** | Kubernetes | Latest | Production deployment and scaling |
| **AI Pipeline** | Maitento | Internal | eBay auction context analysis |
| **Configuration** | Environment Variables | - | No hardcoded secrets or connection strings |

## Repository Structure

This is a **lightweight mono-repo** (`dexie-platform`) containing all components of the Dexie platform. The structure is designed to support extraction to separate repositories in the future if needed.

```
/ (repo root)
├── .github/
│   └── workflows/         # CI/CD pipeline definitions (GitHub Actions)
│
├── backend/               # All backend services and shared libraries
│   ├── libs/             # Shared code used across services
│   │   ├── dexie-domain/  # Shared domain models, value objects, business rules
│   │   │   └── src/Dexie.Shared.Domain/
│   │   ├── dexie-infra/   # Infrastructure: logging, messaging, MongoDB helpers
│   │   │   └── src/Dexie.Shared.Infrastructure/
│   │   └── dexie-testing/ # Common testing utilities and helpers
│   │       └── src/Dexie.Testing/
│   │
│   └── services/         # Individual microservices (each independently buildable)
│       ├── cards-service/     # Pokémon card data management
│       │   └── src/Dexie.Cards.Api/
│       ├── listings-service/  # Marketplace listing aggregation
│       │   └── src/Dexie.Listings.Api/
│       ├── prices-service/    # Price calculation and history
│       │   └── src/Dexie.Prices.Api/
│       └── gateway-api/       # Public API gateway and routing
│           └── src/Dexie.Gateway.Api/
│
├── web/                   # Frontend applications
│   └── internal-ops-ui/   # React internal operations dashboard
│
├── mobile/               # Mobile applications
│   └── dexie-app/        # MAUI cross-platform mobile app (Android/iOS)
│
├── docs/                 # Documentation
│   ├── architecture/     # Architecture decision records (ADRs) and diagrams
│   └── runbooks/        # Operational runbooks and troubleshooting guides
│
└── scripts/              # Development and automation scripts
```

### Backend Microservices

Each microservice is self-contained in its own directory under `/backend/services/`:

- **`cards-service/`** - Manages Pokémon card data, images, and metadata
  - Solution: `CardsService.sln`
  - API Project: `Dexie.Cards.Api`

- **`listings-service/`** - Aggregates marketplace listings from eBay and other sources
  - Solution: `ListingsService.sln`
  - API Project: `Dexie.Listings.Api`

- **`prices-service/`** - Calculates current and historical prices, manages price trends
  - Solution: `PricesService.sln`
  - API Project: `Dexie.Prices.Api`

- **`gateway-api/`** - Public-facing API gateway that routes requests to appropriate services
  - Solution: `GatewayApi.sln`
  - API Project: `Dexie.Gateway.Api`

### Shared Libraries

Located under `/backend/libs/` and referenced by services as needed:

- **`dexie-domain/`** - Contains business domain models, value objects, and shared business rules
  - Library: `Dexie.Shared.Domain`
  - Versioned and maintained independently

- **`dexie-infra/`** - Shared infrastructure concerns: logging, messaging, MongoDB conventions
  - Library: `Dexie.Shared.Infrastructure`
  - References `Dexie.Shared.Domain` for domain model serialization

- **`dexie-testing/`** - Common testing utilities, fixtures, and helpers for unit/integration tests
  - Library: `Dexie.Testing`
  - Includes xUnit, FluentAssertions, and test helpers

### Frontend & Mobile Applications

- **`web/internal-ops-ui/`** - React-based internal operations dashboard for Dexie team
  - Package: `@dexie/internal-ops-ui`
  - (React implementation to follow in future stories)

- **`mobile/dexie-app/`** - MAUI (.NET Multi-platform App UI) mobile application
  - Target: Android and iOS
  - Features: Card scanning, price lookup, collection management
  - (MAUI implementation to follow in future stories)

## Quickstart

Follow these steps to get started with the Dexie platform codebase.

### Prerequisites

- **.NET 8.0 SDK** - [Download from Microsoft](https://dotnet.microsoft.com/download/dotnet/8.0)
- **Git** - For version control
- **MongoDB** (optional) - For local development, or use Docker
- **RabbitMQ** (optional) - For local event processing, or use Docker
- **Node.js** (future) - For React UI development
- **MAUI workloads** (future) - For mobile development

### Clone the Repository

```bash
git clone https://github.com/your-org/dexie-platform.git
cd dexie-platform
```

### Build a Specific Microservice

Each microservice can be built independently:

```bash
# Build the cards microservice
dotnet build backend/services/cards-service/CardsService.sln

# Build the prices microservice
dotnet build backend/services/prices-service/PricesService.sln

# Build the API gateway
dotnet build backend/services/gateway-api/GatewayApi.sln
```

### Build Shared Libraries

```bash
# Build domain models library
dotnet build backend/libs/dexie-domain/Dexie.Shared.Domain.sln

# Build infrastructure library
dotnet build backend/libs/dexie-infra/Dexie.Shared.Infrastructure.sln

# Build testing utilities
dotnet build backend/libs/dexie-testing/Dexie.Testing.sln
```

### Run a Service Locally

```bash
# Run cards service (default: http://localhost:5001)
cd backend/services/cards-service/src/Dexie.Cards.Api
dotnet run

# In another terminal, test the health endpoint
curl http://localhost:5001/health
```

Expected response:
```json
{"status":"healthy","service":"cards"}
```

### Open in Your IDE

**Using Visual Studio Code:**
```bash
# Open cards service
code backend/services/cards-service/CardsService.sln

# Open all backend services
code backend/
```

**Using Visual Studio:**
1. Double-click any `.sln` file
2. Or use File → Open → Solution in Visual Studio

**Using JetBrains Rider:**
```bash
# Open from command line
rider backend/services/cards-service/CardsService.sln
```

### Environment Configuration

Dexie services use environment variables for configuration. Set these before running services:

**Linux/macOS:**
```bash
export MONGODB_CONNECTION_STRING="mongodb://localhost:27017/dexie"
export RABBITMQ_HOST="localhost"
export RABBITMQ_PORT="5672"
export RABBITMQ_USERNAME="guest"
export RABBITMQ_PASSWORD="guest"
```

**Windows (PowerShell):**
```powershell
$env:MONGODB_CONNECTION_STRING="mongodb://localhost:27017/dexie"
$env:RABBITMQ_HOST="localhost"
$env:RABBITMQ_USERNAME="guest"
$env:RABBITMQ_PASSWORD="guest"
```

**Docker Compose (Recommended for Local Development):**

```yaml
# docker-compose.yml (example)
version: '3.8'
services:
  mongodb:
    image: mongo:latest
    ports:
      - "27017:27017"

  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"
      - "15672:15672"
```

### Verify Service Health

All services expose a `/health` endpoint:

```bash
# Check cards service
curl http://localhost:5001/health

# Check listings service (runs on separate port)
curl http://localhost:5002/health

# Check prices service
curl http://localhost:5003/health

# Check gateway API
curl http://localhost:5000/health
```

### Build Everything

To build the entire backend:

```bash
dotnet build backend/libs/dexie-domain/Dexie.Shared.Domain.sln
dotnet build backend/libs/dexie-infra/Dexie.Shared.Infrastructure.sln
dotnet build backend/services/cards-service/CardsService.sln
dotnet build backend/services/listings-service/ListingsService.sln
dotnet build backend/services/prices-service/PricesService.sln
dotnet build backend/services/gateway-api/GatewayApi.sln
```

Or use the build script (future):
```bash
./scripts/build-all.sh
```

## Next Steps

- 📖 Read the [Contributing Guide](CONTRIBUTING.md) for development workflow
- 📚 Explore [Architecture Documentation](docs/architecture/) for system design
- 🔧 Check out [Development Scripts](scripts/) for automation tools
- 🚀 See our [CI/CD Pipeline](.github/workflows/) for deployment process
- 🏃 Run the full application suite locally with Docker Compose

## Future Enhancements

This is the foundation of the Dexie platform. Planned enhancements include:

- **Full CI/CD Pipeline** - Automated testing, building, and deployment
- **React Web UI** - Complete internal operations dashboard
- **MAUI Mobile App** - iOS and Android applications
- **Comprehensive Test Suite** - Unit, integration, and end-to-end tests
- **Production Deployment Guides** - Kubernetes manifests and cloud deployment
- **Monitoring & Observability** - Logging, metrics, and tracing implementation
- **API Documentation Portal** - Interactive Swagger UI and developer portal
- **Data Scraping Pipeline** - Automated eBay data collection and processing
- **AI Model Integration** - Enhanced context analysis with Maitento AI

## Support & Documentation

- **Architecture Decisions**: [docs/architecture/](docs/architecture/)
- **Operational Runbooks**: [docs/runbooks/](docs/runbooks/)
- **Contributing Guidelines**: [CONTRIBUTING.md](CONTRIBUTING.md)
- **API Documentation**: Will be available at `/swagger` when services are running
- **Issue Tracker**: GitHub Issues (link to be added)

## License

[License information to be added in future stories]

---

**Dexie Platform** - Made with ❤️ for the UK Pokémon community
