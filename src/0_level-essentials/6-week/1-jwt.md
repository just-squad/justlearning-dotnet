# **JWT (JSON Web Tokens)**

**Цель**: Реализовать аутентификацию через токены.

## **Что такое JWT?**

JWT — это «пропуск», который выдается пользователю после входа в систему. Токен содержит информацию о пользователе (например, ID, роли) и подписывается сервером.  
Формат токена: **Header.Payload.Signature**.

**Пример токена**:

```bash
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjMiLCJyb2xlIjoiQWRtaW4iLCJleHAiOjE2OTAwMDAwMDB9.4t5eRZ3cJ3z7Qw3v8X1Yy7dN6KkXm9S1Lw2vQjZ4fU4
```

---

## **Настройка JWT в ASP.NET Core**

1. Установите пакет:

   ```bash
   Microsoft.AspNetCore.Authentication.JwtBearer
   ```

2. Добавьте настройки в `appsettings.json`:

   ```json
   "Jwt": {
     "Key": "СекретныйКлючМинимум32Символа",
     "Issuer": "MyAPI",
     "Audience": "MyClient",
     "ExpiryMinutes": 60
   }
   ```

3. Настройте аутентификацию в `Program.cs`:

   ```csharp
   builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
       .AddJwtBearer(options =>
       {
           options.TokenValidationParameters = new TokenValidationParameters
           {
               ValidateIssuer = true,
               ValidateAudience = true,
               ValidateLifetime = true,
               ValidIssuer = builder.Configuration["Jwt:Issuer"],
               ValidAudience = builder.Configuration["Jwt:Audience"],
               IssuerSigningKey = new SymmetricSecurityKey(
                   Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
           };
       });
   ```

4. Включите аутентификацию и авторизацию:

   ```csharp
   app.UseAuthentication();
   app.UseAuthorization();
   ```

---

## **Генерация токена при входе**

Пример метода в контроллере `AuthController`:

```csharp
[HttpPost("login")]
public IActionResult Login([FromBody] LoginRequest request)
{
    // Проверка логина/пароля (здесь должна быть ваша логика)
    if (request.Username != "admin" || request.Password != "123")
        return Unauthorized();

    var claims = new[]
    {
        new Claim(ClaimTypes.Name, request.Username),
        new Claim(ClaimTypes.Role, "Admin")
    };

    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
    var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

    var token = new JwtSecurityToken(
        issuer: _config["Jwt:Issuer"],
        audience: _config["Jwt:Audience"],
        claims: claims,
        expires: DateTime.Now.AddMinutes(30),
        signingCredentials: creds);

    return Ok(new { Token = new JwtSecurityTokenHandler().WriteToken(token) });
}
```
