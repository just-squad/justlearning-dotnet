# **Фильтры (Filters)**

**Цель**: Перехватывать и обрабатывать запросы/ответы на разных этапах выполнения.

Фильтры — это мощный механизм в ASP.NET Core, позволяющий перехватывать запросы и ответы на разных этапах выполнения pipeline. Они помогают реализовать сквозную функциональность (cross-cutting concerns), такую как:

- **Авторизация** (проверка прав доступа)
- **Валидация** (проверка входных данных)
- **Логирование** (запись действий)
- **Обработка ошибок** (глобальные исключения)
- **Кэширование** (управление заголовками Cache-Control)

---

## **Типы фильтров и их место в конвейере запросов**

Фильтры выполняются в строгом порядке в рамках **Middleware Pipeline** и **MVC Pipeline**:

```text
Запрос → Middleware → Routing → Action Execution → Фильтры → Action Method → Фильтры → Результат → Middleware → Ответ
```

### **5 основных типов фильтров**

| Тип фильтра               | Когда выполняется?                 | Пример использования                   |
| ------------------------- | ---------------------------------- | -------------------------------------- |
| **Authorization Filters** | Первыми, до остальных фильтров     | `[Authorize]`, кастомные проверки прав |
| **Resource Filters**      | До и после остальных фильтров      | Кэширование, управление контекстом     |
| **Action Filters**        | До и после вызова метода действия  | Валидация модели, логирование          |
| **Exception Filters**     | Только при ошибках                 | Глобальная обработка исключений        |
| **Result Filters**        | До и после формирования результата | Изменение HTTP-ответа                  |

---

## **Стандартные фильтры в ASP.NET Core**

### **`[Authorize]` — проверка аутентификации**

**Пример:** Требовать авторизацию для контроллера:

```csharp
[Authorize]
public class AdminController : Controller
{
    [Authorize(Roles = "Admin")] // Только для админов
    public IActionResult Dashboard() => View();
}
```

**Реальный кейс**:  
Ограничение доступа к API только для аутентифицированных пользователей.

---

### **`[ValidateAntiForgeryToken]` — защита от CSRF**

**Пример:** Защита формы:

```html
<form asp-action="Update">
  @Html.AntiForgeryToken()
  <!-- Поля формы -->
</form>
```

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult Update(Product model) { ... }
```

**Реальный кейс**:  
Защита POST-запросов от подделки (например, в формах оплаты).

---

### **`[RequireHttps]` — принудительный HTTPS**

```csharp
[RequireHttps]
public class PaymentController : Controller { ... }
```

**Реальный кейс**:  
Редирект всех запросов на HTTPS в продакшене.

---

### **`[ServiceFilter]` и `[TypeFilter]` — инжектируемые фильтры**

**Пример:** Фильтр с зависимостями из DI:

```csharp
public class LoggingFilter : IActionFilter
{
    private readonly ILogger _logger;

    public LoggingFilter(ILogger<LoggingFilter> logger)
    {
        _logger = logger;
    }

    public void OnActionExecuting(ActionExecutingContext context)
    {
        _logger.LogInformation($"Запрос: {context.ActionDescriptor.DisplayName}");
    }

    public void OnActionExecuted(ActionExecutedContext context) { }
}
```

**Регистрация:**

```csharp
services.AddScoped<LoggingFilter>();
```

**Использование:**

```csharp
[ServiceFilter(typeof(LoggingFilter))]
public IActionResult Get() { ... }
```

**Реальный кейс**:  
Логирование всех запросов к API с указанием времени выполнения.

---

## **Создание кастомных фильтров**

### **Action Filter для валидации модели**

```csharp
public class ValidateModelAttribute : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        if (!context.ModelState.IsValid)
        {
            context.Result = new BadRequestObjectResult(context.ModelState);
        }
    }
}
```

**Применение:**

```csharp
[HttpPost]
[ValidateModel] // Автоматически возвращает 400 при ошибках
public IActionResult Create(ProductDto product) { ... }
```

---

### **Exception Filter для глобальной обработки ошибок**

```csharp
public class CustomExceptionFilter : IExceptionFilter
{
    public void OnException(ExceptionContext context)
    {
        if (context.Exception is NotFoundException)
        {
            context.Result = new NotFoundObjectResult(new { Error = "Ресурс не найден" });
            context.ExceptionHandled = true;
        }
    }
}
```

**Регистрация глобально:**

```csharp
services.AddControllers(options =>
{
    options.Filters.Add<CustomExceptionFilter>();
});
```

**Реальный кейс**:  
Единый формат ошибок для API (`{ "error": "..." }`).

---

### **Resource Filter для кэширования**

```csharp
public class CacheResourceFilter : IResourceFilter
{
    private static readonly Dictionary<string, object> _cache = new();

    public void OnResourceExecuting(ResourceExecutingContext context)
    {
        var key = context.HttpContext.Request.Path;
        if (_cache.TryGetValue(key, out var cachedResult))
        {
            context.Result = (IActionResult)cachedResult;
        }
    }

    public void OnResourceExecuted(ResourceExecutedContext context)
    {
        var key = context.HttpContext.Request.Path;
        _cache[key] = context.Result;
    }
}
```

**Применение:**

```csharp
[CacheResourceFilter]
public IActionResult GetProducts() { ... }
```

---

## **Порядок выполнения фильтров**

Фильтры выполняются в определённом порядке:

1. **Authorization Filters**
2. **Resource Filters** (до остальных)
3. **Action Filters** (до метода)
4. **Выполнение метода действия**
5. **Action Filters** (после метода)
6. **Exception Filters** (если было исключение)
7. **Result Filters**
8. **Resource Filters** (после всего)

**Пример:**

```csharp
[LogAction] // Action Filter
[CacheResource] // Resource Filter
[Authorize] // Authorization Filter
public IActionResult Get() { ... }
```

---

## **Фильтры vs Middleware: Когда что использовать?**

| Критерий             | Фильтры                        | Middleware                            |
| -------------------- | ------------------------------ | ------------------------------------- |
| **Уровень доступа**  | MVC-контекст (Action, Model)   | HTTP-контекст (Request, Response)     |
| **Время выполнения** | После маршрутизации            | До маршрутизации                      |
| **Использование**    | Логика, связанная с действиями | Сквозная инфраструктура (CORS, HTTPS) |

**Пример разделения:**

- **Middleware**: Сжатие ответов, CORS.
- **Фильтры**: Валидация модели, авторизация.

---

## **Лучшие практики**

1. **Избегайте бизнес-логики в фильтрах** — они должны решать инфраструктурные задачи.
2. **Не злоупотребляйте глобальными фильтрами** — это усложняет отладку.
3. **Тестируйте фильтры изолированно**:

   ```csharp
   var filter = new ValidateModelAttribute();
   var context = new ActionExecutingContext(...);
   filter.OnActionExecuting(context);
   Assert.IsType<BadRequestObjectResult>(context.Result);
   ```

4. **Для сложных сценариев** используйте **`IAsync...Filter`**:

   ```csharp
   public class AsyncActionFilter : IAsyncActionFilter
   {
       public async Task OnActionExecutionAsync(
           ActionExecutingContext context,
           ActionExecutionDelegate next)
       {
           // До вызова действия
           await next(); // Выполняет действие
           // После вызова действия
       }
   }
   ```

---

## **Реальные примеры использования**

### **Аудит действий пользователя**

```csharp
public class AuditFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        var user = context.HttpContext.User;
        var action = context.ActionDescriptor.DisplayName;
        AuditService.Log(user, action);
    }
}
```

### **API Versioning через заголовки**

```csharp
public class ApiVersionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        var version = context.HttpContext.Request.Headers["X-API-Version"];
        if (version != "1.0")
        {
            context.Result = new StatusCodeResult(StatusCodes.Status426UpgradeRequired);
        }
    }
}
```

### **Троттлинг запросов**

```csharp
public class RateLimitFilter : IAsyncActionFilter
{
    public async Task OnActionExecutionAsync(
        ActionExecutingContext context,
        ActionExecutionDelegate next)
    {
        var ip = context.HttpContext.Connection.RemoteIpAddress;
        if (RateLimiter.IsLimited(ip))
        {
            context.Result = new StatusCodeResult(429);
            return;
        }
        await next();
    }
}
```

---

## **Выводы**

- **Фильтры** — это мощный инструмент для перехвата запросов/ответов в ASP.NET Core.
- **Стандартные фильтры** (`[Authorize]`, `[ValidateAntiForgeryToken]`) закрывают базовые сценарии.
- **Кастомные фильтры** позволяют реализовать:
  - Единую обработку ошибок.
  - Кэширование.
  - Валидацию.
  - Логирование.
- **Правильный выбор типа фильтра** (Authorization vs Action vs Resource) критичен для производительности.

**Главное правило**:  
_"Фильтры должны делать одну вещь и делать её хорошо"_ (как и Middleware).
