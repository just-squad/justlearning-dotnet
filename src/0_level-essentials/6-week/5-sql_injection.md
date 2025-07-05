# **Защита от SQL-инъекций**

**Правило**: **Никогда** не используйте сырые SQL-строки с конкатенацией.  
**Пример уязвимого кода**:

```csharp
var sql = $"SELECT * FROM Users WHERE Name = '{name}'"; // Опасность!
```

**Решение**: Всегда используйте параметризованные запросы или ORM (EF Core):

```csharp
// EF Core автоматически экранирует параметры
var user = _dbContext.Users.FirstOrDefault(u => u.Name == name);
```
