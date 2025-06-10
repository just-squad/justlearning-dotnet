# **Microsoft Dependency Injection (DI)**

**Цель**: Научиться внедрять зависимости, чтобы код был тестируемым и гибким.

Dependency Injection (DI) — это ключевая техника разработки, которая помогает создавать гибкие, тестируемые и поддерживаемые приложения. В этом руководстве разберем, как работает встроенный механизм DI в .NET, как его правильно настраивать, и в каких случаях какие подходы лучше использовать.

---

## **Основы Dependency Injection**

### **Что такое DI и зачем он нужен?**

**Dependency Injection (DI)** — это паттерн проектирования, при котором зависимости (сервисы) передаются в класс извне, а не создаются внутри него. Это позволяет:

- Уменьшить связанность (coupling) между компонентами.
- Упростить тестирование (можно подменять зависимости).
- Улучшить управляемость кода.

**Пример без DI (плохо):**

```csharp
public class OrderService
{
    private readonly EmailSender _emailSender = new EmailSender();

    public void ProcessOrder(Order order)
    {
        // Логика обработки заказа
        _emailSender.SendEmail(order.CustomerEmail, "Ваш заказ оформлен");
    }
}
```

**Проблемы:**

- `OrderService` жестко зависит от `EmailSender`.
- Невозможно заменить `EmailSender` на другой сервис (например, для тестов).

**Пример с DI (хорошо):**

```csharp
public class OrderService
{
    private readonly IEmailSender _emailSender;

    public OrderService(IEmailSender emailSender) // Зависимость внедряется извне
    {
        _emailSender = emailSender;
    }

    public void ProcessOrder(Order order)
    {
        _emailSender.SendEmail(order.CustomerEmail, "Ваш заказ оформлен");
    }
}
```

**Преимущества:**

- Можно подменить реализацию `IEmailSender` (например, на `FakeEmailSender` в тестах).
- Код становится более гибким и поддерживаемым.

---

## **Встроенный DI-контейнер в .NET**

Microsoft предоставляет легковесный DI-контейнер (`Microsoft.Extensions.DependencyInjection`), который используется в ASP.NET Core и других .NET-приложениях.

### **Основные понятия**

- **Сервис (Service)** — любой класс или интерфейс, который регистрируется в DI.
- **Жизненный цикл сервиса** — определяет, как долго живет объект сервиса.
- **Контейнер (ServiceProvider)** — управляет созданием и временем жизни сервисов.

---

## **Регистрация сервисов**

Сервисы регистрируются в `Startup.cs` (в ASP.NET Core) или в `Program.cs` (в .NET 6+).

### **Три типа жизненного цикла**

| Тип           | Описание                                          | Когда использовать?                                     |
| ------------- | ------------------------------------------------- | ------------------------------------------------------- |
| **Transient** | Новый экземпляр при каждом запросе                | Легковесные сервисы без состояния (`IDateTimeProvider`) |
| **Scoped**    | Один экземпляр на область (например, HTTP-запрос) | Сервисы с контекстом (`DbContext`, `UserSession`)       |
| **Singleton** | Один экземпляр на все время работы приложения     | Общие ресурсы (`IConfiguration`, кэши)                  |

**Пример регистрации:**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<IEmailSender, EmailSender>(); // Transient
    services.AddScoped<DbContext, AppDbContext>();      // Scoped
    services.AddSingleton<ICacheService, CacheService>(); // Singleton
}
```

---

## **Внедрение зависимостей**

Зависимости можно внедрять тремя способами:

1. **Через конструктор (рекомендуется)**
2. **Через свойства (редко, не рекомендуется)**
3. **Через метод (например, в Middleware)**

### **Внедрение через конструктор**

```csharp
public class OrderService
{
    private readonly IEmailSender _emailSender;
    private readonly DbContext _dbContext;

    public OrderService(IEmailSender emailSender, DbContext dbContext)
    {
        _emailSender = emailSender;
        _dbContext = dbContext;
    }
}
```

### **Внедрение в контроллеры (ASP.NET Core)**

```csharp
[ApiController]
[Route("api/orders")]
public class OrdersController : ControllerBase
{
    private readonly OrderService _orderService;

    public OrdersController(OrderService orderService)
    {
        _orderService = orderService;
    }

    [HttpPost]
    public IActionResult CreateOrder(Order order)
    {
        _orderService.ProcessOrder(order);
        return Ok();
    }
}
```

Так же можно внедрять зависимости непосредственно в методы контроллера

```csharp
[ApiController]
[Route("api/orders")]
public class OrdersController : ControllerBase
{
    public OrdersController()
    {
    }

    [HttpPost]
    public IActionResult CreateOrder([FromService] OrderService orderService, Order order)
    {
        orderService.ProcessOrder(order);
        return Ok();
    }
}
```

---

## **Практические примеры**

### **Пример 1: Логирование (Singleton)**

```csharp
public interface ILogger
{
    void Log(string message);
}

public class FileLogger : ILogger
{
    public void Log(string message) => File.AppendAllText("log.txt", message);
}

// Регистрация
services.AddSingleton<ILogger, FileLogger>();
```

### **Пример 2: Работа с БД (Scoped)**

```csharp
public class AppDbContext : DbContext { /* ... */ }

// Регистрация
services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(Configuration.GetConnectionString("Default")));
```

### **Пример 3: Валидация запросов (Transient)**

```csharp
public interface IValidator<T>
{
    bool Validate(T model);
}

public class OrderValidator : IValidator<Order>
{
    public bool Validate(Order order) => order.Total > 0;
}

// Регистрация
services.AddTransient<IValidator<Order>, OrderValidator>();
```

---

## **Лучшие практики и подводные камни**

### **Что регистрировать как Singleton?**

✅ **Можно:**

- Сервисы без состояния (`IConfiguration`).
- Кэши (`IMemoryCache`).
- HTTP-клиенты (`IHttpClientFactory`).

❌ **Нельзя:**

- `DbContext` (должен быть Scoped).
- Сервисы, зависящие от запроса (например, `IHttpContextAccessor`).

### **Циклические зависимости**

**Проблема:**

```csharp
public class ServiceA
{
    public ServiceA(ServiceB b) { }
}

public class ServiceB
{
    public ServiceB(ServiceA a) { }
}
```

**Решение:**

- Пересмотреть архитектуру (разделить логику).
- Использовать `Lazy<T>` или фабрики.

### **Когда использовать `IServiceProvider` напрямую?**

Иногда нужно получить сервис вручную (например, в фабриках):

```csharp
public class OrderProcessorFactory
{
    private readonly IServiceProvider _provider;

    public OrderProcessorFactory(IServiceProvider provider)
    {
        _provider = provider;
    }

    public OrderProcessor Create()
    {
        return _provider.GetRequiredService<OrderProcessor>();
    }
}
```

---

## **Альтернативы: Autofac, Simple Injector**

Встроенный DI-контейнер Microsoft прост, но ограничен. Для сложных сценариев можно использовать:

- **Autofac** (поддержка модулей, property injection).
- **Simple Injector** (высокая производительность).

**Пример Autofac:**

```csharp
var builder = new ContainerBuilder();
builder.RegisterType<EmailSender>().As<IEmailSender>();
var container = builder.Build();
```

---

## **Выводы: Когда что использовать?**

| Сценарий                 | Рекомендация                             |
| ------------------------ | ---------------------------------------- |
| **Легковесные сервисы**  | `Transient`                              |
| **Сервисы с контекстом** | `Scoped` (например, `DbContext`)         |
| **Общие ресурсы**        | `Singleton` (например, `IConfiguration`) |
| **Тестирование**         | Использовать Moq + DI                    |
| **Сложные зависимости**  | Рассмотреть Autofac/Simple Injector      |

**Главное правило:**  
_"Зависимости должны быть явными, а время жизни — контролируемым."_

---

## **Дополнительные материалы**

- [Официальная документация](https://docs.microsoft.com/ru-ru/aspnet/core/fundamentals/dependency-injection)
- [DI в ASP.NET Core на практике](https://habr.com/ru/post/346628/)
- [Сравнение DI-контейнеров](https://www.claudiobernasconi.ch/2019/01/24/net-dependency-injection-container-performance/)
