# **Фильтры (Filters)**

**Цель**: Перехватывать и обрабатывать запросы/ответы на разных этапах выполнения.

## **Типы фильтров**

- **Authorization Filters**: Проверка прав доступа.
- **Action Filters**: Логирование, изменение входных/выходных данных.
- **Exception Filters**: Обработка ошибок.
- **Result Filters**: Изменение результатов (например, форматирование JSON).

## **Пример Action Filter для логирования**

```csharp
public class LogActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Выполняется ДО действия
        Console.WriteLine($"Начало выполнения: {context.ActionDescriptor.DisplayName}");
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // Выполняется ПОСЛЕ действия
        Console.WriteLine($"Завершено: {context.ActionDescriptor.DisplayName}");
    }
}
```

**Регистрация фильтра**:

- Глобально (для всех контроллеров) в `Program.cs`:

  ```csharp
  builder.Services.AddControllers(options =>
  {
      options.Filters.Add<LogActionFilter>();
  });
  ```

- Локально для конкретного действия или контроллера:

  ```csharp
  [ServiceFilter(typeof(LogActionFilter))]
  public class UsersController : ControllerBase { ... }
  ```
