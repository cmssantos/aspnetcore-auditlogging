# Cms.AspNetCore.AuditLogging

[![NuGet](https://img.shields.io/nuget/v/Cms.AspNetCore.AuditLogging.svg)](https://www.nuget.org/packages/Cms.AspNetCore.AuditLogging)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Cms.AspNetCore.AuditLogging is a lightweight, flexible, and dependency-injected audit logging library for ASP.NET Core applications. It allows you to log important user actions (e.g., create, update, delete) and track the responsible user, IP address, timestamps, and additional contextual data.

## Features

- ✅ Easy integration into ASP.NET Core apps
- ✅ Logs audit events with `UserId`, `IP Address`, `Action`, `Timestamp`, etc.
- ✅ Supports automatic and explicit audit registration
- ✅ Stores events using Entity Framework Core or any custom sink
- ✅ Highly customizable and extensible
- ✅ SOLID and clean architecture design

## Installation

```bash
dotnet add package Cms.AspNetCore.AuditLogging
```

## Getting Started

### 1. Configure Services

```csharp
builder.Services.AddAuditLogging(options =>
{
    options.StoreBehavior = AuditStoreBehavior.Auto; // or Manual
});
```

### 2. Use Audit Logging

```csharp
public class ProductService
{
    private readonly IAuditLogger _auditLogger;

    public ProductService(IAuditLogger auditLogger)
    {
        _auditLogger = auditLogger;
    }

    public async Task DeleteAsync(Guid id)
    {
        // Your delete logic...
        await _auditLogger.LogAsync("Product", "Delete", id.ToString());
    }
}
```

### 3. Persist Audit Logs with EF Core (optional)

Create your `AuditDbContext` and model:

```csharp
public class AuditDbContext : DbContext
{
    public DbSet<AuditLog> AuditLogs { get; set; }

    public AuditDbContext(DbContextOptions<AuditDbContext> options) : base(options) { }
}
```

Then register:

```csharp
builder.Services.AddAuditStoreWithEf<AuditDbContext>();
```

## Configuration

| Option               | Description                                      | Default      |
|----------------------|--------------------------------------------------|--------------|
| `StoreBehavior`      | Auto or Manual audit logging                     | `Auto`       |
| `ApplicationName`    | App identifier in logs                          | `null`       |
| `IncludeRequestBody` | Whether to include request body in the audit     | `false`      |

## Models

```csharp
public class AuditLog
{
    public string UserId { get; set; }
    public string IpAddress { get; set; }
    public string Action { get; set; }
    public string Entity { get; set; }
    public string EntityId { get; set; }
    public string ApplicationName { get; set; }
    public DateTime Timestamp { get; set; }
    public string? Data { get; set; }
}
```

## Advanced

- Implement `IAuditStore` for custom persistence (e.g., NoSQL, Queue)
- Use `IUserContext` to customize how user info is retrieved

## License

This project is licensed under the [MIT License](LICENSE).