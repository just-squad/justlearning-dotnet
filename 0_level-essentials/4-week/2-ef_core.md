# **Entity Framework Core (ORM)**

**Цель**: Использовать EF Core для работы с базой данных без написания SQL.

## **Что такое ORM?**

ORM (Object-Relational Mapping) — это технология, которая позволяет работать с базой данных как с обычными объектами C#.  
Пример: вместо SQL-запросов вы пишете:

```csharp
var user = new User { Name = "Анна" };
context.Users.Add(user);
context.SaveChanges(); // EF сам сгенерирует INSERT
```

---

## **Настройка EF Core**

1. **Установите NuGet-пакеты** для вашей СУБД (например, PostgreSQL):

   ```bash
   Npgsql.EntityFrameworkCore.PostgreSQL
   Microsoft.EntityFrameworkCore.Design
   ```

2. **Создайте класс контекста** (главный класс для работы с БД):

   ```csharp
   public class AppDbContext : DbContext
   {
       public DbSet<User> Users { get; set; } // Таблица Users

       protected override void OnConfiguring(DbContextOptionsBuilder options)
       {
           options.UseNpgsql("Host=localhost;Database=mydb;Username=user;Password=password");
       }
   }
   ```

3. **Миграции** (создание и обновление структуры БД):
   - Выполните в терминале:

     ```bash
     dotnet ef migrations add InitialCreate
     dotnet ef database update

     ```

   - EF создаст таблицы на основе ваших моделей.

---

## **CRUD с EF Core**

Примеры операций:

- **Create**:

  ```csharp
  using var context = new AppDbContext();
  var user = new User { Name = "Иван", Email = "ivan@test.com" };
  context.Users.Add(user);
  context.SaveChanges();
  ```

- **Read**:

  ```csharp
  var user = context.Users.FirstOrDefault(u => u.Id == 1);
  ```

- **Update**:

  ```csharp
  var user = context.Users.Find(1);
  user.Name = "Новое имя";
  context.SaveChanges();
  ```

- **Delete**:

  ```csharp
  var user = context.Users.Find(1);
  context.Users.Remove(user);
  context.SaveChanges();
  ```
