# /tech-stack:add-dotnet10-best-practices - ASP.NET Core 10 Configuration

Sets up ASP.NET Core 10 best practices in your CLAUDE.md file, providing Claude with explicit guidelines for generating performant, reliable, and async-correct .NET code.

- Purpose - Configure ASP.NET Core 10 coding standards
- Output - Updated CLAUDE.md with ASP.NET Core 10 guidelines
- Source - https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices?view=aspnetcore-10.0

```bash
/tech-stack:add-dotnet10-best-practices
```

## Arguments

Optional argument which practices to add or avoid.

## How It Works

1. **File Detection**: Locates or creates CLAUDE.md in your project root.

2. **Content Injection**: Adds the following sections:
   - **Avoid Blocking Calls** - async end-to-end, no `Task.Wait`/`.Result`, no `Task.Run` wrapping.
   - **Large Object Allocations** - LOH / Gen2 GC rules, `ArrayPool<T>`, caching.
   - **Data Access & I/O** - no-tracking EF queries, DbContext pooling, pagination.
   - **HttpClientFactory** - reuse connections, avoid socket exhaustion.
   - **HttpContext Rules** - thread safety, background work, `IServiceScopeFactory`.
   - **Response Body** - async I/O, `HasStarted`, `OnStarting`.
   - **Minimal APIs / Controllers** - async actions, `IAsyncEnumerable<T>`.
   - **Exceptions, Compression, Hosting** - production-hardening guidelines.

3. **Non-Destructive Update**: Preserves existing CLAUDE.md content while adding new guidelines.
