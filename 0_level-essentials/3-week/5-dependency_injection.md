# **Dependency Injection (DI)**

**Цель**: Научиться внедрять зависимости, чтобы код был тестируемым и гибким.

## **Что такое DI?**

DI — это паттерн, при котором зависимости (сервисы) передаются в класс извне, а не создаются внутри него.  
ASP.NET Core имеет встроенный контейнер DI.

**Пример сервиса**:

```csharp
public interface IPostService
{
    List<Post> GetAllPosts();
}

public class PostService : IPostService
{
    public List<Post> GetAllPosts() => new List<Post> { /* ... */ };
}
```

**Регистрация сервиса** (в `Program.cs`):

```csharp
builder.Services.AddScoped<IPostService, PostService>();
```

**Использование в контроллере**:

```csharp
private readonly IPostService _postService;

public PostsController(IPostService postService)
{
    _postService = postService; // DI передаст реализацию PostService
}

[HttpGet]
public IActionResult GetAllPosts()
{
    var posts = _postService.GetAllPosts();
    return Ok(posts);
}
```

---
