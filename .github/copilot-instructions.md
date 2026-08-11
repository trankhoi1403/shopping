# Copilot Instructions for Shopping API

## Project Overview

This is an ASP.NET Core 8 Web API project built with controllers-based architecture. The API is containerized with Docker and exposes a Swagger/OpenAPI interface for documentation.

## Build, Test, and Run

### Build the project
```bash
dotnet build
```

### Run the API locally
From the `api/` directory:
```bash
dotnet run
```
The API runs on `https://localhost:7263` with Swagger UI available at `/swagger/ui/index.html`.

### Build Docker image
```bash
docker build -t shopping-api .
```

### Run Docker container
```bash
docker run -p 8080:8080 -p 8081:8081 shopping-api
```

## Architecture

The project follows ASP.NET Core conventions:
- **Controllers**: REST API endpoints are defined in `Controllers/` using controller classes with `[ApiController]` attribute
- **Routing**: Routes are configured via `[Route]` attributes on controllers (e.g., `[Route("[controller]")]` maps to `/weatherforecast`)
- **Configuration**: App settings are managed in `appsettings.json` (production) and `appsettings.Development.json` (development)
- **Dependency Injection**: Built-in ASP.NET Core DI container via constructor injection
- **Logging**: ILogger<T> is injected for structured logging

### Key Files
- **Program.cs**: Application entry point, service registration, middleware pipeline configuration
- **Controllers/**: API endpoint implementations inheriting from ControllerBase
- **appsettings.json**: Configuration for all environments

## Key Conventions

### C# and .NET Features
- **Target Framework**: .NET 8 with nullable reference types enabled (`<Nullable>enable</Nullable>`)
- **Implicit Usings**: Enabled for cleaner namespaces
- **Modern C# Syntax**: Uses nullable types, init-only properties, and other modern patterns

### API Controller Patterns
- Controllers inherit from `ControllerBase` (not `Controller`, which adds view support)
- HTTP methods use attributes: `[HttpGet]`, `[HttpPost]`, `[HttpPut]`, `[HttpDelete]`
- Route parameters go in method parameters; ASP.NET Core binding is automatic
- Constructors use dependency injection for services (ILogger, custom services, etc.)

### Namespace Structure
Follow the convention: `api.<feature>` (e.g., `api.Controllers`, `api.Services`)

### Configuration
- Use `appsettings.{Environment}.json` for environment-specific settings
- Access configuration via `IConfiguration` injected into services or Program.cs
- User secrets (for development) use the User Secrets Manager with ID `b2bb8006-9fed-4e2a-bed8-e06e908bec88`

### Swagger/OpenAPI
- Enabled in development environments via middleware
- API documentation is auto-generated from controller methods and XML comments
- Add `[ProducesResponseType]` attributes to document expected responses
- Add `[Produces("application/json")]` or similar for content type documentation

## Common Tasks

### Adding a new API endpoint
1. Create a method in a controller class
2. Decorate with the appropriate HTTP method attribute (`[HttpGet]`, `[HttpPost]`, etc.)
3. Use `[Route(...)]` to define the path if needed
4. Inject any required services via constructor
5. Add XML documentation comments for Swagger documentation

### Running in development vs. production
- Development: `dotnet run` activates development configuration, enables Swagger UI
- Production: Swagger is disabled, HTTPS redirection is enforced

### Adding dependencies
Update `api.csproj` with new `<PackageReference>` entries, then run `dotnet restore`.
