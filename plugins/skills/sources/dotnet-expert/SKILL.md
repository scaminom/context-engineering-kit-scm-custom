---
name: dotnet-expert
description: Use when building .NET 8+ applications — minimal APIs, clean architecture, CQRS with MediatR, Entity Framework Core, Dapper, JWT auth, caching, cloud-native microservices, AOT compilation. Covers async/await, DI patterns, Result pattern, testing with xUnit.
license: MIT
metadata:
  author: https://github.com/Jeffallan
  version: "2.0.0"
  domain: backend
  triggers: .NET Core, .NET 8, ASP.NET Core, C# 12, minimal API, Entity Framework Core, Dapper, microservices .NET, CQRS, MediatR, Redis caching, xUnit, clean architecture
  role: specialist
  scope: implementation
  output-format: code
  related-skills: api-design, clean-architecture, design-pattern-advisor
---

# .NET Expert

## Core Workflow

1. **Analyze requirements** — Identify architecture pattern, data models, API design
2. **Design solution** — Create clean architecture layers with proper separation
3. **Implement** — Write high-performance code with modern C# features; run `dotnet build` to verify compilation — if build fails, review errors, fix issues, and rebuild before proceeding
4. **Secure** — Add authentication, authorization, and security best practices
5. **Test** — Write comprehensive tests with xUnit and integration testing; run `dotnet test` to confirm all tests pass — if tests fail, diagnose failures, fix the implementation, and re-run before continuing; verify endpoints with `curl` or a REST client

## Reference Guide

Load detailed guidance based on context:

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Minimal APIs | `references/minimal-apis.md` | Creating endpoints, routing, middleware, filters |
| Clean Architecture | `references/clean-architecture.md` | CQRS, MediatR, layers, DI patterns, pipeline behaviors |
| Entity Framework | `references/entity-framework.md` | DbContext, migrations, relationships, configurations |
| EF Core Best Practices | `references/ef-core-best-practices.md` | Performance tuning, batch ops, concurrency, indexing |
| Dapper Patterns | `references/dapper-patterns.md` | Raw SQL, multi-mapping, TVPs, stored procedures |
| Authentication | `references/authentication.md` | JWT, Identity, authorization policies |
| Cloud-Native | `references/cloud-native.md` | Docker, health checks, K8s, OpenTelemetry, Serilog |
| Caching Patterns | `references/caching-patterns.md` | Multi-level cache, Redis, stale-while-revalidate |

## Constraints

### MUST DO
- Use .NET 8+ and C# 12 features
- Enable nullable reference types: `<Nullable>enable</Nullable>`
- Use async/await for all I/O — e.g., `await dbContext.Users.ToListAsync()`
- Pass `CancellationToken` through all async method chains
- Implement proper dependency injection (constructor injection)
- Use record types for DTOs — e.g., `public record UserDto(int Id, string Name);`
- Use `sealed` on classes not designed for inheritance
- Follow clean architecture: Domain → Application → Infrastructure → API
- Write integration tests with `WebApplicationFactory<Program>`
- Configure OpenAPI/Swagger documentation
- Use `AsNoTracking()` for read-only EF Core queries
- Return `Result<T>` instead of throwing exceptions for business logic failures
- Use guard clauses for early validation — `ArgumentNullException.ThrowIfNull()`

### MUST NOT DO
- Use synchronous I/O operations
- Block on async with `.Result` or `.Wait()` (deadlock risk)
- Use `async void` (except event handlers)
- Expose entities directly in API responses (use DTOs)
- Skip input validation at API boundaries
- Use `new HttpClient()` directly (use `IHttpClientFactory`)
- Hardcode configuration values (use `IOptions<T>`)
- Catch generic `Exception` without logging or re-throwing
- Use `dynamic` or `object` in business logic

## Core Patterns

### Dependency Injection

```csharp
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddApplicationServices(
        this IServiceCollection services,
        IConfiguration configuration)
    {
        services.AddScoped<IProductService, ProductService>();
        services.AddSingleton<ICacheService, RedisCacheService>();
        services.AddTransient<IValidator<CreateOrderRequest>, CreateOrderValidator>();

        services.Configure<CatalogOptions>(configuration.GetSection(CatalogOptions.SectionName));

        services.AddScoped<IPriceCalculator>(sp =>
        {
            var options = sp.GetRequiredService<IOptions<PricingOptions>>().Value;
            return options.UseNewEngine
                ? sp.GetRequiredService<NewPriceCalculator>()
                : sp.GetRequiredService<LegacyPriceCalculator>();
        });

        services.AddKeyedScoped<IPaymentProcessor, StripeProcessor>("stripe");
        services.AddKeyedScoped<IPaymentProcessor, PayPalProcessor>("paypal");

        return services;
    }
}
```

### Async/Await

```csharp
public async Task<(Stock, Price)> GetStockAndPriceAsync(
    string productId,
    CancellationToken ct = default)
{
    var stockTask = _stockService.GetAsync(productId, ct);
    var priceTask = _priceService.GetAsync(productId, ct);
    await Task.WhenAll(stockTask, priceTask);
    return (await stockTask, await priceTask);
}

public async Task<T> LibraryMethodAsync<T>(CancellationToken ct = default)
{
    var result = await _httpClient.GetAsync(url, ct).ConfigureAwait(false);
    return await result.Content.ReadFromJsonAsync<T>(ct).ConfigureAwait(false);
}

public ValueTask<Product?> GetCachedProductAsync(string id)
{
    if (_cache.TryGetValue(id, out Product? product))
        return ValueTask.FromResult(product);
    return new ValueTask<Product?>(GetFromDatabaseAsync(id));
}
```

### IOptions Pattern

```csharp
public sealed class CatalogOptions
{
    public const string SectionName = "Catalog";

    public int DefaultPageSize { get; set; } = 50;
    public int MaxPageSize { get; set; } = 200;
    public TimeSpan CacheDuration { get; set; } = TimeSpan.FromMinutes(15);
}

// IOptions<T> — singleton, read once at startup
// IOptionsSnapshot<T> — scoped, re-reads per request
// IOptionsMonitor<T> — singleton, notified on changes
```

### Result Pattern

```csharp
public sealed class Result<T>
{
    public bool IsSuccess { get; }
    public T? Value { get; }
    public string? Error { get; }
    public string? ErrorCode { get; }

    private Result(bool isSuccess, T? value, string? error, string? errorCode)
    {
        IsSuccess = isSuccess;
        Value = value;
        Error = error;
        ErrorCode = errorCode;
    }

    public static Result<T> Success(T value) => new(true, value, null, null);
    public static Result<T> Failure(string error, string? code = null) => new(false, default, error, code);

    public Result<TNew> Map<TNew>(Func<T, TNew> mapper) =>
        IsSuccess ? Result<TNew>.Success(mapper(Value!)) : Result<TNew>.Failure(Error!, ErrorCode);

    public async Task<Result<TNew>> MapAsync<TNew>(Func<T, Task<TNew>> mapper) =>
        IsSuccess ? Result<TNew>.Success(await mapper(Value!)) : Result<TNew>.Failure(Error!, ErrorCode);
}
```

### Immutability

```csharp
public sealed record Money(decimal Amount, string Currency);

public sealed class CreateOrderRequest
{
    public required string CustomerId { get; init; }
    public required IReadOnlyList<OrderItem> Items { get; init; }
}
```

### Guard Clauses

```csharp
public async Task<ProcessResult> ProcessPaymentAsync(
    PaymentRequest request,
    CancellationToken cancellationToken)
{
    ArgumentNullException.ThrowIfNull(request);

    if (request.Amount <= 0)
        throw new ArgumentOutOfRangeException(nameof(request.Amount), "Amount must be positive");

    if (string.IsNullOrWhiteSpace(request.Currency))
        throw new ArgumentException("Currency is required", nameof(request.Currency));

    var gateway = _gatewayFactory.Create(request.Currency);
    return await gateway.ChargeAsync(request, cancellationToken);
}
```

### Middleware

```csharp
public sealed class RequestTimingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestTimingMiddleware> _logger;

    public RequestTimingMiddleware(RequestDelegate next, ILogger<RequestTimingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var stopwatch = Stopwatch.StartNew();
        try
        {
            await _next(context);
        }
        finally
        {
            stopwatch.Stop();
            _logger.LogInformation(
                "Request {Method} {Path} completed in {ElapsedMs}ms with status {StatusCode}",
                context.Request.Method,
                context.Request.Path,
                stopwatch.ElapsedMilliseconds,
                context.Response.StatusCode);
        }
    }
}
```

## Testing

### Unit Tests with xUnit and Moq

```csharp
public class OrderServiceTests
{
    private readonly Mock<IOrderRepository> _mockRepository = new();
    private readonly Mock<IStockService> _mockStockService = new();
    private readonly OrderService _sut;

    public OrderServiceTests()
    {
        _sut = new OrderService(_mockRepository.Object, _mockStockService.Object);
    }

    [Fact]
    public async Task CreateOrderAsync_WithValidRequest_ReturnsSuccess()
    {
        _mockStockService
            .Setup(s => s.CheckAsync("PROD-001", 5, It.IsAny<CancellationToken>()))
            .ReturnsAsync(new StockResult { IsAvailable = true, Available = 10 });

        _mockRepository
            .Setup(r => r.CreateAsync(It.IsAny<Order>(), It.IsAny<CancellationToken>()))
            .ReturnsAsync(new Order { Id = 1 });

        var result = await _sut.CreateOrderAsync(new CreateOrderRequest
        {
            ProductId = "PROD-001",
            Quantity = 5
        });

        Assert.True(result.IsSuccess);
        _mockRepository.Verify(
            r => r.CreateAsync(It.IsAny<Order>(), It.IsAny<CancellationToken>()),
            Times.Once);
    }
}
```

### Integration Tests with WebApplicationFactory

```csharp
public class ProductsApiTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly WebApplicationFactory<Program> _factory;
    private readonly HttpClient _client;

    public ProductsApiTests(WebApplicationFactory<Program> factory)
    {
        _factory = factory.WithWebHostBuilder(builder =>
        {
            builder.ConfigureServices(services =>
            {
                services.RemoveAll<DbContextOptions<AppDbContext>>();
                services.AddDbContext<AppDbContext>(options =>
                    options.UseInMemoryDatabase("TestDb"));

                services.RemoveAll<IDistributedCache>();
                services.AddDistributedMemoryCache();
            });
        });
        _client = _factory.CreateClient();
    }

    [Fact]
    public async Task GetProduct_WithValidId_ReturnsProduct()
    {
        using var scope = _factory.Services.CreateScope();
        var context = scope.ServiceProvider.GetRequiredService<AppDbContext>();
        context.Products.Add(new Product { Id = "TEST-001", Name = "Test", Price = 99.99m });
        await context.SaveChangesAsync();

        var response = await _client.GetAsync("/api/products/TEST-001");

        response.EnsureSuccessStatusCode();
        var product = await response.Content.ReadFromJsonAsync<Product>();
        Assert.Equal("Test", product!.Name);
    }
}
```

## Anti-Patterns

| Anti-Pattern | Fix |
|---|---|
| `async void` methods | Return `Task` (except event handlers) |
| `.Result` or `.Wait()` | Use `await` |
| `catch (Exception) { }` | Handle or rethrow with context |
| `new Service()` in constructors | Use constructor injection |
| `dynamic` in business logic | Use generics or explicit types |
| Mutable `static` state | Use DI scoping or `ConcurrentDictionary` |
| N+1 queries | Use `.Include()` or explicit joins |
| Early `ToList()` | Filter in SQL before materializing |
| `new HttpClient()` | Use `IHttpClientFactory` |
| Hardcoded config | Use `IOptions<T>` |

## Output Templates

When implementing .NET features, provide:
1. Project structure (solution/project files)
2. Domain models and DTOs
3. API endpoints or service implementations
4. Database context and migrations if applicable
5. Brief explanation of architectural decisions
