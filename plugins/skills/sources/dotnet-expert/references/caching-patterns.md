# Caching Patterns

## Multi-Level Cache (L1 Memory + L2 Redis + L3 Database)

```csharp
public class CachedProductService : IProductService
{
    private readonly IProductRepository _repository;
    private readonly IMemoryCache _memoryCache;
    private readonly IDistributedCache _distributedCache;
    private readonly ILogger<CachedProductService> _logger;

    private static readonly TimeSpan MemoryCacheDuration = TimeSpan.FromMinutes(1);
    private static readonly TimeSpan DistributedCacheDuration = TimeSpan.FromMinutes(15);

    public CachedProductService(
        IProductRepository repository,
        IMemoryCache memoryCache,
        IDistributedCache distributedCache,
        ILogger<CachedProductService> logger)
    {
        _repository = repository;
        _memoryCache = memoryCache;
        _distributedCache = distributedCache;
        _logger = logger;
    }

    public async Task<Product?> GetByIdAsync(string id, CancellationToken ct = default)
    {
        var cacheKey = $"product:{id}";

        if (_memoryCache.TryGetValue(cacheKey, out Product? cached))
        {
            _logger.LogDebug("L1 cache hit for {CacheKey}", cacheKey);
            return cached;
        }

        var distributed = await _distributedCache.GetStringAsync(cacheKey, ct);
        if (distributed != null)
        {
            _logger.LogDebug("L2 cache hit for {CacheKey}", cacheKey);
            var product = JsonSerializer.Deserialize<Product>(distributed);
            _memoryCache.Set(cacheKey, product, MemoryCacheDuration);
            return product;
        }

        _logger.LogDebug("Cache miss for {CacheKey}, fetching from database", cacheKey);
        var fromDb = await _repository.GetByIdAsync(id, ct);

        if (fromDb != null)
        {
            var serialized = JsonSerializer.Serialize(fromDb);

            await _distributedCache.SetStringAsync(
                cacheKey,
                serialized,
                new DistributedCacheEntryOptions
                {
                    AbsoluteExpirationRelativeToNow = DistributedCacheDuration
                },
                ct);

            _memoryCache.Set(cacheKey, fromDb, MemoryCacheDuration);
        }

        return fromDb;
    }

    public async Task InvalidateAsync(string id, CancellationToken ct = default)
    {
        var cacheKey = $"product:{id}";
        _memoryCache.Remove(cacheKey);
        await _distributedCache.RemoveAsync(cacheKey, ct);
        _logger.LogInformation("Invalidated cache for {CacheKey}", cacheKey);
    }
}
```

## Stale-While-Revalidate Pattern

Returns stale data immediately while refreshing in background. Prevents cache stampede.

```csharp
public class StaleWhileRevalidateCache<T>
{
    private readonly IDistributedCache _cache;
    private readonly TimeSpan _freshDuration;
    private readonly TimeSpan _staleDuration;

    public StaleWhileRevalidateCache(
        IDistributedCache cache,
        TimeSpan freshDuration,
        TimeSpan staleDuration)
    {
        _cache = cache;
        _freshDuration = freshDuration;
        _staleDuration = staleDuration;
    }

    public async Task<T?> GetOrCreateAsync(
        string key,
        Func<CancellationToken, Task<T>> factory,
        CancellationToken ct = default)
    {
        var cached = await _cache.GetStringAsync(key, ct);

        if (cached != null)
        {
            var entry = JsonSerializer.Deserialize<CacheEntry<T>>(cached)!;

            if (entry.IsStale(_freshDuration) && !entry.IsExpired(_staleDuration))
            {
                _ = Task.Run(async () =>
                {
                    var fresh = await factory(CancellationToken.None);
                    await SetAsync(key, fresh, CancellationToken.None);
                });
            }

            if (!entry.IsExpired(_staleDuration))
                return entry.Value;
        }

        var value = await factory(ct);
        await SetAsync(key, value, ct);
        return value;
    }

    private async Task SetAsync(string key, T value, CancellationToken ct)
    {
        var entry = new CacheEntry<T>(value, DateTime.UtcNow);
        var serialized = JsonSerializer.Serialize(entry);

        await _cache.SetStringAsync(
            key,
            serialized,
            new DistributedCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = _staleDuration
            },
            ct);
    }

    private sealed record CacheEntry<TValue>(TValue Value, DateTime CreatedAt)
    {
        public bool IsStale(TimeSpan freshDuration) => DateTime.UtcNow - CreatedAt > freshDuration;
        public bool IsExpired(TimeSpan staleDuration) => DateTime.UtcNow - CreatedAt > staleDuration;
    }
}
```

## Redis Setup

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = builder.Configuration.GetConnectionString("Redis");
    options.InstanceName = "MyApp_";
});

builder.Services.AddMemoryCache();

builder.Services.AddScoped<IProductService, CachedProductService>();
```

## Cache Invalidation Strategies

| Strategy | When to Use |
|----------|------------|
| Time-based expiration | Data that can be slightly stale |
| Event-driven invalidation | Data modified through your API |
| Stale-while-revalidate | High-traffic reads, acceptable staleness |
| Cache-aside | Default pattern for most cases |
| Write-through | Consistency-critical data |

## Quick Reference

| Pattern | Purpose |
|---------|---------|
| L1 Memory + L2 Redis | Minimize latency with fallback layers |
| `IMemoryCache` | In-process, fastest, per-instance |
| `IDistributedCache` | Shared across instances (Redis) |
| Stale-while-revalidate | Serve stale, refresh async |
| `InvalidateAsync` | Remove from all cache layers |
| `DistributedCacheEntryOptions` | TTL and sliding expiration config |
