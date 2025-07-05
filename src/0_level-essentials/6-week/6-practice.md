# **Практика на неделю**

1. **Добавление аутентификации в API**:

   - Возьмите API из предыдущих недель (например, блог).
   - Реализуйте регистрацию и вход через JWT.
   - Защитите методы создания/удаления постов: только аутентифицированные пользователи с ролью `Admin`.

2. **Мини-проект: Secure API с ролевым доступом**:
   - Сущности: `User` (с полями `Id`, `Username`, `PasswordHash`, `Role`).
   - Методы:
     - `POST /api/auth/register` → Регистрация (пароль хешируется).
     - `POST /api/auth/login` → Выдача JWT.
     - `GET /api/admin/users` → Только для роли `Admin`.

---

## **Пример кода для регистрации**

**Модель пользователя**:

```csharp
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string PasswordHash { get; set; }
    public string Role { get; set; }
}
```

**Контроллер аутентификации**:

```csharp
[HttpPost("register")]
public IActionResult Register([FromBody] RegisterRequest request)
{
    if (_dbContext.Users.Any(u => u.Username == request.Username))
        return Conflict("Пользователь уже существует");

    var user = new User
    {
        Username = request.Username,
        PasswordHash = BCrypt.Net.BCrypt.HashPassword(request.Password),
        Role = "User"
    };

    _dbContext.Users.Add(user);
    _dbContext.SaveChanges();

    return Ok();
}
```
