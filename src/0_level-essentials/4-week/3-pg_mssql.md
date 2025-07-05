# **Работа с PostgreSQL/SQL Server**

**Цель**: Настроить подключение к реальной базе данных.

## **Строка подключения**

Для PostgreSQL:

```csharp
options.UseNpgsql("Host=localhost;Port=5432;Database=mydb;Username=user;Password=password");
```

Для SQL Server:

```csharp
options.UseSqlServer("Server=localhost;Database=mydb;User Id=user;Password=password");
```

---

## **Отношения между таблицами**

Пример связи «1 ко многим»: У пользователя много заказов.

```csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Order> Orders { get; set; } // Навигационное свойство
}

public class Order
{
    public int Id { get; set; }
    public string Product { get; set; }
    public int UserId { get; set; } // Внешний ключ
    public User User { get; set; } // Навигационное свойство
}
```

**Как использовать**:

```csharp
var user = context.Users.Include(u => u.Orders).First(); // Загрузить пользователя с заказами
foreach (var order in user.Orders)
{
    Console.WriteLine(order.Product);
}
```
