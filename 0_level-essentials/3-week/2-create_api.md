# **Создание API: контроллеры, маршрутизация, Middleware**

**Цель**: Настроить простое API и обрабатывать запросы.

## **Создание проекта**

1. Установите [.NET SDK](https://dotnet.microsoft.com/download).
2. В терминале выполните:

   ```bash
   dotnet new webapi -o BlogApi
   cd BlogApi
   dotnet run
   ```

   Проект запустится на `https://localhost:5001`.

---

## **Контроллеры**

Контроллер — это класс, который обрабатывает HTTP-запросы.  
**Пример контроллера для статей**:

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/posts")] // Базовый маршрут
public class PostsController : ControllerBase
{
    // GET: api/posts
    [HttpGet]
    public IActionResult GetAllPosts()
    {
        var posts = new List<string> { "Post 1", "Post 2" };
        return Ok(posts); // Возвращает HTTP 200 с данными
    }

    // POST: api/posts
    [HttpPost]
    public IActionResult CreatePost([FromBody] string postContent)
    {
        // Здесь бы сохраняли пост в БД, но пока просто возвращаем результат
        return Ok($"Создан пост: {postContent}");
    }
}
```

**Как это работает**:

- `[ApiController]` включает автоматическую валидацию данных.
- `[Route("api/posts")]` задает базовый URL для всех методов контроллера.
- `[HttpGet]` и `[HttpPost]` определяют HTTP-методы.

---

## **Маршрутизация**

Маршрутизация определяет, какой метод контроллера будет вызван.  
Пример с параметром в URL:

```csharp
[HttpGet("{id}")] // GET api/posts/5
public IActionResult GetPostById(int id)
{
    // Предположим, есть метод для поиска поста по ID
    var post = FindPost(id);
    if (post == null)
    {
        return NotFound(); // HTTP 404
    }
    return Ok(post);
}
```

---

## **Middleware**

Middleware — это компоненты, которые обрабатывают запросы и ответы в порядке их подключения.  
**Пример Middleware для логирования**:

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine($"Запрос: {context.Request.Path}");
    await next(); // Передаем запрос следующему Middleware
    Console.WriteLine($"Ответ: {context.Response.StatusCode}");
});
```

**Стандартные Middleware в ASP.NET Core**:

- `app.UseHttpsRedirection()`: Перенаправляет HTTP на HTTPS.
- `app.UseRouting()`: Включает маршрутизацию.
- `app.UseAuthorization()`: Проверяет права доступа.

---
