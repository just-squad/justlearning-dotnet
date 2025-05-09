# **Кэширование**

**Цель**: Ускорить работу API, уменьшив нагрузку на БД.

## **Кэширование ответов**

```csharp
[HttpGet]
[ResponseCache(Duration = 60)] // Кэшировать ответ на 60 секунд
public IActionResult GetProducts()
{
    return Ok(_dbContext.Products.ToList());
}
```

## **Распределенное кэширование (Redis)**

1. Установите пакет `Microsoft.Extensions.Caching.StackExchangeRedis`.
2. Настройте в `Program.cs`:

   ```csharp
   builder.Services.AddStackExchangeRedisCache(options =>
   {
       options.Configuration = "localhost:6379"; // Адрес Redis-сервера
   });

   ```

3. Используйте в контроллере:

   ```csharp
   private readonly IDistributedCache _cache;

   public ProductsController(IDistributedCache cache)
   {
       _cache = cache;
   }

   [HttpGet("{id}")]
   public async Task<Product> GetProduct(int id)
   {
       string key = $"product_{id}";
       byte[] cachedData = await _cache.GetAsync(key);
       if (cachedData != null)
       {
           return JsonSerializer.Deserialize<Product>(cachedData);
       }

       Product product = await _dbContext.Products.FindAsync(id);
       await _cache.SetAsync(key, JsonSerializer.SerializeToUtf8Bytes(product), new DistributedCacheEntryOptions
       {
           AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)
       });
       return product;
   }
   ```
