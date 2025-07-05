# **Логирование**

**Цель**: Отслеживать работу приложения и находить ошибки.

## **Встроенное логирование**

```csharp
private readonly ILogger<ProductsController> _logger;

public ProductsController(ILogger<ProductsController> logger)
{
    _logger = logger;
}

[HttpGet]
public IActionResult GetProducts()
{
    _logger.LogInformation("Запрос всех товаров");
    try
    {
        // Код с возможной ошибкой
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, "Ошибка при получении товаров");
        return StatusCode(500);
    }
}
```

## **Настройка Serilog**

1. Установите пакеты:

   ```bash
   Serilog.AspNetCore
   Serilog.Sinks.File
   Serilog.Sinks.Console
   ```

2. Настройте в `Program.cs`:

   ```csharp
   Log.Logger = new LoggerConfiguration()
       .WriteTo.Console()
       .WriteTo.File("logs/log.txt", rollingInterval: RollingInterval.Day)
       .CreateLogger();

   builder.Host.UseSerilog();
   ```
