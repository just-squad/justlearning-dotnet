# **Оптимизация запросов к БД**

**Цель**: Избегать медленных запросов и N+1 проблемы.

## **Жадная загрузка (Eager Loading)**

Используйте `Include` и `ThenInclude` для загрузки связанных данных:

```csharp
var orders = await _dbContext.Orders
    .Include(o => o.User) // Загружаем пользователя
    .ThenInclude(u => u.Address) // Загружаем адрес пользователя
    .ToListAsync();
```

## **Логирование SQL-запросов**

Добавьте в `AppDbContext`:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
{
    options
        .UseNpgsql("...")
        .LogTo(Console.WriteLine, LogLevel.Information); // Вывод SQL в консоль
}
```

## **Индексы для ускорения поиска**

Добавьте через миграции:

```csharp
// В классе конфигурации сущности
entity.HasIndex(u => u.Email).IsUnique(); // Уникальный индекс на Email
```
