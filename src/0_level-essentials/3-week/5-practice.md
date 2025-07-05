# **Практика на неделю**

1. **Простой API для блога (CRUD без БД)**:

   - Создайте контроллер `PostsController` с методами:
     - `GET /api/posts` → Возвращает список статей.
     - `POST /api/posts` → Создает статью (сохраняйте в памяти в `List<Post>`).
     - `GET /api/posts/{id}` → Возвращает статью по ID.
     - `DELETE /api/posts/{id}` → Удаляет статью.
   - Используйте DI для сервиса, который управляет статьями.

2. **Мини-проект: API для управления пользователями**:
   - Поля пользователя: `Id`, `Name`, `Email`.
   - Методы:
     - Регистрация пользователя (`POST /api/users`).
     - Получение списка пользователей (`GET /api/users`).
     - Поиск по email (`GET /api/users?email=example@test.com`).

---

## **Пример кода для мини-проекта**

**Класс `User`**:

```csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}
```

**Контроллер**:

```csharp
[ApiController]
[Route("api/users")]
public class UsersController : ControllerBase
{
    private static List<User> _users = new List<User>();
    private static int _nextId = 1;

    [HttpGet]
    public IActionResult GetAllUsers() => Ok(_users);

    [HttpPost]
    public IActionResult CreateUser([FromBody] User user)
    {
        user.Id = _nextId++;
        _users.Add(user);
        return CreatedAtAction(nameof(GetUserById), new { id = user.Id }, user);
    }

    [HttpGet("{id}")]
    public IActionResult GetUserById(int id)
    {
        var user = _users.FirstOrDefault(u => u.Id == id);
        return user != null ? Ok(user) : NotFound();
    }
}
```
