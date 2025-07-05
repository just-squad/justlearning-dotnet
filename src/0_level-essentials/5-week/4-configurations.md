# **Работа с конфигурацией**

**Цель**: Настраивать приложение для разных сред (development, production).

## **appsettings.json**

```json
{
  "Database": {
    "ConnectionString": "Host=localhost;Database=mydb;Username=user;Password=123",
    "Timeout": 30
  }
}
```

## **Чтение настроек**

1. Создайте класс для конфигурации:

   ```csharp
   public class DatabaseSettings
   {
       public string ConnectionString { get; set; }
       public int Timeout { get; set; }
   }
   ```

2. Зарегистрируйте его в `Program.cs`:

   ```csharp
   builder.Services.Configure<DatabaseSettings>(builder.Configuration.GetSection("Database"));
   ```

3. Используйте через `IOptions<T>`:

   ```csharp
   private readonly DatabaseSettings _dbSettings;

   public ProductsController(IOptions<DatabaseSettings> dbSettings)
   {
       _dbSettings = dbSettings.Value;
       Console.WriteLine($"Timeout: {_dbSettings.Timeout}");
   }
   ```
