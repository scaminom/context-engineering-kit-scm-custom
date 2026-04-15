---
description: ASP.NET Core 10 performance and reliability rules (auto-loads on .NET files)
globs: ["**/*.cs", "**/*.csproj", "**/*.cshtml", "**/*.razor"]
---

# ASP.NET Core 10 Best Practices

Guidelines for maximizing performance and reliability of ASP.NET Core 10 apps. Source: https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices?view=aspnetcore-10.0

## General Principles

- **Cache aggressively** - See ASP.NET Core caching overview.
- **Understand hot code paths** - Frequently-called paths limit scale-out; optimize them first.
- **Use the latest ASP.NET Core release** - Each release includes perf improvements. Target .NET 10 for HTTP/3, improved GC, and throughput gains.

## Avoid Blocking Calls

- **Do** make hot code paths asynchronous.
- **Do** call data access, I/O, and long-running operation APIs asynchronously when an async API exists.
- **Do** make controller/Razor Page/Minimal API actions asynchronous end-to-end.
- **Do not** block with `Task.Wait` or `Task<TResult>.Result`.
- **Do not** acquire locks in common code paths.
- **Do not** call `Task.Run` and immediately `await` it.
- **Do not** use `Task.Run` to wrap a synchronous API as asynchronous.
- **Consider** message brokers (e.g. Azure Service Bus) to offload long-running work.

## Large Collections & Pagination

- **Do** paginate large collections using page size / page index parameters.
- **Do** return `IAsyncEnumerable<T>` over `IEnumerable<T>` when streaming results; use `ToListAsync` before returning when you must return a concrete enumerable.
- **Do not** materialize entire large datasets into a single response.

## Minimize Large Object Allocations

- **Do** cache large objects frequently used.
- **Do** pool buffers with `ArrayPool<T>` for large arrays.
- **Do not** allocate many short-lived large objects (>= 85,000 bytes) on hot code paths - they go to the LOH and trigger Gen2 GC.

## Optimize Data Access & I/O

- **Do** call all data access APIs asynchronously.
- **Do** use no-tracking EF Core queries for read-only data.
- **Do** filter and aggregate LINQ queries (`.Where`, `.Select`, `.Sum`) on the database side.
- **Do** minimize network round trips - fetch required data in a single call.
- **Do** cache frequently accessed data with `MemoryCache` or `DistributedCache` when slightly stale is acceptable.
- **Do not** retrieve more data than necessary.
- **Do not** use projection queries on collections that cause N+1 SQL.
- **Consider** DbContext pooling and explicitly compiled queries in high-scale apps - measure first.

## Pool HTTP Connections

- **Do** use `IHttpClientFactory` (or typed clients) to obtain `HttpClient` instances.
- **Do not** create and dispose `HttpClient` instances directly - leaves sockets in TIME_WAIT and exhausts them.

## Keep Common Code Paths Fast

- **Do** profile middleware in the request pipeline, especially early middleware.
- **Do not** put long-running tasks in custom middleware.

## Long-Running Tasks

- **Do** handle long-running requests with `IHostedService`/`BackgroundService` or out-of-process (Azure Functions + Service Bus).
- **Do** use SignalR for async client communication.
- **Do not** wait for long-running work as part of an HTTP request.

## Client Assets

- **Do** bundle and minify JS/CSS for initial load.
- **Do** use the `environment` tag to differentiate Development/Production.
- **Consider** Webpack or similar for complex front-ends.

## Compression

- **Do** enable response compression to reduce payload sizes.

## Exceptions

- **Do** throw/catch exceptions only for unusual or unexpected conditions.
- **Do** add logic to detect and handle conditions that would otherwise throw.
- **Do not** use exceptions for normal program flow, especially on hot code paths.

## HttpRequest / HttpResponse Body

- **Do** use async I/O on request/response bodies (`ReadToEndAsync`, `JsonSerializer.DeserializeAsync(Request.Body)`).
- **Do not** synchronously read/write bodies - Kestrel does not support sync reads and this causes sync-over-async and thread pool starvation.
- **Do not** buffer large request/response bodies into a single `byte[]` or `string` - risks OOM/DoS and LOH pressure.
- **Do** prefer `System.Text.Json` (default) over `Newtonsoft.Json` for async, UTF-8 optimized, higher performance serialization.

## Forms

- **Do** use `HttpContext.Request.ReadFormAsync()`.
- **Do not** use `HttpContext.Request.Form` directly - it does sync-over-async.

## HttpContext Rules

- **Do not** store `IHttpContextAccessor.HttpContext` in a field - store the accessor instead and resolve `HttpContext` on each use, checking for null.
- **Do not** access `HttpContext` from multiple threads in parallel - it is not thread-safe. Copy the data you need first.
- **Do not** use `HttpContext` after the request completes.
- **Do not** use `async void` in controllers/endpoints - always return `Task`.
- **Do not** capture `HttpContext` in background threads (`Task.Run`) - copy required values into locals first.

## DI & Background Work

- **Do not** capture scoped services (e.g. `DbContext`) from a request into `Task.Run` - they will be disposed.
- **Do** inject `IServiceScopeFactory` (singleton) and create a new scope inside background work:
  ```csharp
  await using var scope = serviceScopeFactory.CreateAsyncScope();
  var ctx = scope.ServiceProvider.GetRequiredService<ContosoDbContext>();
  ```
- **Do** implement background tasks as hosted services (`IHostedService`/`BackgroundService`).

## Response Headers & Status

- **Do not** modify status code or headers after the response body has started - check `Response.HasStarted` or register `Response.OnStarting` callback.
- **Do not** call `next()` in middleware after you have started writing to the response body.

## Hosting

- **Do** use in-process hosting with IIS (default since ASP.NET Core 3.0+) for better performance than out-of-process.

## Request Validation

- **Do not** assume `HttpRequest.ContentLength` is non-null. Null means unknown length, not zero. Comparisons with null return false and can bypass size checks.
