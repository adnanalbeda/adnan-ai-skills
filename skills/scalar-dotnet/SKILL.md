---
name: scalar-dotnet
description: Scalar.AspNetCore workflow for ASP.NET Core OpenAPI reference UI setup, configuration, migration, and troubleshooting. Use when working with Scalar, MapScalarApiReference, OpenAPI routes, API reference UI, or Swagger UI replacement in .NET projects.
---

# Scalar .NET

Use this skill for Scalar API references in ASP.NET Core. Default to the installed `Scalar.AspNetCore` version; when exact API behavior matters, inspect package references and prefer official docs.

## Official Sources

- ASP.NET Core integration: https://scalar.com/products/api-references/integrations/aspnetcore/integration
- API reference configuration: https://scalar.com/products/api-references/configuration
- Scalar GitHub: https://github.com/scalar/scalar
- `ScalarOptions`: https://github.com/scalar/scalar/blob/main/integrations/dotnet/aspnetcore/src/Scalar.AspNetCore/Options/ScalarOptions.cs
- `ScalarOptionsExtensions`: https://github.com/scalar/scalar/blob/main/integrations/dotnet/aspnetcore/src/Scalar.AspNetCore/Extensions/ScalarOptionsExtensions.cs

## Quick Start

```powershell
dotnet add package Scalar.AspNetCore
```

```csharp
using Scalar.AspNetCore;
```

For `Microsoft.AspNetCore.OpenApi`:

```csharp
builder.Services.AddOpenApi();

if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
    app.MapScalarApiReference();
}
```

For Swashbuckle, expose OpenAPI where Scalar expects it:

```csharp
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

if (app.Environment.IsDevelopment())
{
    app.MapSwagger("/openapi/{documentName}.json");
    app.MapScalarApiReference();
}
```

For NSwag, use `builder.Services.AddOpenApiDocument()`, set `app.UseOpenApi(options => options.Path = "/openapi/{documentName}.json")`, then call `app.MapScalarApiReference()`. Default UI route is `/scalar`.

## Configuration

Use `app.MapScalarApiReference("/docs", options => { ... })` for custom route and fluent options like `.WithTitle(...)`, `.WithOpenApiRoutePattern(...)`, `.WithClassicLayout()`, `.ForceDarkMode()`, `.ShowOperationId()`, and `.AddServer(...)`.

Use `AddDocument` or `AddDocuments` for versioned or multi-document APIs:

```csharp
app.MapScalarApiReference(options =>
{
    options.AddDocument("v1", "Production API", "api/v1/openapi.json")
           .AddDocument("v2-beta", "Beta API", "api/v2-beta/openapi.json", isDefault: true);
});
```

Keep document name casing consistent. Scalar forwards names exactly as written, and generator behavior can be case-sensitive.

## Authentication

Scalar can prefill API key, HTTP, bearer, basic, and OAuth2 authentication values, but the OpenAPI document must already define matching security schemes. Typical bearer setup: `.AddPreferredSecuritySchemes("BearerAuth").AddHttpAuthentication("BearerAuth", auth => auth.Token = "dev-token")`.

Never prefill production secrets. Browser-visible auth defaults are for development and testing only. Use `EnablePersistentAuthentication()` cautiously because values persist in browser storage.

## Useful Options

- `.WithAgentKey(...)` enables Scalar Agent in production; `.DisableAgent()` turns it off.
- `.WithDefaultHttpClient(ScalarTarget.CSharp, ScalarClient.HttpClient)` chooses default code samples.
- `.WithBundleUrl(...)` changes bundle location; `.DisableDefaultFonts()` avoids default CDN fonts.
- `.WithJavaScriptConfiguration("/scalar/config.js")` loads custom JS; expose the module via static files.
- `builder.Services.Configure<ScalarOptions>(...)` sets defaults; endpoint-level options override DI options.
- `app.MapScalarApiReference()` returns `IEndpointConventionBuilder`, so `.AllowAnonymous()` and other endpoint conventions can be chained.

## Design Checks

- Which OpenAPI generator is active: Microsoft, Swashbuckle, NSwag, or FastEndpoints?
- What exact OpenAPI JSON route should Scalar read?
- Should Scalar be development-only, public, authenticated, or behind authorization?
- Is the API single-document or multi-document/versioned?
- Are auth schemes defined in OpenAPI before configuring Scalar auth defaults?

## Common Pitfalls

- Do not map Scalar without exposing the OpenAPI document route it reads.
- Do not assume Swagger UI routes and Scalar route patterns are identical during migration.
- Do not prefill production credentials or client secrets into Scalar options.
- Do not enable persistent auth without accepting browser storage risk, and do not forget `using Scalar.AspNetCore;` when `MapScalarApiReference` is not found.
